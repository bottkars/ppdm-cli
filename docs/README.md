# PPDM CLI Documentation

## Overview

This is the comprehensive documentation for PPDM CLI version 20.1.0.0-13, covering command reference, deployment guides, and operational procedures.

## 📚 Documentation Structure

### 🎯 Command Reference
- **[Complete Command Reference](COMMAND_REFERENCE.md)** - Comprehensive CLI command documentation
  - All 50+ commands with detailed examples
  - Global flags and configuration options
  - Filter expressions and output formats
  - Error handling and troubleshooting
- **[Individual Command Documentation](commands/)** - Detailed command-specific guides
  - [Activities](commands/ACTIVITIES.md) - Activity monitoring and management
  - [Infrastructure Objects](commands/INFRASTRUCTURE_OBJECTS.md) - Infrastructure object management with category filtering
  - [Backup and Restore](commands/BACKUP_RESTORE.md) - Backup and restore operations
  - [Curl](commands/CURL.md) - Direct API access and testing
  - [Commands Index](commands/README.md) - Complete command listing and quick reference
- **[Test Results](TEST_RESULTS.md)** - Comprehensive test results and validation
  - 187 tests with 100% success rate
  - Performance metrics and benchmarks
  - Real-world test scenarios
- **[Real Test Results](REAL_TEST_RESULTS.md)** - Actual test results from production environment
  - Live API response validation
  - Real data testing results
  - Production readiness assessment

### 🚀 Deployment Guides
- [Deployment Overview](deployment/README.md) - Deployment options and quick start
- [Kubernetes Deployment](deployment/KUBERNETES_DEPLOYMENT.md) - Complete Kubernetes setup
- [OpenShift 4.20 Security Fixes](deployment/OPENSHIFT_4.20_SECURITY_FIXES.md) - OpenShift security configuration
- [Podman Guide](deployment/PODMAN_GUIDE.md) - Local container deployment
- [Portability Guide](deployment/PORTABILITY.md) - Cross-platform deployment
- [Kubernetes Backup](deployment/backup-kubernetes.md) - Kubernetes-specific backup operations

### 📋 Release Information
- [Release 20.1.0.0-12](../docs/api/tasks/RELEASE-20.1.0.0-12.md) - Latest release documentation
- [API Reference](../docs/api/reference/RELEASE-20.1.0.0-12.md) - Complete API reference
- [Examples](../docs/api/examples/RELEASE-20.1.0.0-12.md) - Automation examples and scripts

## 🚀 Quick Start

### Installation
```bash
# Pull the container
podman pull quay.io/delldps/ppdm-cli:20.1.0.0-13

# Test the installation
podman run --rm quay.io/delldps/ppdm-cli:20.1.0.0-13 --version
```

### Configuration
```bash
# Set environment variables
export PPDM_HOST="ppdm.example.com"
export PPDM_USERNAME="admin"
export PPDM_PASSWORD="password"

# Test connection
ppdm-cli infrastructure-objects list
```

### Basic Usage
```bash
# List infrastructure objects with category filtering
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM

# List activities with filtering
ppdm-cli activities list --filter 'status eq "RUNNING"'

# Export to JSON
ppdm-cli infrastructure-objects list --output json > infra_objects.json

# Use multi-host configuration
ppdm-cli --host staging infrastructure-objects list
ppdm-cli --host production infrastructure-objects list

# Tab completion for categories
ppdm-cli infrastructure-objects list --category <TAB>
```

## 🌐 Multi-Host Configuration

Create `~/.ppdm/config.json`:
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

## 📊 Output Formats

All commands support multiple output formats:

```bash
# Table format (default)
ppdm-cli infrastructure-objects list

# JSON format
ppdm-cli infrastructure-objects list --output json

# YAML format
ppdm-cli infrastructure-objects list --output yaml

# CSV format
ppdm-cli infrastructure-objects list --output csv

# JSON-Raw format (strict API response)
ppdm-cli infrastructure-objects list --output json-raw

# YAML-Raw format (strict API response)
ppdm-cli infrastructure-objects list --output yaml-raw
```

## 🔍 Filter Expressions

Powerful filtering capabilities for all list commands:

```bash
# Filter by category (new feature)
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM

# Filter by type
ppdm-cli infrastructure-objects list --type VMWARE_ESX_HOST

# Filter by vendor
ppdm-cli infrastructure-objects list --vendor VMWARE

# Filter by status
ppdm-cli activities list --filter "status eq 'RUNNING'"

# Complex filtering
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --vendor DATADOMAIN

# Date range filtering
ppdm-cli activities list --filter "createdAt ge '2026-03-16T00:00:00Z'"
```

## 🐳 Container Deployment

### Kubernetes
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
        image: quay.io/delldps/ppdm-cli:20.1.0.0-13
        command: ["ppdm-cli", "infrastructure-objects", "list"]
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

### OpenShift
```bash
# Apply OpenShift manifests
oc apply -f Documentation/deployment/openshift/imagestream.yaml
oc apply -f Documentation/deployment/openshift/deployment.yaml

# Check deployment
oc get pods -l app=ppdm-cli
```

### Local Container
```bash
# Run with environment variables
podman run --rm \
  -e PPDM_HOST=ppdm.example.com \
  -e PPDM_USERNAME=admin \
  -e PPDM_PASSWORD=password \
  quay.io/delldps/ppdm-cli:20.1.0.0-13 infrastructure-objects list --category STORAGE_SYSTEM
```

