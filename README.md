# unity-mcp-server-sync

[English](#english) | [中文](#中文)

---

<a name="english"></a>
## English

Automated GitHub Actions workflow repository to build and publish the `CoplayDev/unity-mcp` Server Docker image to GitHub Container Registry (GHCR).

### 🚀 Features
* **Daily Auto-Sync**: Automatically runs daily (at 00:00 UTC) to fetch the latest release of [CoplayDev/unity-mcp](https://github.com/CoplayDev/unity-mcp) and build it.
* **Smart Rebuild Skipping**: Automatically queries GHCR before building. If the target version image already exists, it skips the build to save Actions minutes.
* **Manual Control**: Supports manual trigger (`workflow_dispatch`) with optional `version` specification and a `force` rebuild option.
* **Node.js 24 Ready**: Optimized with `FORCE_JAVASCRIPT_ACTIONS_TO_NODE24` to ensure long-term stability and compatibility with GitHub runner updates.
* **Dual Tags**: Pushes both the version tag (e.g., `v9.6.8`) and the `latest` tag representing the latest built release.

---

### 📦 How to Use

#### 1. Docker CLI
Pull the image directly from GHCR:
```bash
# Pull the latest release
docker pull ghcr.io/mmm1h/unity-mcp-server:latest

# Or pull a specific version for stability
docker pull ghcr.io/mmm1h/unity-mcp-server:v9.6.8
```

Run the container using stdio or HTTP transport:
```bash
# Run with stdio transport (Compatible with Docker MCP Gateway)
docker run -i --rm ghcr.io/mmm1h/unity-mcp-server:latest

# Run with HTTP transport (Expose port 8080)
docker run -d -p 8080:8080 --name unity-mcp-server \
  -e TRANSPORT=http \
  -e HTTP_HOST=0.0.0.0 \
  -e HTTP_PORT=8080 \
  -e UNITY_EDITOR_URL=http://<NAS_IP_OR_HOST>:8080 \
  ghcr.io/mmm1h/unity-mcp-server:latest
```

#### 2. Docker Compose
Add this service to your `docker-compose.yml` on your NAS or server:
```yaml
version: '3.8'

services:
  unity-mcp-server:
    image: ghcr.io/mmm1h/unity-mcp-server:latest # Use specific version like :v9.6.8 for production stability
    container_name: unity-mcp-server
    restart: always
    ports:
      - "8080:8080"
    environment:
      - TRANSPORT=http
      - HTTP_HOST=0.0.0.0
      - HTTP_PORT=8080
      - UNITY_EDITOR_URL=http://<YOUR_UNITY_EDITOR_IP>:8080
```

---

### 🛡️ Security Best Practices
* **No Secrets in Images**: The built Docker image contains only the clean `mcp-for-unity` server.
* **External Runtime Configuration**: Pass all sensitive keys, ports, and external connection URLs via environment variables (`docker-compose.yml` or `.env` files) on your NAS.

---
---

<a name="中文"></a>
## 中文 (Chinese)

本仓库提供 GitHub Actions 工作流，用于自动获取并构建上游 [CoplayDev/unity-mcp](https://github.com/CoplayDev/unity-mcp) 的 Server 镜像，并将其发布至 GitHub Container Registry (GHCR)。

### 🚀 核心特性
* **每日自动同步**：每日（UTC 00:00）自动运行，检查上游最新 Release，如有更新则自动构建。
* **智能跳过机制**：构建前自动检测 GHCR 中是否已存在对应版本的镜像。如果已存在，则自动跳过构建以节省 Actions 额度。
* **手动运维控制**：支持手动触发工作流（`workflow_dispatch`），支持指定 `version` 以及勾选 `force` 强制覆盖重建。
* **未来无感运行**：全局开启 `FORCE_JAVASCRIPT_ACTIONS_TO_NODE24` 环境变量，完全适配并平滑过渡到 Node.js 24 动作环境。
* **双标签维护**：每次成功构建均会同时推送具体版本号 tag（例如 `v9.6.8`）与 `latest`（指代我们当前自动构建到的最新上游 release 镜像）。

---

### 📦 快速使用指南

#### 1. 命令行 Docker CLI 拉取与运行
直接从 GHCR 中拉取镜像：
```bash
# 拉取最新版本
docker pull ghcr.io/mmm1h/unity-mcp-server:latest

# 拉取特定版本（推荐生产环境使用，更稳定）
docker pull ghcr.io/mmm1h/unity-mcp-server:v9.6.8
```

运行容器（以 HTTP 传输为例）：
```bash
docker run -d -p 8080:8080 --name unity-mcp-server \
  -e TRANSPORT=http \
  -e HTTP_HOST=0.0.0.0 \
  -e HTTP_PORT=8080 \
  -e UNITY_EDITOR_URL=http://<您的NAS或宿主机IP>:8080 \
  ghcr.io/mmm1h/unity-mcp-server:latest
```

#### 2. Docker Compose 配置
在您的 NAS 或服务器上创建一个 `docker-compose.yml` 配置文件：
```yaml
version: '3.8'

services:
  unity-mcp-server:
    image: ghcr.io/mmm1h/unity-mcp-server:latest # 生产稳定环境建议更换为具体版本号，例如 :v9.6.8
    container_name: unity-mcp-server
    restart: always
    ports:
      - "8080:8080"
    environment:
      - TRANSPORT=http
      - HTTP_HOST=0.0.0.0
      - HTTP_PORT=8080
      - UNITY_EDITOR_URL=http://<您的UNITY编辑器运行IP>:8080
```

---

### 🛡️ 安全提示
* **镜像完全无害**：构建出的镜像仅负责打包上游服务端运行环境，绝不包含任何敏感密钥、外部连接或凭证。
* **运行时配置**：请务必在 NAS 的部署环境（如 `docker-compose` 或环境变量配置）中提供具体的 Token、API Key 和外部连接信息。
