# Monitoring Stack — 135node2

Docker Compose monitoring stack for host `135node2`, combining metrics, alerts, dashboards, and centralized Docker logs.

## Components

- Prometheus — metrics collection and alert evaluation
- Alertmanager — Telegram alert delivery
- Grafana — dashboards and log exploration
- Node Exporter — host metrics
- cAdvisor — Docker container metrics
- Loki — log storage with 30-day retention
- Grafana Alloy — Docker log collection and forwarding to Loki

## Included dashboards

- Infrastructure Overview
- Docker Containers & Logs
- Docker Logs Explorer
- Node Exporter Full

## Setup

1. Copy the environment template:

   ```bash
   cp .env.example .env
   ```

2. Update both bind addresses and generate a strong Grafana password:

   ```bash
   openssl rand -base64 32
   ```

3. Create the Telegram bot-token secret:

   ```bash
   install -d -m 700 secrets
   printf '%s' 'YOUR_TELEGRAM_BOT_TOKEN' > secrets/telegram_bot_token
   chmod 600 secrets/telegram_bot_token
   ```

4. Validate and start:

   ```bash
   docker compose config --quiet
   docker compose up -d
   docker compose ps
   ```

## Access

Default ports:

| Service | Port |
|---|---:|
| Grafana | 3000 |
| Prometheus | 9090 |
| Alertmanager | 9093 |
| Loki | 3100 |

Services are intentionally bound only to the configured LAN and NetBird IPs, not to every host interface.

## Alert rules

- Node Exporter unavailable for more than 1 minute
- CPU usage above 85% for 5 minutes
- Memory usage above 90% for 5 minutes
- Disk usage above 90% for 10 minutes

Telegram notifications include both firing and resolved states.

## Security

- `.env` and `secrets/` are excluded from Git.
- Never commit the Grafana password or Telegram bot token.
- Keep Grafana and monitoring endpoints private to LAN/NetBird unless a secured reverse proxy is added.
