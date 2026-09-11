# PPDM Kubernetes Deployment

Complete Kubernetes deployment for PPDM monitoring with automated alert acknowledgment and system health checks.

## 🚀 Quick Start

### One-Command Deployment
```bash
# Deploy everything with a single command
./deploy-k8s.sh
```

### Manual Deployment
```bash
# Deploy all manifests
kubectl apply -f k8s/
```

## 📁 File Structure

```
k8s/
├── namespace.yaml              # ppdm-monitor namespace
├── secrets.yaml               # PPDM credentials (base64 encoded)
├── rbac.yaml                 # Service account and permissions
├── cronjob-alerts.yaml      # Alert acknowledgment (every 5 min)
├── cronjob-system-check.yaml  # System health checks (every 10 min)
└── README.md                 # Detailed documentation
```

## 🔐 Configuration

### PPDM Server Details
- **Host**: `ppdm-1.demo.local`
- **Username**: `administrator`
- **Password**: `Password123!`
- **Port**: `8443`
- **SSL**: Insecure (demo environment)

### Scheduling
- **Alerts Monitor**: Every 5 minutes
- **System Check**: Every 10 minutes
- **Concurrency**: Forbidden (one job at a time)

## 📊 Monitoring Features

### Automated Alert Acknowledgment
- Runs `ppdm-cli alerts acknowledge-all` every 5 minutes
- Ensures no alerts go unnoticed
- Provides immediate response to critical alerts

### System Health Monitoring
- Checks PPDM connectivity every 10 minutes
- Retrieves system information and node status
- Logs all health check results

### Resource Management
- CPU: 100m request, 200m limit per pod
- Memory: 128Mi request, 256Mi limit per pod
- Job history: 3 successful, 3 failed jobs retained

## 🔍 Operations

### Monitor Deployment
```bash
# Check all resources
kubectl get all -n ppdm-monitor

# Check CronJob status
kubectl get cronjobs -n ppdm-monitor

# View job history
kubectl get jobs -n ppdm-monitor --sort-by=.metadata.creationTimestamp

# Watch real-time logs
kubectl logs -n ppdm-monitor -l job-name=ppdm-alerts-monitor-$(date +%s) -f
```

### Manual Operations
```bash
# Trigger manual alert acknowledgment
kubectl create job --from=cronjob/ppdm-alerts-monitor -n ppdm-monitor manual-$(date +%s)

# Trigger manual system check
kubectl create job --from=cronjob/ppdm-system-check -n ppdm-monitor manual-check-$(date +%s)

# Clean up old jobs
kubectl delete jobs --all -n ppdm-monitor --wait=false
```

### Troubleshooting
```bash
# Check pod issues
kubectl describe pod -n ppdm-monitor -l job-name=<job-name>

# Check CronJob issues
kubectl describe cronjob -n ppdm-monitor ppdm-alerts-monitor

# View events
kubectl get events -n ppdm-monitor --sort-by=.metadata.creationTimestamp
```

## 🛡️ Security

### RBAC
- Dedicated service account: `ppdm-monitor-sa`
- Least privilege role for pod and job management
- Namespace-isolated permissions

### Secrets Management
- Credentials stored as Kubernetes secrets
- Base64 encoded values
- Environment variable injection to containers

### Network Security
- Consider network policies for production
- Restrict external access from monitoring pods
- Use TLS termination at ingress level

## 🔄 Updates and Maintenance

### Updating Secrets
```bash
# Update PPDM credentials
kubectl patch secret ppdm-secrets -n ppdm-monitor -p '{"data":{"ppdm-host":"<new-base64-value>","ppdm-username":"<new-base64-value>","ppdm-password":"<new-base64-value>"}}'
```

### Updating Schedules
```bash
# Change alert monitoring to every 3 minutes
kubectl patch cronjob ppdm-alerts-monitor -n ppdm-monitor -p '{"spec":{"schedule":"*/3 * * * *"}}'

# Change system check to every 15 minutes
kubectl patch cronjob ppdm-system-check -n ppdm-monitor -p '{"spec":{"schedule":"*/15 * * * *"}}'
```

### Rolling Updates
```bash
# Redeploy without downtime
kubectl apply -f k8s/
# Jobs will continue with old configuration until new ones complete
kubectl delete jobs --all -n ppdm-monitor --wait=false
```

## 📈 Scaling and Performance

### Resource Scaling
```bash
# Increase resource limits
kubectl patch cronjob ppdm-alerts-monitor -n ppdm-monitor -p '{"spec":{"jobTemplate":{"spec":{"template":{"spec":{"containers":[{"name":"ppdm-cli","resources":{"limits":{"cpu":"500m","memory":"512Mi"}}}]}}}}}'
```

### Concurrency Control
```bash
# Allow concurrent jobs (for high-frequency monitoring)
kubectl patch cronjob ppdm-alerts-monitor -n ppdm-monitor -p '{"spec":{"concurrencyPolicy":"Allow"}}'
```

## 🧹 Cleanup

### Complete Removal
```bash
# Remove all monitoring resources
kubectl delete -f k8s/

# Remove namespace and everything in it
kubectl delete namespace ppdm-monitor
```

### Job Cleanup Only
```bash
# Clean completed jobs (keep configuration)
kubectl delete jobs --all -n ppdm-monitor --field-selector=status.successful=1

# Clean failed jobs
kubectl delete jobs --all -n ppdm-monitor --field-selector=status.failed=1
```

## 📋 Production Considerations

### High Availability
- Consider multiple scheduler pods
- Use node affinity for PPDM server proximity
- Implement proper alerting on job failures

### Monitoring
- Monitor the monitoring jobs themselves
- Set up alerting for job failures
- Log aggregation for audit trails

### Backup and Recovery
- Backup Kubernetes manifests
- Document custom configurations
- Have recovery procedures ready

### Performance Optimization
- Optimize container images for size
- Use appropriate resource requests/limits
- Consider job completion times in scheduling

## 🔗 Integration

### External Monitoring
- Integrate with Prometheus for metrics
- Forward logs to centralized logging
- Set up alerting on job failures

### CI/CD Integration
```yaml
# Example GitOps workflow
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: ppdm-monitor
  namespace: ppdm-monitor
spec:
  source:
    repoURL: https://github.com/your-org/ppdm-k8s.git
    targetRevision: HEAD
  destination:
    server: https://kubernetes.default.svc
    namespace: ppdm-monitor
  syncPolicy:
    automated:
      prune: true
    syncOptions:
    - CreateNamespace=true
```
