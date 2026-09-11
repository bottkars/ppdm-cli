# PPDM Web Monitor - Docker Deployment Guide

## 🐳 Complete Docker Setup

This guide provides a complete Docker and Docker Compose setup for running the PPDM Web Monitor and Tool Gateway.

## 📋 Prerequisites

- Docker 20.10+ or Podman 3.0+
- Docker Compose 2.0+ (if using compose)
- PPDM server accessible from the Docker host

## 🚀 Quick Start

### 1. Environment Setup

```bash
# Copy environment template
cp .env.example .env

# Edit the environment file
nano .env
```

Edit `.env` with your PPDM server details:
```bash
PPDM_SERVER=your-ppdm-server.com
PPDM_USERNAME=admin
PPDM_PASSWORD=your-password
```

### 2. Docker Compose Deployment

```bash
# Build and start the service
docker-compose up -d

# Check logs
docker-compose logs -f ppdm-web-monitor

# Stop the service
docker-compose down
```

### 3. Tool Gateway and Demo Application

The PPDM CLI container now includes a tool gateway REST API and demo web application:

#### Tool Gateway API
- **Port**: 9000 (internal), configurable via environment
- **Health Check**: `GET /health`
- **API Documentation**: `GET /swagger`
- **Tools List**: `GET /tools`
- **Server Discovery**: `GET /config/servers`

#### Demo Application
- **Port**: 8081 (default)
- **Access**: http://localhost:8081
- **Features**:
  - Dynamic PPDM server selection
  - Tool execution interface
  - Real-time tool call debugging
  - API key authentication

#### Docker Compose with Tool Gateway

```yaml
version: '3.8'

services:
  ppdm-cli:
    build: .
    container_name: ppdm-cli
    ports:
      - "9000:9000"  # Tool Gateway API
      - "8081:8081"  # Demo Application
    volumes:
      - ~/.ppdm/config.json:/home/ppdm/.ppdm/config.json:ro
    environment:
      - PPDM_CONFIG_FILE=/home/ppdm/.ppdm/config.json
      - TOOL_GATEWAY_PORT=9000
      - DEMO_APP_PORT=8081
    restart: unless-stopped
```

### 4. Direct Docker Run

```bash
# Build the image
docker build -t ppdm-cli .

# Run with environment file
docker run -d --name ppdm-web-monitor \
    --env-file .env \
    -p 8080:8080 \
    ppdm-cli

# Check logs
docker logs -f ppdm-web-monitor
```

## 🔧 Configuration

### Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `PPDM_SERVER` | localhost | PPDM server hostname |
| `PPDM_USERNAME` | admin | PPDM username |
| `PPDM_PASSWORD` | password | PPDM password |
| `PPDM_PORT` | 443 | PPDM server port |
| `PPDM_INSECURE` | false | Skip SSL verification |
| `WEB_MONITOR_HOST` | 0.0.0.0 | Web monitor bind address |
| `WEB_MONITOR_PORT` | 8080 | Web monitor port |
| `WEB_MONITOR_INTERVAL` | 10 | Refresh interval (seconds) |
| `WEB_MONITOR_ACTIVITIES` | 20 | Number of activities to show |
| `DEFAULT_CATEGORY` | - | Default activity category |
| `DEFAULT_STATE` | - | Default activity state |
| `DEFAULT_ACTIVITY_TYPE` | - | Default activity type |
| `DEBUG` | false | Enable debug logging |
| `TOOL_GATEWAY_PORT` | 9000 | Tool Gateway API port |
| `DEMO_APP_PORT` | 8081 | Demo application port |
| `PPDM_CONFIG_FILE` | /home/ppdm/.ppdm/config.json | PPDM config file path |

### Tool Gateway Configuration

The tool gateway provides a REST API for executing PPDM CLI tools:

#### Available Tools
- **asset-list-tool**: List assets with optional filtering by type
- **backup-trigger-tool**: Trigger backup operations
- **job-status-tool**: Check backup job status

#### Tool Parameters
All tools support dynamic server selection via the `server` parameter:
```json
{
  "server": "prod",
  "types": "VMWARE_VIRTUAL_MACHINE",
  "filter": "name eq \"my-vm\""
}
```

