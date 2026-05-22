# unity-mcp-img (中文版)

[English Version](./README.md)

---

本仓库提供 GitHub Actions 工作流，用于自动获取、构建上游官方项目
[CoplayDev/unity-mcp](https://github.com/CoplayDev/unity-mcp) 的 Server 端镜像，
并将其发布至 GitHub Container Registry (GHCR)。

### 🌟 关于上游官方项目
[CoplayDev/unity-mcp](https://github.com/CoplayDev/unity-mcp) (MCP for Unity)
是一个强大的MCP工具。它基于 Model Context Protocol (MCP) 协议，将 AI 助手
（如 Claude Desktop、Cursor、Claude Code、VS Code 等）直接与您的 Unity
编辑器连接起来。这使得大语言模型（LLM）获得了操作 Unity 资产、管理场景、
编辑脚本以及自动化执行各类开发任务的能力。

本仓库 (`unity-mcp-img`) 作为其配套的镜像自动同步构建库，将官方 Server 端的
HTTP/stdio 运行环境打包为轻量且高度优化的 Docker 容器镜像，极大地方便了在
NAS、本地服务器或云端等各类环境中的一键部署与运行。

### 🚀 核心特性
* **每日自动同步**：每日（UTC 00:00）自动运行，检查上游最新 Release，如有
  更新则自动构建。
* **智能跳过机制**：构建前自动检测 GHCR 中是否已存在对应版本的镜像。如果已
  存在，则自动跳过构建以节省 Actions 额度。
* **手动运维控制**：支持手动触发工作流（`workflow_dispatch`），支持指定
  `version` 以及勾选 `force` 强制覆盖重建。
* **未来无感运行**：全局开启 `FORCE_JAVASCRIPT_ACTIONS_TO_NODE24`
  环境变量，完全适配并平滑过渡到 Node.js 24 动作环境。
* **双标签维护**：每次成功构建均会同时推送具体版本号 tag（例如 `v9.6.8`）与
  `latest`（指代我们当前自动构建到的最新上游 release 镜像）。

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

运行容器：
```bash
# 默认 stdio 传输模式（兼容 Docker MCP Gateway 模式）
docker run -i --rm ghcr.io/mmm1h/unity-mcp-server:latest

# HTTP 传输模式（映射 8080 端口）
docker run -d -p 8080:8080 --name unity-mcp-server \
  ghcr.io/mmm1h/unity-mcp-server:latest \
  --transport http --http-host 0.0.0.0 --http-port 8080
```

#### 2. Docker Compose 配置
在您的 NAS 或服务器上创建一个 `docker-compose.yml` 配置文件：
```yaml
version: '3.8'

services:
  unity-mcp-server:
    # 生产稳定环境建议更换为具体版本号，例如 :v9.6.8
    image: ghcr.io/mmm1h/unity-mcp-server:latest
    container_name: unity-mcp-server
    restart: always
    ports:
      - "8080:8080"
    command: ["--transport", "http", "--http-host", "0.0.0.0", "--http-port", "8080"]
```

---

### 🛡️ 配置与环境变量
只要 Unity 编辑器 and 本服务同时运行，服务会自动与其建立连接。您可以通过以下可
选的环境变量或 CLI 命令行参数对其进行配置：

* **环境变量**：
  * `LOG_LEVEL=DEBUG` - 开启详细的调试日志（默认：`INFO`）。
  * `DISABLE_TELEMETRY=true` - 禁用匿名遥测与使用数据分析。

* **CLI 命令行参数**（在 docker 容器启动命令的末尾传入）：
  * `--transport {stdio,http}` - 传输协议类型（默认：`stdio`）。
  * `--http-host HOST` - 覆盖 HTTP 绑定监听地址（例如 `0.0.0.0`）。
  * `--http-port PORT` - 覆盖 HTTP 绑定监听端口（例如 `8080`）。
  * `--http-remote-hosted` - 将 HTTP 传输视为远程托管服务（此模式下需要
    外部 API Key 验证服务）。
