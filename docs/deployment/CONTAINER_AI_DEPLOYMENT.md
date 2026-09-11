# PPDM CLI AI Interface - Container Deployment Guide

## Overview

Deploy PPDM CLI with AI interface in containers using Podman/Docker with environment variable configuration for Ollama connectivity.

## Architecture

```
Container Network:
ppdm-cli (container)  <--->  ollama (container)  <--->  PPDM Server (external)
     :11434                    :11434                 :8443
```

## Environment Variables

### **Required for AI Interface:**
```bash
OLLAMA_URL=http://ollama:11434          # Ollama service URL
OLLAMA_MODEL=qwen2.5:7b                # Default model to use
```

### **Optional PPDM Configuration:**
```bash
PPDM_SERVER=your-ppdm-server.com       # PPDM server URL
PPDM_USERNAME=admin                     # PPDM username
PPDM_PASSWORD=your-password            # PPDM password
PPDM_PORT=8443                         # PPDM port (default: 8443)
PPDM_INSECURE_SKIP_VERIFY=false         # SSL verification
```

## Deployment Options

### **Option 1: Podman Compose (Recommended)**

```yaml
# docker-compose.yml
version: '3.8'

services:
  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama
    environment:
      - OLLAMA_HOST=0.0.0.0
    
  ppdm-cli:
    image: quay.io/delldps/ppdm-cli:latest
    container_name: ppdm-cli
    environment:
      # AI Configuration
      - OLLAMA_URL=http://ollama:11434
      - OLLAMA_MODEL=qwen2.5:7b
      
      # PPDM Configuration
      - PPDM_SERVER=your-ppdm-server.com
      - PPDM_USERNAME=admin
      - PPDM_PASSWORD=your-password
      - PPDM_PORT=8443
      - PPDM_INSECURE_SKIP_VERIFY=false
      
      # Optional: Log level for clean output
      - PPDM_LOG_LEVEL=error
    depends_on:
      - ollama
    command: ["sleep", "infinity"]  # Keep container running

volumes:
  ollama_data:

```

**Deploy:**
```bash
# Start services
podman-compose up -d

# Pull Ollama model
podman exec ollama ollama pull qwen2.5:7b

# Test AI interface
podman exec ppdm-cli ppdm-cli ai "show me all policies with 0 assets"
```

---

### **Option 2: Separate Container Commands**

```bash
# 1. Start Ollama container
podman run -d \
  --name ollama \
  -p 11434:11434 \
  -v ollama_data:/root/.ollama \
  ollama/ollama:latest

# 2. Pull model
podman exec ollama ollama pull qwen2.5:7b

# 3. Start PPDM CLI container
podman run -it --rm \
  --name ppdm-cli \
  --link ollama:ollama \
  -e OLLAMA_URL=http://ollama:11434 \
  -e OLLAMA_MODEL=qwen2.5:7b \
  -e PPDM_SERVER=your-ppdm-server.com \
  -e PPDM_USERNAME=admin \
  -e PPDM_PASSWORD=your-password \
  quay.io/delldps/ppdm-cli:latest \
  ai "show me all policies with 0 assets"
```

---

### **Option 3: Kubernetes Deployment**

```yaml
# k8s-deployment.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: ppdm-ai
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ollama
  namespace: ppdm-ai
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ollama
  template:
    metadata:
      labels:
        app: ollama
    spec:
      containers:
      - name: ollama
        image: ollama/ollama:latest
        ports:
        - containerPort: 11434
        env:
        - name: OLLAMA_HOST
          value: "0.0.0.0"
        volumeMounts:
        - name: ollama-storage
          mountPath: /root/.ollama
      volumes:
      - name: ollama-storage
        persistentVolumeClaim:
          claimName: ollama-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: ollama-service
  namespace: ppdm-ai
spec:
  selector:
    app: ollama
  ports:
  - port: 11434
    targetPort: 11434
---
apiVersion: batch/v1
kind: Job
metadata:
  name: ppdm-ai-test
  namespace: ppdm-ai
spec:
  template:
    spec:
      containers:
      - name: ppdm-cli
        image: quay.io/delldps/ppdm-cli:latest
        env:
        - name: OLLAMA_URL
          value: "http://ollama-service:11434"
        - name: OLLAMA_MODEL
          value: "qwen2.5:7b"
        - name: PPDM_SERVER
          value: "your-ppdm-server.com"
        - name: PPDM_USERNAME
          value: "admin"
        - name: PPDM_PASSWORD
          value: "your-password"
        command: ["ppdm-cli", "ai", "show me all policies with 0 assets"]
      restartPolicy: Never
```

---

## Configuration Examples

### **Development Environment:**
```bash
# Local development with container Ollama
export OLLAMA_URL=http://localhost:11434
export OLLAMA_MODEL=mistral
./ppdm-cli ai "show me policies with 0 assets"
```

### **Production Container:**
```bash
# Production deployment
podman run -it --rm \
  -e OLLAMA_URL=http://ollama-prod:11434 \
  -e OLLAMA_MODEL=qwen2.5:7b \
  -e PPDM_SERVER=ppdm-prod.company.com \
  -e PPDM_USERNAME=ppdm-admin \
  -e PPDM_PASSWORD=${PPDM_PASSWORD} \
  quay.io/delldps/ppdm-cli:latest \
  ai "list all failed activities"
```