#### API Authentication
Use API key authentication for tool execution:
```bash
curl -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -d '{"server": "prod", "types": "VMWARE_VIRTUAL_MACHINE"}' \
  http://localhost:9000/tools/asset-list-tool/execute
```

### Port Mapping

- **Container Port**: 8080 (web monitor), 9000 (tool gateway), 8081 (demo app)
- **Host Port**: Configurable (default: 8080, 9000, 8081)

```bash
# Different external ports
docker run -p 9090:8080 -p 9900:9000 -p 9081:8081 ppdm-cli

# Bind to specific interface
docker run -p 127.0.0.1:8080:8080 -p 127.0.0.1:9000:9000 ppdm-cli
```

## 🌐 Tool Gateway and Demo Application

### Tool Gateway API

The tool gateway provides a REST API for executing PPDM CLI tools programmatically.

#### API Endpoints

| Endpoint | Method | Auth Required | Description |
|----------|--------|--------------|-------------|
| `/health` | GET | No | Health check |
| `/tools` | GET | Yes | List available tools |
| `/tools/{id}` | GET | Yes | Get tool details |
| `/tools/{id}/execute` | POST | Yes | Execute tool |
| `/config/servers` | GET | No | Get available PPDM servers |
| `/swagger` | GET | No | API documentation |

#### Tool Execution Example

```bash
# List assets from prod server
curl -X POST http://localhost:9000/tools/asset-list-tool/execute \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your-api-key" \
  -d '{
    "server": "prod",
    "types": "VMWARE_VIRTUAL_MACHINE",
    "filter": "name eq \"my-vm\""
  }'

# Trigger backup
curl -X POST http://localhost:9000/tools/backup-trigger-tool/execute \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your-api-key" \
  -d '{
    "server": "prod",
    "assetId": "asset-uuid",
    "policyId": "policy-uuid"
  }'

# Check job status
curl -X POST http://localhost:9000/tools/job-status-tool/execute \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your-api-key" \
  -d '{
    "server": "prod",
    "jobId": "activity-uuid"
  }'
```

### Demo Application

The demo application provides a web UI for testing the tool gateway.

#### Features
- **Dynamic Server Selection**: Automatically discovers PPDM servers from config.json
- **Tool Execution**: Form-based interface for tool parameters
- **Debug Display**: Shows both tool call details and API response
- **Real-time Feedback**: Loading states and error handling

#### Access and Usage

1. **Access the demo**: http://localhost:8081
2. **Configure connection**: Enter API key and gateway URL
3. **Select server**: Choose from dynamically loaded PPDM servers
4. **Execute tools**: Use the form interface to run tools
5. **View results**: See both tool call details and response

#### Demo Application Docker Compose

```yaml
services:
  tool-demo:
    build:
      context: .
      dockerfile: Dockerfile.tool-demo
    container_name: tool-demo
    ports:
      - "8081:8080"
    depends_on:
      - ppdm-cli
    restart: unless-stopped
```

## 📁 File Structure

```
.
├── Dockerfile                 # Multi-stage build definition
├── docker-compose.yml         # Complete compose configuration
├── docker/
│   └── entrypoint.sh          # Container startup script
├── .env.example               # Environment variables template
├── config/                    # Configuration files (mounted)
├── logs/                      # Application logs (mounted)
└── certs/                     # SSL certificates (mounted)
```

## 🛠️ Advanced Configuration

### Custom Docker Compose

```yaml
version: '3.8'

services:
  ppdm-web-monitor:
    build: .
    container_name: ppdm-web-monitor-prod
    ports:
      - "0.0.0.0:8080:8080"
    environment:
      - PPDM_SERVER=prod-ppdm.company.com
      - PPDM_USERNAME=monitor-user
      - PPDM_PASSWORD=${PPDM_PASSWORD}
      - WEB_MONITOR_INTERVAL=5
      - DEBUG=false
    volumes:
      - ./config:/app/config:ro
      - ./logs:/app/logs
      - /etc/ssl/certs:/etc/ssl/certs:ro
    restart: always
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080"]
      interval: 30s
      timeout: 10s
      retries: 3
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 512M
```

### Production Best Practices

