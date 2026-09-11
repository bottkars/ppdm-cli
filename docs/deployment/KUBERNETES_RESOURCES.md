# PPDM Kubernetes/OpenShift Deployment

This directory contains Kubernetes manifests for deploying PPDM monitoring in the `ppdm-monitor` namespace with **complete OpenTelemetry observability**, **comprehensive log level system**, and full OpenShift compatibility.

## 🚀 Deployment Overview

### Components
1. **Namespace** (`namespace.yaml`)
   - Creates isolated `ppdm-monitor` namespace
   - Labeled for monitoring purposes

2. **Secrets** (`secrets.yaml`)
   - Stores PPDM connection credentials securely
   - Contains base64 encoded values:
     - Host: `ppdm-1.demo.local`
     - Username: `administrator`
     - Password: `Password123!`

3. **CronJob - Alerts Monitor** (`cronjob-alerts.yaml`)
   - Runs every 5 minutes (`*/5 * * * *`)
   - Executes `ppdm-cli alerts acknowledge-all`
   - **🔥 OpenTelemetry tracing** of alert operations
   - **🔊 Clean logging** with `--quiet` flag for production
   - Acknowledges all PPDM alerts automatically
   - OpenShift security context and annotations

4. **CronJob - System Check** (`cronjob-system-check.yaml`)
   - Runs every 10 minutes (`*/10 * * * *`)
   - Checks PPDM system connectivity
   - **🔥 OpenTelemetry tracing** of health checks
   - **🔊 Error-level logging** with `--log-level error`
   - Retrieves system information
   - OpenShift security context and annotations

5. **CronJob - Debug Monitor** (`cronjob-debug.yaml`) - **NEW**
   - Runs daily at 2 AM (`0 2 * * *`)
   - Executes with **comprehensive debugging**
   - **🔍 API call visibility** with `--show-api-calls`
   - **🔧 Debug logging** with `--log-level debug`
   - For troubleshooting and analysis

6. **ConfigMap** (`configmap-otel.yaml`)
   - OpenTelemetry configuration
   - Sampling rates and output settings
   - Trace and metric file locations

7. **ConfigMap** (`configmap-loglevels.yaml`) - **NEW**
   - Log level configuration for different environments
   - Production, development, and debugging presets
   - Environment-specific settings

8. **PersistentVolumeClaim** (`pvc-otel.yaml`)
   - Storage for OTel trace and metric files
   - 5Gi persistent storage
   - Automatic backup and rotation

7. **RBAC** (`rbac.yaml`)
   - Service account for monitoring pods
   - Role for pod and job management
   - Role binding with least privilege principle
   - OpenShift-specific annotations

8. **OpenShift-Specific Resources:**
   - **SecurityContextConstraints** (`securitycontextconstraints.yaml`)
     - Allows containers to run with any UID
     - Required for OpenShift security policies
   - **Service** (`service.yaml`)
     - Internal cluster access for monitoring pods
   - **Route** (`route.yaml`)
     - External access via OpenShift router
     - TLS termination support

## 🔥 OpenTelemetry Features

### **Complete Observability**
- **HTTP Request Tracing**: All API calls traced with method, URL, status code
- **Error Tracking**: Automatic error detection and classification
- **Performance Metrics**: Request duration and timing analysis
- **Business Operations**: High-level spans for alerts, health checks
- **File Output**: Persistent trace and metric storage

### **OTel Configuration**
```yaml
# ConfigMap example
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
```

### **Trace Output Example**
```json
{
  "Name": "ppdm.api.request",
  "Attributes": [
    {"Key": "http.method", "Value": {"Type": "STRING", "Value": "GET"}},
    {"Key": "http.url", "Value": {"Type": "STRING", "Value": "https://ppdm-server:8443/api/v2/alerts"}},
    {"Key": "http.status_code", "Value": {"Type": "INT64", "Value": 200}},
    {"Key": "ppdm.endpoint", "Value": {"Type": "STRING", "Value": "/api/v2/alerts"}}
  ]
}
```

