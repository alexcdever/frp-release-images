# frp-release-images

Track upstream `fatedier/frp` releases and publish `frps` / `frpc` images to GitHub Container Registry (GHCR).

## Overview

This repository automates the full release flow:

- Detect new upstream frp releases on schedule
- Update tracked version in `FRP_VERSION`
- Build multi-arch images (`linux/amd64`, `linux/arm64`) using upstream `fatedier/frp` Dockerfiles
- Publish images to GHCR

Published images:

- `ghcr.io/alexcdever/frps:<version>`
- `ghcr.io/alexcdever/frps:latest`
- `ghcr.io/alexcdever/frpc:<version>`
- `ghcr.io/alexcdever/frpc:latest`

## Release workflow

GitHub Actions workflow: `Track frp release and publish images`

- Scheduled run: every 6 hours
- Manual run: from Actions page, optional input `version` (example: `v0.65.0`)

Manual input `version` behavior:

- Leave empty: auto-detect upstream latest frp release tag
- Set `version` (for example `v0.67.0`): force build that exact tag

When to set `version` manually:

- Rebuild an existing tag after CI/image issues
- Backfill or republish a historical frp release
- Validate a specific release without depending on latest

Build source in CI:

- Workflow checks out `fatedier/frp` at the target tag
- Uses upstream Dockerfiles:
  - `dockerfiles/Dockerfile-for-frps`
  - `dockerfiles/Dockerfile-for-frpc`

## Runtime defaults

- `frps` default command: `frps -c /etc/frp/frps.toml`
- `frpc` default command: `frpc -c /etc/frp/frpc.toml`

## Docker Compose (split deployment)

`frps` (server) and `frpc` (client) are typically deployed on different hosts. This repo provides two separate compose files:

- `docker-compose.frps.yml`
- `docker-compose.frpc.yml`

### 1) Deploy frps (server host)

1. Update `config/frps.toml`:
   - Set a strong `webServer.password`
   - Set a strong `auth.token`
2. Start service:

```bash
docker compose -f docker-compose.frps.yml up -d
```

3. Check logs:

```bash
docker compose -f docker-compose.frps.yml logs -f
```

4. Stop service:

```bash
docker compose -f docker-compose.frps.yml down
```

### 2) Deploy frpc (client host)

1. Update `config/frpc.toml`:
   - Set `serverAddr` to your frps public IP/domain
   - Set `auth.token` to the same value used by frps
2. Start service:

```bash
docker compose -f docker-compose.frpc.yml up -d
```

3. Check logs:

```bash
docker compose -f docker-compose.frpc.yml logs -f
```

4. Stop service:

```bash
docker compose -f docker-compose.frpc.yml down
```

## Quick validation

Before startup, validate compose files:

```bash
docker compose -f docker-compose.frps.yml config
docker compose -f docker-compose.frpc.yml config
```