```bash
# 1. Use specific image version
docker build -t ppdm-cli:20.1.0.0 .

# 2. Run with resource limits
docker run -d --name ppdm-web-monitor \
    --memory=256m --cpus=0.5 \
    -p 8080:8080 \
    ppdm-cli:20.1.0.0

# 3. Use read-only filesystem
docker run -d --name ppdm-web-monitor \
    --read-only \
    --tmpfs /tmp \
    -p 8080:8080 \
    ppdm-cli

# 4. Run as non-root user (included in Dockerfile)
# The container already runs as user 'ppdm' (UID 1001)
```

## 🔍 Monitoring and Logs

### View Logs

```bash
# Docker Compose
docker-compose logs -f ppdm-web-monitor

# Docker
docker logs -f ppdm-web-monitor

# Filter logs
docker logs ppdm-web-monitor | grep "Activity monitor"
```

### Health Checks

```bash
# Check container health
docker ps --format "table {{.Names}}\t{{.Status}}"

# Manual health check
curl -f http://localhost:8080 || echo "Service unhealthy"
```

### Performance Monitoring

```bash
# Container resource usage
docker stats ppdm-web-monitor

# Container inspection
docker inspect ppdm-web-monitor
```

## 🔒 Security Considerations

### Network Security

```bash
# 1. Use custom network
docker network create ppdm-net
docker run --network ppdm-net -p 127.0.0.1:8080:8080 ppdm-cli

# 2. Limit exposed ports
docker run -p 127.0.0.1:8080:8080 ppdm-cli

# 3. Use HTTPS proxy (nginx/traefik)
# Place reverse proxy in front of the container
```

### Secrets Management

```bash
# 1. Use Docker secrets (Swarm mode)
echo "your-password" | docker secret create ppdm_password -

# 2. Use environment file with restricted permissions
chmod 600 .env
docker run --env-file .env ppdm-cli

# 3. Use external secret management (HashiCorp Vault, etc.)
```

## 🐛 Troubleshooting

### Common Issues

#### 1. Container Won't Start
```bash
# Check logs
docker logs ppdm-web-monitor

# Common causes:
# - Invalid PPDM credentials
# - Network connectivity issues
# - Port conflicts
```

#### 2. Can't Access Web UI
```bash
# Check port mapping
docker port ppdm-web-monitor

# Check container binding
docker exec ppdm-web-monitor netstat -tlnp

# Test from inside container
docker exec ppdm-web-monitor curl http://localhost:8080
```

#### 3. Authentication Issues
```bash
# Test PPDM connectivity
docker exec ppdm-web-monitor \
    ./ppdm-cli activities list --limit 1

# Check environment variables
docker exec ppdm-web-monitor env | grep PPDM
```

### Debug Mode

```bash
# Enable debug logging
docker run -e DEBUG=true -p 8080:8080 ppdm-cli

# Or update environment file
echo "DEBUG=true" >> .env
docker-compose up -d
```

## 📈 Scaling

### Multiple Instances

```yaml
# docker-compose.yml
version: '3.8'
services:
  ppdm-web-monitor:
    build: .
    deploy:
      replicas: 3
    ports:
      - "8080-8082:8080"
    # ... rest of configuration
```

### Load Balancing

```bash
# Using nginx as load balancer
upstream ppdm_monitor {
    server ppdm-web-monitor-1:8080;
    server ppdm-web-monitor-2:8080;
    server ppdm-web-monitor-3:8080;
}

server {
    listen 80;
    location / {
        proxy_pass http://ppdm_monitor;
    }
}
```

## 🔄 Updates and Maintenance

### Updating the Container

```bash
# 1. Pull latest code
git pull

# 2. Rebuild image
docker-compose build --no-cache

# 3. Restart service
docker-compose up -d

# 4. Remove old image
docker image prune -f
```

### Backup Configuration

```bash
# Backup configuration
tar -czf ppdm-config-backup.tar.gz config/ .env

# Restore configuration
tar -xzf ppdm-config-backup.tar.gz
```

## 📞 Support

For issues with:
- **Docker setup**: Check Docker documentation
- **PPDM connectivity**: Verify server credentials and network
- **Web monitor functionality**: Check application logs

## 🎯 Production Deployment Checklist

- [ ] Environment variables configured
- [ ] SSL certificates in place (if required)
- [ ] Resource limits set
- [ ] Health checks configured
- [ ] Log rotation setup
- [ ] Backup strategy implemented
- [ ] Monitoring and alerting configured
- [ ] Security hardening completed