### **CI/CD Pipeline:**
```yaml
# GitHub Actions example
- name: Test AI Interface
  run: |
    podman run --rm \
      -e OLLAMA_URL=http://ollama:11434 \
      -e OLLAMA_MODEL=qwen2.5:7b \
      -e PPDM_SERVER=${{ secrets.PPDM_SERVER }} \
      -e PPDM_USERNAME=${{ secrets.PPDM_USERNAME }} \
      -e PPDM_PASSWORD=${{ secrets.PPDM_PASSWORD }} \
      quay.io/delldps/ppdm-cli:latest \
      ai "show me system health"
```

## Network Configuration

### **Container-to-Container Communication:**
```bash
# Test connectivity
podman exec ppdm-cli curl -s http://ollama:11434/api/tags

# Expected output
{"models":[{"name":"qwen2.5:7b","model":"qwen2.5:7b",...}]}
```

### **External Access:**
```bash
# Expose Ollama externally (if needed)
podman run -d \
  --name ollama \
  -p 0.0.0.0:11434:11434 \
  -v ollama_data:/root/.ollama \
  ollama/ollama:latest
```

## Troubleshooting

### **Common Issues:**

#### **"Ollama service not available"**
```bash
# Check if Ollama container is running
podman ps | grep ollama

# Check network connectivity
podman exec ppdm-cli curl -v http://ollama:11434/api/tags

# Check logs
podman logs ollama
```

#### **"Model not available"**
```bash
# List available models
podman exec ollama ollama list

# Pull required model
podman exec ollama ollama pull qwen2.5:7b

# Verify model
podman exec ollama ollama show qwen2.5:7b
```

#### **Environment Variables Not Working**
```bash
# Check environment variables in container
podman exec ppdm-cli env | grep OLLAMA

# Test manually
podman exec ppdm-cli ppdm-cli ai --help | grep "env:"
```

#### **Network Issues**
```bash
# Check network configuration (should show default bridge)
podman network ls
podman network inspect bridge

# Test DNS resolution
podman exec ppdm-cli nslookup ollama
```

### **Debug Mode:**
```bash
# Enable debug logging
podman exec ppdm-cli ppdm-cli ai "test" --debug --log-level debug

# Show API calls
podman exec ppdm-cli ppdm-cli ai "test" --show-api-calls
```

## Security Considerations

### **Environment Variables:**
```bash
# Use secrets for sensitive data
podman secret create ppdm-password password-file.txt

# Reference in compose file
environment:
  - PPDM_PASSWORD_FILE=/run/secrets/ppdm-password
secrets:
  - ppdm-password
```

### **Network Security:**
```bash
# Isolate Ollama network
podman network create --internal ollama-internal

# Only expose required ports
podman run -d --network ollama-internal ollama/ollama:latest
```

### **Container Security:**
```bash
# Run as non-root user
podman run --user 1000:1000 ...

# Read-only filesystem
podman run --read-only --tmpfs /tmp ...

# Resource limits
podman run --memory=2g --cpus=1 ...
```

## Performance Optimization

### **Model Selection:**
```bash
# Faster models for production
OLLAMA_MODEL=mistral          # 7B, good balance
OLLAMA_MODEL=codellama:7b     # Better for CLI commands
OLLAMA_MODEL=qwen2.5:7b       # Your preferred model
```

### **Resource Allocation:**
```yaml
# docker-compose.yml with resource limits
services:
  ollama:
    deploy:
      resources:
        limits:
          memory: 8G
          cpus: '4'
        reservations:
          memory: 4G
          cpus: '2'
```

### **Caching:**
```bash
# Persistent model storage
volumes:
  ollama_data:
    driver: local
```

## Monitoring

### **Health Checks:**
```yaml
# docker-compose.yml
services:
  ollama:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:11434/api/tags"]
      interval: 30s
      timeout: 10s
      retries: 3
```

### **Logging:**
```bash
# Enable logging
podman run -d \
  --log-driver json-file \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  ollama/ollama:latest
```

## Examples

### **Daily Operations:**
```bash
# Check policy status
podman exec ppdm-cli ppdm-cli ai "show me all active policies"

# Find issues
podman exec ppdm-cli ppdm-cli ai "list failed activities from yesterday"

# System health
podman exec ppdm-cli ppdm-cli ai "show system health status"
```

### **Batch Operations:**
```bash
# Generate commands for review
podman exec ppdm-cli ppdm-cli ai "delete policies with 0 assets" --dry-run

# Get explanations
podman exec ppdm-cli ppdm-cli ai "explain: protection-policies list -f numberOfAssets gt 5"
```

---

## **Quick Start Summary**

```bash
# 1. Deploy with compose
curl -O https://raw.githubusercontent.com/your-repo/docker-compose.yml
podman-compose up -d

# 2. Pull model
podman exec ollama ollama pull qwen2.5:7b

# 3. Test AI interface
podman exec ppdm-cli ppdm-cli ai "show me all policies with 0 assets"

# 4. Start using
podman exec -it ppdm-cli ppdm-cli ai "your question here"
```

**Your PPDM CLI AI interface is now container-ready!**
