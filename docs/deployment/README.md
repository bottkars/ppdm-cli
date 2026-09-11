# PPDM CLI Deployment Guide

## Overview

This section contains comprehensive deployment guides for PPDM CLI in various environments including Kubernetes, OpenShift, and container platforms with **complete OpenTelemetry observability**, **comprehensive log level system**, and production-ready configurations.

## 📋 Deployment Options

### 🐳 Container Deployment
- [Docker Deployment](DOCKER_DEPLOYMENT.md) - Docker and container usage guide
- [Container AI Deployment](CONTAINER_AI_DEPLOYMENT.md) - AI-assisted container deployment

### ☸️ Kubernetes Deployment
- [Kubernetes Deployment](KUBERNETES_DEPLOYMENT.md) - Complete Kubernetes setup with OTel + Log Levels
- [Kubernetes Resources](KUBERNETES_RESOURCES.md) - Kubernetes manifests and usage guide
- [Kubernetes Backup](backup-kubernetes.md) - Kubernetes-specific backup operations

### 🏢 OpenShift Deployment
- [OpenShift 4.20 Security Fixes](OPENSHIFT_4.20_SECURITY_FIXES.md) - Security configuration for OpenShift 4.20
- [OpenShift Resources](openshift) - OpenShift-specific manifests and configurations

## 🚀 Quick Start

### Container Deployment with OTel + Log Levels
```bash
# Pull the latest image
podman pull quay.io/delldps/ppdm-cli:latest

# Run with environment variables, OTel, and clean logging
podman run --rm \
  -e PPDM_HOST=ppdm.example.com \
  -e PPDM_USERNAME=admin \
  -e PPDM_PASSWORD=password \
  -e PPDM_OTEL_ENABLED=true \
  -e PPDM_LOG=stdout \
  -e PPDM_LOG_LEVEL=error \
  quay.io/delldps/ppdm-cli:latest assets list

# File-based OTel output with debug logging
podman run --rm \
  -v /tmp/otel:/tmp/otel \
  -e PPDM_HOST=ppdm.example.com \
  -e PPDM_USERNAME=admin \
  -e PPDM_PASSWORD=password \
  -e PPDM_OTEL_ENABLED=true \
  -e PPDM_TRACES_FILE=/tmp/otel/traces.json \
  -e PPDM_METRICS_FILE=/tmp/otel/metrics.json \
  -e PPDM_LOG_LEVEL=debug \
  quay.io/delldps/ppdm-cli:latest assets list --show-api-calls
```

### Kubernetes Deployment with Log Levels
```bash
# Deploy with automatic OpenShift detection and log level configuration
git clone https://github.com/bottkars/ppdm-cli.git
cd ppdm-cli
./deploy-k8s.sh --ppdm-server ppdm-1.demo.local --username admin --password Password123! --insecure-skip-verify true --log-level error
```

## 🔊 Log Level Configuration

### **Production Deployment**
```bash
# Environment variables for production
export PPDM_LOG_LEVEL=error
export PPDM_OTEL_ENABLED=true
export PPDM_LOG=stdout

# Container deployment
docker run -e PPDM_LOG_LEVEL=error -e PPDM_OTEL_ENABLED=true ppdm-cli status

# Kubernetes CronJob
kubectl apply -f k8s/cronjob-alerts.yaml  # Uses --quiet flag
```

### **Development Deployment**
```bash
# Environment variables for development
export PPDM_LOG_LEVEL=info
export PPDM_OTEL_ENABLED=true
export PPDM_SHOW_API_CALLS=true

# Container deployment with API visibility
docker run -e PPDM_LOG_LEVEL=info -e PPDM_SHOW_API_CALLS=true ppdm-cli assets list --show-api-calls
```

### **Debugging Deployment**
```bash
# Environment variables for debugging
export PPDM_LOG_LEVEL=debug
export PPDM_OTEL_ENABLED=true
export PPDM_SHOW_API_CALLS=true

# Container deployment with comprehensive debugging
docker run -e PPDM_LOG_LEVEL=debug -e PPDM_SHOW_API_CALLS=true ppdm-cli protection-policies create --show-api-calls --name "Debug Policy"
```

## 📊 Environment Configuration Matrix

| **Environment** | **Log Level** | **OTel** | **API Calls** | **Use Case** |
|-----------------|---------------|---------|--------------|-------------|
| **Production** | `error` | `true` | `false` | Clean monitoring |
| **Staging** | `warn` | `true` | `false` | Pre-production testing |
| **Development** | `info` | `true` | `false` | Development work |
| **Debugging** | `debug` | `true` | `true` | Troubleshooting |

