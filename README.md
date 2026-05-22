# unity-mcp-server-sync

[中文版 (Chinese Version)](./README_zh.md)

---

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