## 🔧 Advanced Features

### Debug Mode
```bash
# Enable debug output
ppdm-cli --debug infrastructure-objects list

# Debug with specific host
ppdm-cli --host staging --debug activities show "activity-id"

# Debug API calls
ppdm-cli --debug curl --endpoint infrastructure-objects --method GET --apiver api/v3
```

### Multi-Architecture Support
```bash
# Pull architecture-specific image
podman pull quay.io/delldps/ppdm-cli:20.1.0.0-13-amd64
podman pull quay.io/delldps/ppdm-cli:20.1.0.0-13-arm64

# Multi-architecture manifest
podman pull quay.io/delldps/ppdm-cli:20.1.0.0-13
```

### Category Filtering (New Feature)
```bash
# Filter by infrastructure object categories
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM
ppdm-cli infrastructure-objects list --category NETWORK_HOST
ppdm-cli infrastructure-objects list --category APPLICATION_HOST

# Tab completion for categories
ppdm-cli infrastructure-objects list --category <TAB>

# Combined filtering
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --vendor DATADOMAIN
```

### JSON-Raw Output
```bash
# Get raw API response (strict compliance)
ppdm-cli infrastructure-objects list --output json-raw

# Use in automation scripts
ppdm-cli infrastructure-objects list --output json-raw | jq '.content[] | .name'

# Clean YAML output for automation
ppdm-cli infrastructure-objects list --output yaml-raw 2>/dev/null | yq '.content[].name'
```

## 📈 Automation Examples

### Bash Script
```bash
#!/bin/bash
# Infrastructure inventory script

HOSTS=("staging" "production")
DATE=$(date +%Y%m%d)
OUTPUT_DIR="inventory-$DATE"

mkdir -p "$OUTPUT_DIR"

for host in "${HOSTS[@]}"; do
    echo "Collecting from $host..."
    ppdm-cli --host "$host" infrastructure-objects list --output json > "$OUTPUT_DIR/infra-objects-$host.json"
    ppdm-cli --host "$host" activities list --output json > "$OUTPUT_DIR/activities-$host.json"
    ppdm-cli --host "$host" infrastructure-objects list --category STORAGE_SYSTEM --output csv > "$OUTPUT_DIR/storage-systems-$host.csv"
done

echo "Inventory completed in $OUTPUT_DIR"
```

### Python Script
```python
#!/usr/bin/env python3
import subprocess
import json

def get_infrastructure_objects(host=None, category=None):
    cmd = "ppdm-cli infrastructure-objects list --output json"
    if host:
        cmd += f" --host {host}"
    if category:
        cmd += f" --category {category}"
    
    result = subprocess.run(cmd, shell=True, capture_output=True, text=True)
    return json.loads(result.stdout)

# Get infrastructure object summary
objects = get_infrastructure_objects()
summary = {}
for obj in objects['content']:
    for category in obj.get('categories', []):
        summary[category] = summary.get(category, 0) + 1

print("Infrastructure Object Summary by Category:")
for category, count in summary.items():
    print(f"{category}: {count}")

# Get storage systems specifically
storage_systems = get_infrastructure_objects(category="STORAGE_SYSTEM")
print(f"\nStorage Systems: {len(storage_systems['content'])}")
for system in storage_systems['content'][:5]:  # Show first 5
    print(f"  - {system['name']} ({system['vendor']})")
```

## 🚨 Troubleshooting

### Common Issues

#### Connection Problems
```bash
# Test connection
ppdm-cli --debug infrastructure-objects list

# Check environment variables
env | grep PPDM_

# Verify configuration
cat ~/.ppdm/config.json

# Test API access directly
ppdm-cli curl --endpoint infrastructure-objects --method GET --apiver api/v3 --pageSize 1
```

#### Authentication Issues
```bash
# Test credentials
ppdm-cli infrastructure-objects list

# Check permissions
ppdm-cli activities list --filter 'top 1'

# Test with specific host
ppdm-cli --host production infrastructure-objects list
```

#### Container Issues
```bash
# Check container logs
kubectl logs deployment/ppdm-cli

# Debug container
kubectl exec -it deployment/ppdm-cli -- /bin/bash

# Test container with new command
podman run --rm quay.io/delldps/ppdm-cli:20.1.0.0-13 infrastructure-objects list --help
```

## 📚 Additional Resources

### Development Documentation
- [Unified Skillset](../PPDM-UNIFIED-SKILLSET.md) - Development standards and patterns
- [Project Cleanup Standards](../PPDM-UNIFIED-SKILLSET.md#-project-cleanup-standards) - Code organization

### API Documentation
- [API Reference](../docs/api/reference/RELEASE-20.1.0.0-12.md) - Complete API documentation
- [Examples](../docs/api/examples/RELEASE-20.1.0.0-12.md) - Automation examples

### Container Registry
- **Registry**: quay.io/delldps/ppdm-cli
- **Tags**: 
  - `20.1.0.0-13` - Latest release
  - `20.1.0.0-13-amd64` - AMD64 specific
  - `20.1.0.0-13-arm64` - ARM64 specific
  - `latest` - Latest version

---

**Version**: 20.1.0.0-13  
**Last Updated**: March 19, 2026  
**Documentation Structure**: Comprehensive command documentation with real test results
