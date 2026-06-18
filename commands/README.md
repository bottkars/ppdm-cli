# PPDM CLI Commands Documentation

## Overview

This directory contains detailed documentation for individual PPDM CLI commands. Each command document provides comprehensive examples, usage patterns, and troubleshooting guidance.

## Available Command Documentation

### Core Commands

#### [Activities](ACTIVITIES.md)
Monitor and manage PPDM activities including backups, restores, and other operations.
- List, filter, and monitor activities
- Real-time activity monitoring
- Batch operations (cancel, retry)
- Activity status and performance analysis

#### [Infrastructure Objects](INFRASTRUCTURE_OBJECTS.md)
Manage infrastructure objects discovered by PPDM with advanced filtering capabilities.
- List and filter infrastructure objects by category, type, vendor
- Category-based organization (9 categories supported)
- Tab completion for all categories and types
- Export and analysis capabilities

#### [Backup and Restore](BACKUP_RESTORE.md)
Comprehensive backup and restore operations for PPDM assets.
- Create backups with protection policies
- List and manage backup copies
- Perform restore operations
- Monitor backup and restore activities

#### [Curl](CURL.md)
Direct API access to any PPDM endpoint with full HTTP request control.
- Raw API access to all endpoints
- Support for all HTTP methods (GET, POST, PUT, DELETE, PATCH)
- Custom headers and request bodies
- API exploration and testing

### Management Commands

#### [Asset Management](ASSET_MANAGEMENT.md)
Manage PPDM assets including creation, updates, and deletion.
- List and filter assets by type
- Create and update asset configurations
- Asset discovery and inventory management
- Asset health and availability monitoring

#### [Protection Policies](PROTECTION_POLICIES.md)
Manage protection policies with enhanced V3 functionality.
- Create and configure protection policies
- Policy scheduling and retention management
- Policy activation and deactivation
- Policy performance analysis

#### [Storage Systems](STORAGE_SYSTEMS.md)
Manage storage systems and storage appliances.
- List and configure storage systems
- Storage system health monitoring
- Capacity management and reporting
- Storage system integration

#### [Inventory Sources](INVENTORY_SOURCES.md)
Manage inventory sources for asset discovery.
- Configure inventory sources (vCenter, hosts, etc.)
- Asset discovery and synchronization
- Inventory source health monitoring
- Discovery scheduling and management

#### [Hosts](HOSTS.md)
Manage PPDM hosts and host configurations.
- Host registration and management
- Host health and availability monitoring
- Host configuration updates
- Host-based asset discovery

### Monitoring Commands

#### [Health](HEALTH.md)
Monitor PPDM system health and status.
- System health checks
- Component health monitoring
- Health event management
- Health reporting and alerts

#### [Metrics](METRICS.md)
Retrieve and analyze PPDM system metrics.
- System performance metrics
- Resource usage monitoring
- Historical metric analysis
- Metric export and reporting

#### [Alerts](ALERTS.md)
Manage PPDM alerts and notifications.
- Alert listing and filtering
- Alert acknowledgment and management
- Alert escalation and notification
- Alert history and analysis

### Configuration Commands

#### [Config](CONFIG.md)
Manage PPDM CLI configuration and host settings.
- Multi-host configuration management
- Connection testing and validation
- Environment variable configuration
- Configuration backup and restore

#### [Credentials](CREDENTIALS.md)
Manage credentials for external systems and authentication.
- Credential creation and management
- Credential security and rotation
- Credential testing and validation
- Integration with external systems

#### [Identity Providers](IDENTITY_PROVIDERS.md)
Manage identity providers and authentication systems.
- LDAP and Active Directory integration
- SSO configuration and management
- User authentication and authorization
- Identity provider health monitoring

### Advanced Commands

#### [Kubernetes](KUBERNETES.md)
Manage Kubernetes clusters and containerized workloads.
- Kubernetes cluster registration
- Container workload protection
- Kubernetes namespace management
- Cluster health monitoring

#### [Data Domain MTrees](DATADOMAIN_MTREES.md)
Manage PowerProtect Data Domain MTrees and storage units.
- MTree creation and management
- Storage unit configuration
- Data Domain integration
- Capacity planning and monitoring

#### [Agent Management](AGENT_MANAGEMENT.md)
Manage PPDM agents for asset protection.
- Agent installation and configuration
- Agent health monitoring
- Agent updates and maintenance
- Agent-based asset discovery

## Quick Reference

### Common Command Patterns

#### Listing Resources
```bash
# List with filtering
ppdm-cli <command> list --filter 'status eq "ACTIVE"'

# List with pagination
ppdm-cli <command> list --page 1 --size 50

# List with output format
ppdm-cli <command> list --output json-raw
```

#### Creating Resources
```bash
# Create with dry-run
ppdm-cli <command> create --name "Test" --dry-run

# Create from file
ppdm-cli <command> create --config-file config.json
```