## 📋 Deployment Instructions

### Prerequisites
- Kubernetes or OpenShift cluster access
- kubectl or oc CLI configured
- Appropriate RBAC permissions

### Step-by-Step Deployment

#### Automated Deployment (Recommended)
```bash
# Deploy with automatic OpenShift detection
./deploy-k8s.sh
```

#### Manual Deployment
```bash
# Deploy all manifests
kubectl apply -f k8s/

# Or with oc on OpenShift
oc apply -f k8s/
```

### OpenShift-Specific Steps

1. **Create Namespace**
   ```bash
   kubectl apply -f namespace.yaml
   # or on OpenShift:
   oc apply -f namespace.yaml
   ```

2. **Create Secrets**
   ```bash
   # Update secrets with your actual values
   # Base64 encode: echo -n "your-value" | base64
   kubectl apply -f secrets.yaml
   ```

3. **Create RBAC and Security**
   ```bash
   kubectl apply -f rbac.yaml
   kubectl apply -f securitycontextconstraints.yaml
   ```

4. **Deploy OpenShift Resources**
   ```bash
   kubectl apply -f service.yaml
   kubectl apply -f route.yaml
   ```

5. **Deploy CronJobs**
   ```bash
   kubectl apply -f cronjob-alerts.yaml
   kubectl apply -f cronjob-system-check.yaml
   ```

## 🔍 Monitoring and Troubleshooting

### Check CronJob Status
```bash
# List cronjobs
kubectl get cronjobs -n ppdm-monitor
# or on OpenShift:
oc get cronjobs -n ppdm-monitor

# Check job history
kubectl get jobs -n ppdm-monitor
# or on OpenShift:
oc get jobs -n ppdm-monitor

# View pod logs
kubectl logs -n ppdm-monitor -l job-name=ppdm-alerts-monitor-<timestamp>
# or on OpenShift:
oc logs -n ppdm-monitor -l job-name=ppdm-alerts-monitor-<timestamp>
```

### Manual Job Execution
```bash
# Trigger alerts acknowledgment manually
kubectl create job --from=cronjob/ppdm-alerts-monitor -n ppdm-monitor manual-alerts-$(date +%s)
# or on OpenShift:
oc create job --from=cronjob/ppdm-alerts-monitor -n ppdm-monitor manual-alerts-$(date +%s)

# Trigger system check manually
kubectl create job --from=cronjob/ppdm-system-check -n ppdm-monitor manual-check-$(date +%s)
# or on OpenShift:
oc create job --from=cronjob/ppdm-system-check -n ppdm-monitor manual-check-$(date +%s)

# Trigger debug monitor manually (for troubleshooting)
kubectl create job --from=cronjob/ppdm-debug-monitor -n ppdm-monitor manual-debug-$(date +%s)
# or on OpenShift:
oc create job --from=cronjob/ppdm-debug-monitor -n ppdm-monitor manual-debug-$(date +%s)
```

---

## 🔊 Log Level Configuration

### **Production-Ready Logging**

The Kubernetes deployment includes **comprehensive log level configuration** for different environments and use cases.

### **📋 Log Level Presets**

#### **Production Environment** (`configmap-loglevels.yaml`)
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: ppdm-log-levels
  namespace: ppdm-monitor
data:
  production.log-level: "error"
  production.quiet: "true"
  production.otel-enabled: "true"
  
  development.log-level: "info"
  development.quiet: "false"
  development.otel-enabled: "true"
  
  debugging.log-level: "debug"
  debugging.quiet: "false"
  debugging.show-api-calls: "true"
  debugging.otel-enabled: "true"
```

### **🎯 CronJob Log Configurations**

#### **Alerts Monitor** - Clean Production Logging
```yaml
args:
- "alerts"
- "acknowledge-all"
- "--quiet"
- "--log-level"
- "error"
env:
- name: PPDM_LOG_LEVEL
  value: "error"
