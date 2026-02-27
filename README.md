# frp-release-images

Track upstream `fatedier/frp` releases and publish `frps` / `frpc` container images to GitHub Container Registry (GHCR).

## What this repository does

- Polls the latest frp release on a schedule
- Updates `FRP_VERSION` when a new upstream version appears
- Builds multi-arch images for `linux/amd64` and `linux/arm64`
- Publishes images to GHCR:
  - `ghcr.io/<owner>/frps:<version>`
  - `ghcr.io/<owner>/frpc:<version>`
  - `ghcr.io/<owner>/frps:latest`
  - `ghcr.io/<owner>/frpc:latest`

## Manual release build

Run the workflow `Track frp release and publish images` from Actions and optionally pass `version` (for example `v0.65.0`).

## Default runtime commands

- `frps` image default command: `frps -c /etc/frp/frps.toml`
- `frpc` image default command: `frpc -c /etc/frp/frpc.toml`
