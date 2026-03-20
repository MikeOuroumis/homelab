# Homelab

Self-hosted services — Docker Compose configs for easy deployment across devices.

## Services

| Service | Description | Port |
|---------|-------------|------|
| [OpenClaw](openclaw/) | Autonomous AI agent | 3100 |
| [Linux Desktop](linux-desktop/) | Ubuntu desktop in your browser (Kasm) | 6901 |

## Quick Start

```bash
cd <service>/
cp .env.example .env   # edit with your API keys
docker compose up -d
```

## Requirements

- Docker Desktop or Docker Engine + Docker Compose v2
- At least 2 GB RAM per service