```

#### **System Check** - Error-Level Monitoring
```yaml
args:
- "status"
- "--log-level"
- "error"
env:
- name: PPDM_LOG_LEVEL
  value: "error"
```

#### **Debug Monitor** - Comprehensive Debugging
```yaml
args:
- "assets"
- "list"
- "--show-api-calls"
- "--log-level"
- "debug"
env:
- name: PPDM_LOG_LEVEL
  value: "debug"
- name: PPDM_SHOW_API_CALLS
  value: "true"
```

### **🌍 Environment Variables**

| **Variable** | **Production Value** | **Development Value** | **Debug Value** |
|--------------|---------------------|----------------------|-----------------|
| `PPDM_LOG_LEVEL` | `error` | `info` | `debug` |
| `PPDM_QUIET` | `true` | `false` | `false` |
| `PPDM_SHOW_API_CALLS` | `false` | `false` | `true` |
| `PPDM_OTEL_ENABLED` | `true` | `true` | `true` |

### **🔧 Custom Log Configuration**

#### **Update Log Levels for Environment**
```bash
# Update ConfigMap for different environments
kubectl patch configmap ppdm-log-levels -n ppdm-monitor --patch-file log-levels-patch.yaml

# Example patch for maintenance mode
apiVersion: v1
kind: ConfigMap
data:
  production.log-level: "warn"
  production.quiet: "false"
```

#### **Temporary Debug Job**
```bash
# Create one-time debug job
kubectl run ppdm-debug-temp \
  --image=quay.io/delldps/ppdm-cli:latest \
  --restart=Never \
  --namespace=ppdm-monitor \
  --env="PPDM_SERVER=$(kubectl get secret ppdm-secrets -n ppdm-monitor -o jsonpath='{.data.server}' | base64 -d)" \
  --env="PPDM_USERNAME=$(kubectl get secret ppdm-secrets -n ppdm-monitor -o jsonpath='{.data.username}' | base64 -d)" \
  --env="PPDM_PASSWORD=$(kubectl get secret ppdm-secrets -n ppdm-monitor -o jsonpath='{.data.password}' | base64 -d)" \
  --env="PPDM_LOG_LEVEL=debug" \
  --env="PPDM_SHOW_API_CALLS=true" \
  --command -- ppdm-cli assets list --show-api-calls
```

### **📊 Log Output Analysis**

#### **View Different Log Levels**
```bash
# Production logs (errors only)
kubectl logs -n ppdm-monitor -l job-name=ppdm-alerts-monitor-<timestamp> | grep ERROR

# Development logs (info level)
kubectl logs -n ppdm-monitor -l job-name=ppdm-debug-monitor-<timestamp> | grep INFO

# Debug logs (all levels with API calls)
kubectl logs -n ppdm-monitor -l job-name=ppdm-debug-monitor-<timestamp> | grep "🔍"
```

#### **Log Aggregation**
```bash
# Collect all logs with timing information
kubectl logs -n ppdm-monitor -l app=ppdm-cli --since=1h | grep -E "(ERROR|WARN|🔍)"

# Export logs for analysis
kubectl logs -n ppdm-monitor -l app=ppdm-cli --since=24h > /tmp/ppdm-logs-$(date +%Y%m%d).log
```

### OpenShift-Specific Monitoring
```bash
# Check OpenShift routes
oc get routes -n ppdm-monitor

# Check services
oc get services -n ppdm-monitor

# Check SecurityContextConstraints
oc get securitycontextconstraints -n ppdm-monitor

# Access via OpenShift Web Console
# Navigate to Developer > Topology in OpenShift web console
```

### Clean Up
```bash
# Delete all monitoring resources
kubectl delete -f k8s/
# or on OpenShift:
oc delete -f k8s/

