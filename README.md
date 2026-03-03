# Claude Code Server

ブラウザベースの VSCode（code-server）と Claude Code が動作する Docker 開発環境です。

## 構成

| コンポーネント | バージョン |
|---|---|
| ベースイメージ | ubuntu:22.04 |
| code-server | 最新 |
| Claude Code | 最新（`@anthropic-ai/claude-code`） |
| Node.js | 20.x |
| Python | 3.12 |
| pip | 最新 |
| uv | 最新 |

---

## 前提条件

- [Docker](https://docs.docker.com/get-docker/) がインストール済みであること
- [Docker Compose](https://docs.docker.com/compose/install/) が使用可能であること（Docker Desktop には同梱）
- claude.ai の Pro アカウント（ホスト側で `claude login` によるブラウザ認証済み）

---

## セットアップ手順

### 1. ワークスペースディレクトリを作成

```bash
mkdir -p workspace
```

### 2. Claude Code のブラウザ認証（ホスト側で実施）

**ホストのターミナル**で以下を実行し、ブラウザ認証を完了させます。

```bash
claude login
```

認証情報は `~/.claude` に保存され、コンテナ起動時に `/root/.claude` としてマウントされます。

### 3. イメージをビルドして起動

```bash
docker compose up --build
```

> 初回ビルドは Python 3.12 などのインストールがあるため、数分かかります。

バックグラウンドで起動する場合：

```bash
docker compose up --build -d
```

### 4. ブラウザでアクセス

```
http://localhost:8080
```

---

## Claude Code の使用方法

code-server のターミナル（ `Ctrl+` ` ` ` ）を開き、以下を実行します。

```bash
# 対話モードで起動
claude

# ファイルを指定して質問
claude "このコードを説明してください" --file main.py

# ワンショット実行
claude -p "Python で Hello World を書いてください"
```

ホスト側で `claude login` 済みであれば、コンテナ内でも認証なしですぐに利用できます。

---

## Python / uv の使用方法

```bash
# Python バージョン確認
python --version   # Python 3.12.x

# uv で仮想環境を作成
uv venv
source .venv/bin/activate

# uv でパッケージをインストール
uv pip install requests numpy

# pip でインストール（従来の方法）
pip install pandas
```

---

## コンテナの停止・削除

```bash
# 停止（データは保持）
docker compose down

# 停止＋ボリューム（拡張機能・設定）も削除
docker compose down -v
```

---

## ファイル構成

```
.
├── Dockerfile            # イメージ定義
├── docker-compose.yaml   # サービス定義
├── README.md             # このファイル
└── workspace/            # 作業ファイルの置き場（コンテナにマウント）
```

---

## セキュリティについて

- code-server は `--auth none`（パスワードなし）で起動しています。**ローカル環境専用**です。
- 外部に公開する場合はパスワードを設定してください。
  `docker-compose.yaml` の `environment` に `PASSWORD=your_password` を追加し、
  `Dockerfile` の `CMD` から `--auth none` を削除します。

---

## トラブルシューティング

| 症状 | 対処 |
|---|---|
| `http://localhost:8080` に接続できない | `docker compose ps` でコンテナが起動しているか確認 |
| Claude Code が認証エラーになる | ホスト側で `claude login` を実行し、`~/.claude` に認証情報があるか確認 |
| Python 3.12 が見つからない | deadsnakes PPA が更新されていない場合、Dockerfile 内を pyenv ベースに変更してください |
| ポート 8080 が使用中 | `docker-compose.yaml` の `"8080:8080"` を `"8081:8080"` などに変更 |
