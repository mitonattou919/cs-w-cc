# CLAUDE.md

## Project Overview

Browser-based VS Code (code-server) + Claude Code development environment running in Docker.
Access the IDE at `http://localhost:8080` and use Claude Code directly from the integrated terminal.

Claude Code credentials are shared from the host (`~/.claude` → `/root/.claude`) so no re-authentication is needed inside the container.

## Stack

| Component     | Version / Notes                          |
|---------------|------------------------------------------|
| Base image    | ubuntu:22.04                             |
| Node.js       | 20.x (via NodeSource)                    |
| Python        | 3.12 (via deadsnakes PPA)                |
| uv            | latest (Python package/venv manager)     |
| gh CLI        | latest (GitHub CLI)                      |
| code-server   | latest                                   |
| Claude Code   | latest (`@anthropic-ai/claude-code`)     |

## Common Commands

### Container lifecycle

```bash
# Build image and start (foreground)
docker compose up --build

# Build image and start (background)
docker compose up --build -d

# Stop (keep volumes)
docker compose down

# Stop and remove volumes (extensions, settings)
docker compose down -v
```

### Python / uv

```bash
# Create virtual environment
uv venv
source .venv/bin/activate

# Install packages
uv pip install <package>
```

### GitHub CLI

```bash
# Authenticate (run once on host)
gh auth login

# Create a pull request
gh pr create
```

## Coding Rules

- **Comments must be written in English.**
- Keep Dockerfile layers minimal; clean up `apt` caches in the same `RUN` step.
- Do not commit secrets, credentials, or `.env` files.
- Prefer `uv` over `pip` for Python dependency management inside the container.
