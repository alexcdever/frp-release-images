# frp-release-images

用于跟踪上游 `fatedier/frp` 的版本发布，并将 `frps` / `frpc` 镜像发布到 GitHub Container Registry (GHCR)。

## 仓库说明

本仓库会自动完成以下流程：

- 定时检测上游 frp 最新版本
- 将跟踪版本写入 `FRP_VERSION`
- 使用上游 `fatedier/frp` 的 Dockerfile 构建多架构镜像（`linux/amd64`、`linux/arm64`）
- 将镜像推送到 GHCR

已发布镜像：

- `ghcr.io/alexcdever/frps:<version>`
- `ghcr.io/alexcdever/frps:latest`
- `ghcr.io/alexcdever/frpc:<version>`
- `ghcr.io/alexcdever/frpc:latest`

## 发布工作流

GitHub Actions 工作流：`Track frp release and publish images`

- 定时触发：每 6 小时一次
- 手动触发：在 Actions 页面运行，可选输入 `version`（例如 `v0.65.0`）

手动输入 `version` 的行为：

- 留空：自动检测上游最新 release tag
- 填写 `version`（例如 `v0.67.0`）：强制构建该指定版本

建议手动填写 `version` 的场景：

- 需要重建某个已有版本（例如 CI 或镜像异常后）
- 需要补发历史版本
- 需要验证某个特定版本，而不是当前 latest

CI 构建来源：

- 工作流会 checkout `fatedier/frp` 对应 tag
- 使用上游 Dockerfile：
  - `dockerfiles/Dockerfile-for-frps`
  - `dockerfiles/Dockerfile-for-frpc`

## 容器默认启动命令

- `frps` 默认命令：`frps -c /etc/frp/frps.toml`
- `frpc` 默认命令：`frpc -c /etc/frp/frpc.toml`

## Docker Compose（分离部署）

`frps`（服务端）和 `frpc`（客户端）通常部署在不同主机。本仓库提供两个独立的 compose 文件：

- `docker-compose.frps.yml`
- `docker-compose.frpc.yml`

### 1）部署 frps（服务端主机）

1. 修改 `config/frps.toml`：
   - 设置强密码 `webServer.password`
   - 设置强口令 `auth.token`
2. 启动服务：

```bash
docker compose -f docker-compose.frps.yml up -d
```

3. 查看日志：

```bash
docker compose -f docker-compose.frps.yml logs -f
```

4. 停止服务：

```bash
docker compose -f docker-compose.frps.yml down
```

### 2）部署 frpc（客户端主机）

1. 修改 `config/frpc.toml`：
   - 将 `serverAddr` 设置为 frps 的公网 IP 或域名
   - 将 `auth.token` 设置为与 frps 一致
2. 启动服务：

```bash
docker compose -f docker-compose.frpc.yml up -d
```

3. 查看日志：

```bash
docker compose -f docker-compose.frpc.yml logs -f
```

4. 停止服务：

```bash
docker compose -f docker-compose.frpc.yml down
```

## 启动前检查

启动前建议先校验 compose 文件：

```bash
docker compose -f docker-compose.frps.yml config
docker compose -f docker-compose.frpc.yml config
```
