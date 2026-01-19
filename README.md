# Multi-Layer Configuration Architecture

> **Summary**: This document describes a multi-layer configuration system with priority order (lowest to highest): (1) In-code defaults, (2) Configuration file, (3) Lowercase-dotted environment variables, (4) Uppercase-underscored environment variables, (5) CLI arguments.

## Layer 1 : In-code Defaults

```python
import pydantic as pdt

class Config(pdt.BaseModel):
    class Backend(pdt.BaseModel):
        host: str = "localhost"
        port: int = 3000
        sse_heartbeat_interval: int = 30
        notifications_max_count: int = 100

    backend: Backend = pdt.Field(default_factory=Backend)

    class Cli(pdt.BaseModel):
        host: str = "localhost"
        port: int = 8080
        proxy: str = ""
        verbose: bool = False

    cli: Cli = pdt.Field(default_factory=Cli)
```


## Layer 2 : Configuration File

```json
{
  "backend": {
    "host": "localhost",
    "port": 3000,
    "sse_heartbeat_interval": 30,
    "notifications_max_count": 100
  },
  "cli": {
    "host": "localhost",
    "port": 8080,
    "proxy": "",
    "verbose": false
  }
}
```

## Layer 3 : Lowercase-Dotted Environment Variables

```bash
env \
  app_name.backend.host="localhost" \
  app_name.backend.port="3000" \
  app_name.backend.sse_heartbeat_interval="30" \
  app_name.backend.notifications_max_count="100" \
  app_name.cli.host="localhost" \
  app_name.cli.port="8080" \
  app_name.cli.proxy="" \
  app_name.cli.verbose="false" \
  path/to/app_name.exe
```

```yaml
# file_name: docker-compose.yaml
services:
  app:
    image: your_app_image
    environment:
      app_name.backend.host: localhost
      app_name.backend.port: 3000
      app_name.backend.sse_heartbeat_interval: 30
      app_name.backend.notifications_max_count: 100
      app_name.cli.host: localhost
      app_name.cli.port: 8080
      app_name.cli.proxy:
      app_name.cli.verbose: false
    command: path/to/app_name.exe
```

## Layer 4 : Uppercase-Underscored Environment Variables

```bash
APP_NAME_BACKEND_HOST="localhost" \
APP_NAME_BACKEND_PORT="3000" \
APP_NAME_BACKEND_SSE_HEARTBEAT_INTERVAL="30" \
APP_NAME_BACKEND_NOTIFICATIONS_MAX_COUNT="100" \
APP_NAME_CLI_HOST="localhost" \
APP_NAME_CLI_PORT="8080" \
APP_NAME_CLI_PROXY="" \
APP_NAME_CLI_VERBOSE="false" \
path/to/app_name.exe
```

```yaml
# file_name: docker-compose.yaml
version: '3.8'
services:
  app:
    image: your_app_image
    environment:
      APP_NAME_BACKEND_HOST: localhost
      APP_NAME_BACKEND_PORT: 3000
      APP_NAME_BACKEND_SSE_HEARTBEAT_INTERVAL: 30
      APP_NAME_BACKEND_NOTIFICATIONS_MAX_COUNT: 100
      APP_NAME_CLI_HOST: localhost
      APP_NAME_CLI_PORT: 8080
      APP_NAME_CLI_PROXY: ""
      APP_NAME_CLI_VERBOSE: false
```

## Layer 5 : CLI Args

```bash
path/to/app_name.exe \
  --backend.host localhost \
  --backend.port 3000 \
  --backend.sse_heartbeat_interval 30 \
  --backend.notifications_max_count 100 \
  --cli.host localhost \
  --cli.port 8080 \
  --cli.proxy "" \
  --cli.verbose false
```