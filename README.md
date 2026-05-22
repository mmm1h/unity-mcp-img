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
  ghcr.io/mmm1h/unity-mcp-server:latest \
  --transport http --http-host 0.0.0.0 --http-port 8080
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
    command: ["--transport", "http", "--http-host", "0.0.0.0", "--http-port", "8080"]
```

---

### 🛡️ Configuration & Environment Variables
The server connects to the Unity Editor automatically when both are running. You can configure it via optional environment variables or CLI flags:

* **Environment Variables**:
  * `LOG_LEVEL=DEBUG` - Enable detailed logging (default: `INFO`).
  * `DISABLE_TELEMETRY=true` - Opt out of anonymous usage analytics.

* **CLI Flags** (passed as docker command arguments):
  * `--transport {stdio,http}` - Transport protocol (default: `stdio`).
  * `--http-host HOST` - Override HTTP bind host (e.g. `0.0.0.0`).
  * `--http-port PORT` - Override HTTP bind port (e.g. `8080`).
  * `--http-remote-hosted` - Treat HTTP transport as remotely hosted (requires external API key validation).