# Delete only jobs (keep configuration)
kubectl delete jobs --all -n ppdm-monitor
# or on OpenShift:
oc delete jobs --all -n ppdm-monitor
```

## ⚙️ Configuration

### Environment Variables
- `PPDM_HOST`: PPDM server address
- `PPDM_USERNAME`: Admin username
- `PPDM_PASSWORD`: Admin password
- `PPDM_PORT`: PPDM port (default: 8443)
- `PPDM_INSECURE`: Skip SSL verification (true for demo)

### OpenShift Security Context
- `runAsNonRoot`: true
- `runAsUser`: 1000
- `runAsGroup`: 3000
- `fsGroup`: 3000
- Follows OpenShift security best practices

### Resource Limits
- CPU: 100m request, 200m limit per pod
- Memory: 128Mi request, 256Mi limit per pod
- Concurrency: Forbid (only one job at a time)

##  Scheduling

- **Alerts Monitor**: Every 5 minutes
  - 288 executions per day
  - Immediate alert acknowledgment

- **System Check**: Every 10 minutes
  - 144 executions per day
  - Health monitoring

## 🚨 Alerts Integration

The deployment automatically:
- Acknowledges all PPDM alerts every 5 minutes
- Monitors PPDM system health every 10 minutes
- Logs all actions for audit trail
- Fails gracefully on connection issues

## 🔄 Updates and Maintenance

### Updating Configuration
```bash
# Update secrets
kubectl patch secret ppdm-secrets -n ppdm-monitor -p '{"data":{"ppdm-host":"<new-base64>"}}'
# or on OpenShift:
oc patch secret ppdm-secrets -n ppdm-monitor -p '{"data":{"ppdm-host":"<new-base64>"}}'

# Update schedule
kubectl patch cronjob ppdm-alerts-monitor -n ppdm-monitor -p '{"spec":{"schedule":"*/3 * * * *"}}'
# or on OpenShift:
oc patch cronjob ppdm-alerts-monitor -n ppdm-monitor -p '{"spec":{"schedule":"*/3 * * * *"}}'
```

### Rolling Updates
```bash
# Redeploy with zero downtime
kubectl apply -f k8s/
# Jobs will continue with old configuration until new ones complete
kubectl delete jobs --all -n ppdm-monitor --wait=false
# or on OpenShift:
oc apply -f k8s/
oc delete jobs --all -n ppdm-monitor --wait=false
```

## 🛡️ Security Considerations

### OpenShift Security
- SecurityContextConstraints for non-root containers
- Service account with OpenShift-specific annotations
- Namespace isolation and least privilege RBAC
- Integration with OpenShift security policies

### Network Access
- Routes provide external access via OpenShift router
- Services provide internal cluster access
- Consider network policies for production
- TLS termination at edge/ingress level

### Secrets Management
- Kubernetes secrets for credential storage
- Base64 encoded values
- Environment variable injection to containers
- Regular secret rotation recommended

## 📊 OpenShift Web Console Integration

### Developer Topology
- View monitoring components graphically
- Check pod connections and dependencies
- Monitor real-time status updates

### Application Launcher
- Access monitoring logs directly from web console
- Trigger manual job executions
- View resource utilization metrics

### Monitoring Dashboards
- Integrate with OpenShift monitoring stack
- Set up alerting on job failures
- Log aggregation for audit trails

## 🔗 External Access

### Route Configuration
```bash
# Check external URL
oc get route ppdm-monitor-route -n ppdm-monitor -o jsonpath='{.spec.host}'
```

### TLS Configuration
- Supports automatic TLS certificate management
- Edge termination with redirect to HTTPS
- Custom certificate injection if needed

## 📋 Production Considerations

### OpenShift Enterprise Features
- Consider integration with OpenShift logging
- Use OpenShift built-in monitoring (Prometheus)
- Implement proper alerting via AlertManager
- Leverage OpenShift CI/CD pipelines

### High Availability
- Multiple scheduler pods across nodes
- Node affinity for PPDM server proximity
- Proper backup and recovery procedures

### Performance Optimization
- Optimize container images for OpenShift
- Use appropriate resource requests/limits
- Consider OpenShift-specific optimizations