## 🐳 Container Deployment Patterns

### **Podman/Docker Production Pattern**
```bash
# Production monitoring with clean output
podman run -d --name ppdm-monitor \
  -e PPDM_HOST=ppdm.prod.company.com \
  -e PPDM_USERNAME=admin \
  -e PPDM_PASSWORD=${PPDM_PASSWORD} \
  -e PPDM_LOG_LEVEL=error \
  -e PPDM_OTEL_ENABLED=true \
  -e PPDM_LOG=/var/log/ppdm/otel.log \
  -v /var/log/ppdm:/var/log/ppdm \
  quay.io/delldps/ppdm-cli:latest \
  status --quiet
```

### **Docker Development Pattern**
```bash
# Development with API visibility
docker run -it --rm \
  -e PPDM_HOST=ppdm.dev.company.com \
  -e PPDM_USERNAME=admin \
  -e PPDM_PASSWORD=${PPDM_PASSWORD} \
  -e PPDM_LOG_LEVEL=info \
  -e PPDM_SHOW_API_CALLS=true \
  quay.io/delldps/ppdm-cli:latest \
  assets list --show-api-calls
```

### **Docker Debugging Pattern**
```bash
# Comprehensive debugging session
docker run -it --rm \
  -e PPDM_HOST=ppdm.debug.company.com \
  -e PPDM_USERNAME=admin \
  -e PPDM_PASSWORD=${PPDM_PASSWORD} \
  -e PPDM_LOG_LEVEL=debug \
  -e PPDM_SHOW_API_CALLS=true \
  -e PPDM_OTEL_ENABLED=true \
  quay.io/delldps/ppdm-cli:latest \
  protection-policies list --show-api-calls --log-level debug
```

## ☸️ Kubernetes Deployment Patterns

### **Production CronJob**
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: ppdm-production-monitor
spec:
  schedule: "*/5 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: ppdm-cli
            image: quay.io/delldps/ppdm-cli:latest
            args: ["status", "--quiet"]
            env:
            - name: PPDM_LOG_LEVEL
              value: "error"
            - name: PPDM_OTEL_ENABLED
              value: "true"
```

### **Development Deployment**
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: ppdm-development-monitor
spec:
  schedule: "*/10 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: ppdm-cli
            image: quay.io/delldps/ppdm-cli:latest
            args: ["assets", "list", "--log-level", "info"]
            env:
            - name: PPDM_LOG_LEVEL
              value: "info"
            - name: PPDM_SHOW_API_CALLS
              value: "false"
```

### **Debugging Job**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ppdm-debug-session
spec:
  restartPolicy: Never
  containers:
  - name: ppdm-cli
    image: quay.io/delldps/ppdm-cli:latest
    args: ["protection-policies", "list", "--show-api-calls", "--log-level", "debug"]
    env:
    - name: PPDM_LOG_LEVEL
      value: "debug"
    - name: PPDM_SHOW_API_CALLS
      value: "true"
    - name: PPDM_OTEL_ENABLED
      value: "true"
```

### Kubernetes Deployment with OTel
```bash
# Apply the deployment manifest with OTel
kubectl apply -f kubernetes-deployment-otel.yaml

# Check the deployment
kubectl get pods -l app=ppdm-cli

# Run a command with OTel
kubectl exec -it deployment/ppdm-cli -- ppdm-cli assets list --otel-enabled --ppdm-log stdout

# Check OTel logs
kubectl logs -f deployment/ppdm-cli | grep "ppdm.api.request"
```

### OpenShift Deployment with OTel
```bash
# Apply OpenShift-specific manifests with OTel
oc apply -f openshift/ppdm-cli-otel.yaml

# Check the deployment
oc get pods -l app=ppdm-cli