#### Monitoring Operations
```bash
# Real-time monitoring
ppdm-cli <command> monitor --filter 'status eq "RUNNING"'

# Status checks
ppdm-cli <command> status --id "resource-id"
```

### Output Formats

| Format | Use Case | Example |
|--------|----------|---------|
| `table` | Human-readable display | Default format |
| `json` | Structured data processing | `--output json` |
| `yaml` | Configuration files | `--output yaml` |
| `csv` | Data export and analysis | `--output csv` |
| `json-raw` | Automation scripts | `--output json-raw` |
| `yaml-raw` | Configuration automation | `--output yaml-raw` |

### Filtering Patterns

#### Status Filtering
```bash
--filter 'status eq "RUNNING"'
--filter 'status eq "COMPLETED"'
--filter 'status ne "FAILED"'
```

#### Time-based Filtering
```bash
--filter 'startTime ge "2024-01-01T00:00:00Z"'
--filter 'creationTime le "$(date -Iseconds)"'
```

#### Name-based Filtering
```bash
--filter 'name co "database"'
--filter 'name eq "specific-name"'
```

#### Combined Filtering
```bash
--filter 'status eq "ACTIVE" and type eq "BACKUP"'
--filter 'name co "server" or name co "database"'
```

## Best Practices

### 1. Use Tab Completion
Take advantage of shell tab completion:
```bash
ppdm-cli <command> --<flag> <TAB>
```

### 2. Filter at Source
Use API filters for better performance:
```bash
# Good: Filter at API level
ppdm-cli activities list --filter 'status eq "RUNNING"'

# Avoid: Client-side filtering
ppdm-cli activities list | grep RUNNING
```

### 3. Use Raw Formats for Automation
Use `json-raw` or `yaml-raw` for automation:
```bash
ppdm-cli activities list --output json-raw 2>/dev/null | jq '.content[].id'
```

### 4. Use Dry Run for Testing
Test operations before execution:
```bash
ppdm-cli backup create --asset-id "id" --policy-id "id" --dry-run
```

### 5. Use Debug Mode for Troubleshooting
Enable debug mode for detailed information:
```bash
ppdm-cli --debug activities list
```

## Troubleshooting

### Common Issues

#### Authentication Problems
```bash
# Test connection
ppdm-cli config test --host production

# Check configuration
ppdm-cli config list

# Use debug mode
ppdm-cli --debug activities list
```

#### Output Format Issues
```bash
# Use json-raw for clean output
ppdm-cli activities list --output json-raw 2>/dev/null

# Redirect stderr to avoid pollution
ppdm-cli activities list --output json-raw 2>/dev/null | jq '.'
```

#### Performance Issues
```bash
# Use pagination
ppdm-cli activities list --page 1 --size 50

# Use specific filters
ppdm-cli activities list --filter 'status eq "RUNNING"'
```

### Getting Help

#### Command Help
```bash
ppdm-cli <command> --help
ppdm-cli <command> <subcommand> --help
```

#### Global Help
```bash
ppdm-cli --help
ppdm-cli completion bash  # Install tab completion
```

#### Debug Information
```bash
ppdm-cli --debug <command>
ppdm-cli --dry-run <command>
```

## Integration Examples

### Bash Scripts
```bash
#!/bin/bash
# PPDM CLI automation script

# Check system status
ppdm-cli status check

# Monitor running activities
ppdm-cli activities monitor --filter 'status eq "RUNNING"' &

# Export data for analysis
ppdm-cli activities list --output csv > activities.csv
ppdm-cli infrastructure-objects list --output csv > infra_objects.csv
```

### Python Integration
```python
import subprocess
import json

# Get activities
result = subprocess.run([
    'ppdm-cli', 'activities', 'list', 
    '--output', 'json-raw'
], capture_output=True, text=True)

activities = json.loads(result.stdout)
for activity in activities['content']:
    print(f"Activity: {activity['name']}, Status: {activity['status']}")
```

### PowerShell Integration
```powershell
# Get PPDM activities
$activities = ppdm-cli activities list --output json-raw | ConvertFrom-Json

# Filter running activities
$runningActivities = $activities.content | Where-Object { $_.status -eq "RUNNING" }

# Display results
$runningActivities | ForEach-Object {
    Write-Host "Activity: $($_.name), Status: $($_.status)"
}
```

## Additional Resources

### Main Documentation
- [Command Reference](../COMMAND_REFERENCE.md) - Complete command reference
- [Test Results](../TEST_RESULTS.md) - Comprehensive test results
- [Main README](../README.md) - Project overview and setup

### API Documentation
- [PPDM API Documentation](https://developer.dell.com/apis/4198) - Official API docs
- [API Examples](../API_EXAMPLES.md) - API usage examples

### Community and Support
- [GitHub Repository](https://github.com/bottkars/agentic-ppdm-go) - Source code and issues
- [Change Log](../CHANGELOG.md) - Version history and changes

---

*For the complete command reference, see [COMMAND_REFERENCE.md](../COMMAND_REFERENCE.md).*
