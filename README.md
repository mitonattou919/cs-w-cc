# Claude Code Server

A Docker-based development environment running browser-based VS Code (code-server) with Claude Code.

## Components

| Component     | Version / Notes                      |
|---------------|--------------------------------------|
| Base image    | ubuntu:22.04                         |
| code-server   | latest                               |
| Claude Code   | latest (`@anthropic-ai/claude-code`) |
| Node.js       | 20.x                                 |
| Python        | 3.12                                 |
| pip           | latest                               |
| uv            | latest                               |

---

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed
- [Docker Compose](https://docs.docker.com/compose/install/) available (bundled with Docker Desktop)
- A claude.ai Pro account with browser authentication completed on the host (`claude login`)

---

## Setup

### 1. Create the workspace directory

```bash
mkdir -p workspace
```

### 2. Authenticate Claude Code on the host

Run the following in your **host terminal** to complete browser authentication:

```bash
claude login
```

Credentials are saved to `~/.claude` and mounted into the container as `/root/.claude`.

### 3. Build and start

```bash
docker compose up --build
```

> The first build may take a few minutes due to Python 3.12 installation.

To run in the background:

```bash
docker compose up --build -d
```

### 4. Open in browser

```
http://localhost:8080
```

---

## Using Claude Code

Open a terminal in code-server (`` Ctrl+` ``) and run:

```bash
# Interactive mode
claude

# Ask about a specific file
claude "Explain this code" --file main.py

# One-shot execution
claude -p "Write Hello World in Python"
```

As long as `claude login` was completed on the host, the container requires no additional authentication.

---

## Python / uv

```bash
# Check Python version
python --version   # Python 3.12.x

# Create a virtual environment with uv
uv venv
source .venv/bin/activate

# Install packages with uv
uv pip install requests numpy

# Install packages with pip
pip install pandas
```

---

## Stopping the Container

```bash
# Stop (data is preserved)
docker compose down

# Stop and remove volumes (extensions, settings)
docker compose down -v
```

---

## File Structure

```
.
├── Dockerfile            # Image definition
├── docker-compose.yaml   # Service definition
├── README.md             # This file
└── workspace/            # Working files (mounted into the container)
```

---

## Security

- code-server runs with `--auth none` (no password). **For local use only.**
- To expose it externally, add `PASSWORD=your_password` to the `environment` section in `docker-compose.yaml` and remove `--auth none` from `CMD` in the `Dockerfile`.

---

## Troubleshooting

| Symptom | Solution |
|---------|----------|
| Cannot connect to `http://localhost:8080` | Run `docker compose ps` to verify the container is running |
| Claude Code authentication error | Run `claude login` on the host and confirm credentials exist in `~/.claude` |
| Python 3.12 not found | If the deadsnakes PPA is unavailable, switch the Dockerfile to a pyenv-based setup |
| Port 8080 already in use | Change `"8080:8080"` to e.g. `"8081:8080"` in `docker-compose.yaml` |