# Run with OTel
oc exec -it deployment/ppdm-cli -- ppdm-cli assets list --otel-enabled --ppdm-log stdout
```

## 🔥 OpenTelemetry Deployment Examples

### **Production CronJob with OTel**
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: ppdm-monitoring-otel
spec:
  schedule: "*/5 * * * *"  # Every 5 minutes
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: ppdm-cli
            image: quay.io/delldps/ppdm-cli:latest
            command: ["ppdm-cli"]
            args: ["health", "entities", "--otel-enabled", "--ppdm-log", "stdout"]
            env:
            - name: PPDM_SERVER
              valueFrom:
                secretKeyRef:
                  name: ppdm-secrets
                  key: server
            - name: PPDM_USERNAME
              valueFrom:
                secretKeyRef:
                  name: ppdm-secrets
                  key: username
            - name: PPDM_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: ppdm-secrets
                  key: password
            - name: PPDM_OTEL_ENABLED
              value: "true"
            - name: PPDM_LOG
              value: "stdout"
          restartPolicy: OnFailure
```

### **ConfigMap for OTel Configuration**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: ppdm-otel-config
data:
  otel-enabled: "true"
  ppdm-log: "stdout"
  sample-rate: "0.1"  # 10% sampling for production
  traces-file: "/var/log/ppdm/traces.json"
  metrics-file: "/var/log/ppdm/metrics.json"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ppdm-otel-logs
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## 📊 Monitoring and Observability

### **Log Collection**
```bash
# Collect OTel traces
kubectl logs -f deployment/ppdm-cli | grep "ppdm.api.request"

# Extract error traces
kubectl logs deployment/ppdm-cli | jq '.[] | select(.Status.Code == "Error")'

# Monitor metrics
kubectl logs deployment/ppdm-cli | grep "ppdm_requests_total"
```

### **File-Based Monitoring**
```bash
# Copy OTel files from container
kubectl cp ppdm-cli-pod:/var/log/ppdm/traces.json ./traces.json
kubectl cp ppdm-cli-pod:/var/log/ppdm/metrics.json ./metrics.json

# Analyze traces
cat traces.json | jq '.[] | .Attributes[] | select(.Key == "http.status_code")'

# Analyze metrics
cat metrics.json | jq '.[] | .ScopeMetrics[].Metrics[] | select(.Name == "ppdm_requests_total")'
```

## 🎯 Best Practices

### **Production OTel Configuration**
- Use **10% sampling** (`PPDM_SAMPLE_RATE=0.1`) for performance
- **Separate files** for traces and metrics
- **Log rotation** for long-running deployments
- **Persistent storage** for trace/metric files
- **Error monitoring** with alerting on HTTP 4xx/5xx

### **Container Optimization**
- Use **minimal Alpine Linux** base image
- **Resource limits** for controlled memory usage
- **Health checks** for container monitoring
- **Graceful shutdown** for trace cleanup

### **Security Considerations**
- **Secret management** for PPDM credentials
- **Network policies** for controlled access
- **RBAC** for Kubernetes deployments
- **File permissions** for trace/metric files

## 📁 File Structure

```
Documentation/deployment/
├── README.md                           # This file
├── KUBERNETES_DEPLOYMENT.md            # Kubernetes deployment guide
├── OPENSHIFT_4.20_SECURITY_FIXES.md    # OpenShift 4.20 security fixes
├── PODMAN_GUIDE.md                     # Podman deployment guide
├── PORTABILITY.md                      # Cross-platform deployment
├── backup-kubernetes.md               # Kubernetes backup operations
└── openshift/                          # OpenShift resources
    ├── deployment.yaml                  # OpenShift deployment manifest
    └── imagestream.yaml                 # OpenShift ImageStream
```

## 🔧 Configuration

### Environment Variables
```bash
# Required for authentication
export PPDM_HOST="ppdm-server.example.com"
export PPDM_USERNAME="admin"
export PPDM_PASSWORD="your-password"

# Optional
export PPDM_PORT="8443"
export PPDM_DEBUG="true"
export PPDM_CONFIG_FILE="/path/to/config.json"
```

### Multi-Host Configuration
```json
{
  "hosts": {
    "staging": {
      "host": "ppdm-staging.example.com",
      "port": 8443,
      "username": "admin",
      "password": "password",
      "insecure": true
    },
    "production": {
      "host": "ppdm-prod.example.com", 
      "port": 8443,
      "username": "admin",
      "password": "password",
      "insecure": false
    }
  },
  "default": "staging"
}
```

## 🏗️ Deployment Patterns

### 1. Job-Based Execution
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: ppdm-cli-job
spec:
  template:
    spec:
      containers:
      - name: ppdm-cli
        image: quay.io/delldps/ppdm-cli:latest
        command: ["ppdm-cli", "assets", "list"]
        env:
        - name: PPDM_HOST
          value: "ppdm.example.com"
        - name: PPDM_USERNAME
          valueFrom:
            secretKeyRef:
              name: ppdm-credentials
              key: username
        - name: PPDM_PASSWORD
          valueFrom:
            secretKeyRef:
              name: ppdm-credentials
              key: password
      restartPolicy: Never
```

### 2. CronJob-Based Scheduling
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: ppdm-health-check
spec:
  schedule: "0 */6 * * *"  # Every 6 hours
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: ppdm-cli
            image: quay.io/delldps/ppdm-cli:latest
            command:
            - /bin/bash
            - -c
            - |
              echo "Health check at $(date)"
              ppdm-cli nodes list --filter "status ne 'HEALTHY'" --output table
              ppdm-cli alerts list --filter "severity eq 'CRITICAL' and status eq 'ACTIVE'" --output table
            env:
            - name: PPDM_HOST
              value: "ppdm.example.com"
            - name: PPDM_USERNAME
              valueFrom:
                secretKeyRef:
                  name: ppdm-credentials
                  key: username
            - name: PPDM_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: ppdm-credentials
                  key: password
          restartPolicy: OnFailure
```

### 3. Service-Based Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ppdm-cli-service
spec:
  replicas: 2
  selector:
    matchLabels:
      app: ppdm-cli-service
  template:
    metadata:
      labels:
        app: ppdm-cli-service
    spec:
      containers:
      - name: ppdm-cli
        image: quay.io/delldps/ppdm-cli:latest
        command: ["sleep", "infinity"]  # Keep container running
        env:
        - name: PPDM_HOST
          value: "ppdm.example.com"
        - name: PPDM_USERNAME
          valueFrom:
            secretKeyRef:
              name: ppdm-credentials
              key: username
        - name: PPDM_PASSWORD
          valueFrom:
            secretKeyRef:
              name: ppdm-credentials
              key: password
        volumeMounts:
        - name: config
          mountPath: /home/ppdm/.ppdm
      volumes:
      - name: config
        configMap:
          name: ppdm-config
```

## 🔒 Security Considerations

### Container Security
- Use non-root containers when possible
- Limit container capabilities
- Use read-only filesystems where appropriate
- Implement resource limits

### Kubernetes Security
- Use RBAC to limit permissions
- Store credentials in secrets
- Use network policies to restrict traffic
- Implement pod security policies

### OpenShift Security
- Configure SecurityContextConstraints
- Use service accounts with minimal permissions
- Implement pod security standards
- Use namespace isolation

## 📊 Monitoring and Logging

### Health Checks
```bash
# Check container health
ppdm-cli nodes list --filter "status eq 'HEALTHY'"

# Check API connectivity
ppdm-cli --debug assets list
```

### Logging
```bash
# Enable debug logging
ppdm-cli --debug activities list

# Log to file
ppdm-cli assets list --output json > assets-$(date +%Y%m%d).json
```

### Metrics Collection
```bash
# Collect metrics for monitoring
ppdm-cli assets list --output json | jq '.page.totalElements' > /metrics/ppdm_assets_total
ppdm-cli activities list --filter "status eq 'RUNNING'" --output json | jq '.page.totalElements' > /metrics/ppdm_activities_running
ppdm-cli alerts list --filter "status eq 'ACTIVE'" --output json | jq '.page.totalElements' > /metrics/ppdm_alerts_active
```

## 🚨 Troubleshooting

### Common Issues

#### Connection Problems
```bash
# Test connection
ppdm-cli --debug nodes list

# Check environment variables
env | grep PPDM_

# Verify configuration
cat ~/.ppdm/config.json
```

#### Permission Issues
```bash
# Check file permissions
ls -la ~/.ppdm/

# Test authentication
ppdm-cli assets list
```

#### Container Issues
```bash
# Check container logs
kubectl logs deployment/ppdm-cli

# Check container status
kubectl get pods -l app=ppdm-cli

# Debug container
kubectl exec -it deployment/ppdm-cli -- /bin/bash
```

### OpenShift-Specific Issues

#### SecurityContextConstraints
```bash
# Check SCC
oc get scc ppdm-monitor-scc

# Check service account
oc get serviceaccount ppdm-monitor

# Check pod security
oc get pod ppdm-monitor-pod -o yaml | grep securityContext
```

## 📚 Additional Resources

### Command Reference
- [Command Reference Guide](../COMMAND_REFERENCE.md) - Complete CLI documentation

---

**Version**: 20.1.0.0-12  
**Last Updated**: March 16, 2026
