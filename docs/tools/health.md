# Health Monitoring Tool

## Overview

The `health` command provides comprehensive monitoring of PPDM system health using both V2 and V3 API operations. It enables health check triggering, result viewing, and entity-level health monitoring for proactive system management.

## Features

### Comprehensive Health Monitoring
- **Health check types**: List all available health check types
- **Health check triggering**: Trigger health checks on demand
- **Health check results**: View health check results and status
- **Health entities**: Monitor health of system entities
- **Health events**: Track health events for entities
- **Multi-version support**: V2 and V3 API operations

### System Components Monitored
- **Storage systems**: Data Domain and other storage systems
- **Network connectivity**: Network health and connectivity
- **Database status**: Database health and performance
- **Service health**: PPDM service status
- **Resource utilization**: CPU, memory, and disk usage

## Available Commands

### V2 Health Checks
- `list-types` - List health check types (API: getHealthCheckTypes)
- `trigger` - Trigger health checks (API: triggerHealthCheck)
- `show-result` - Show health check result (API: getHealthCheckResult)

### V3 Health Monitoring
- `entities` - List health entities
- `events` - List health events for an entity
- `results` - Get health check results

## Usage

### List Health Check Types

```bash
# List all available health check types
ppdm-cli health list-types

# Output in JSON format
ppdm-cli health list-types --output json
```

### Trigger Health Checks

```bash
# Trigger all health checks
ppdm-cli health trigger

# Trigger specific health check type
ppdm-cli health trigger --type "STORAGE_SYSTEM"

# Trigger with debug mode
ppdm-cli --debug health trigger
```

### Show Health Check Results

```bash
# Show health check result by ID
ppdm-cli health show-result --id <health-check-id>

# Show result in JSON format
ppdm-cli health show-result --id <health-check-id> --output json

# Show result in YAML format
ppdm-cli health show-result --id <health-check-id> --output yaml
```

### Monitor Health Entities

```bash
# List all health entities
ppdm-cli health entities

# Filter by entity type
ppdm-cli health entities --filter 'type eq "STORAGE_SYSTEM"'

# Show in JSON format
ppdm-cli health entities --output json
```

### Monitor Health Events

```bash
# List health events for an entity
ppdm-cli health events --entity-id <entity-id>

# Filter by event severity
ppdm-cli health events --entity-id <entity-id> --filter 'severity eq "CRITICAL"'

# Show in JSON format
ppdm-cli health events --entity-id <entity-id> --output json
```

### Get Health Check Results

```bash
# Get all health check results
ppdm-cli health results

# Filter by status
ppdm-cli health results --filter 'status eq "FAILED"'

# Show in JSON format
ppdm-cli health results --output json
```

## Examples

### Basic Health Monitoring

```bash
# List available health check types
ppdm-cli health list-types

# Trigger health checks
ppdm-cli health trigger

# View results
ppdm-cli health results
```

### Specific Health Check

```bash
# Trigger specific health check
ppdm-cli health trigger --type "STORAGE_SYSTEM"

# Show specific result
ppdm-cli health show-result --id <health-check-id>
```

### Entity-Level Monitoring

```bash
# List all health entities
ppdm-cli health entities

# Monitor specific entity
ppdm-cli health events --entity-id <entity-id>

# Filter by entity type
ppdm-cli health entities --filter 'type eq "STORAGE_SYSTEM"'
```

### Advanced Filtering

```bash
# Filter results by status
ppdm-cli health results --filter 'status eq "FAILED"'

# Filter events by severity
ppdm-cli health events --entity-id <entity-id> --filter 'severity eq "CRITICAL"'

# Order by timestamp
ppdm-cli health results --orderby "timestamp desc"
```

### Export Health Data

```bash
# Export health check results to JSON
ppdm-cli health results --output json > health-results.json

# Export health events to CSV
ppdm-cli health events --entity-id <entity-id> --output csv > health-events.csv

# Export health entities to YAML
ppdm-cli health entities --output yaml > health-entities.yaml
```

## Use Cases

### 1. Regular Health Checks

```bash
# Daily health check
ppdm-cli health trigger

# Review results
ppdm-cli health results --filter 'status eq "FAILED"'
```

### 2. Storage System Health

```bash
# Monitor storage system health
ppdm-cli health entities --filter 'type eq "STORAGE_SYSTEM"'

# Check storage system events
ppdm-cli health events --entity-id <storage-entity-id>
```

### 3. Proactive Monitoring

```bash
# List all health entities
ppdm-cli health entities

# Monitor critical entities
ppdm-cli health entities --filter 'severity eq "CRITICAL"'

# Review recent health events
ppdm-cli health results --orderby "timestamp desc"
```

### 4. Troubleshooting

```bash
# Trigger health checks
ppdm-cli health trigger

# Review failed checks
ppdm-cli health results --filter 'status eq "FAILED"'

# Show detailed result
ppdm-cli health show-result --id <health-check-id>
```

### 5. Health Trend Analysis

```bash
# Export health results for analysis
ppdm-cli health results --output json > health-results.json

# Analyze trends over time
ppdm-cli health results --orderby "timestamp desc" --page-size 100
```

## Tips and Best Practices

### 1. Regular Health Monitoring
```bash
# Schedule regular health checks
ppdm-cli health trigger

# Review results immediately
ppdm-cli health results --filter 'status eq "FAILED"'
```

### 2. Entity-Specific Monitoring
```bash
# Monitor critical entities
ppdm-cli health entities --filter 'type eq "STORAGE_SYSTEM"'

# Track entity events
ppdm-cli health events --entity-id <entity-id>
```

### 3. Proactive Issue Detection
```bash
# Check for critical health issues
ppdm-cli health results --filter 'severity eq "CRITICAL"'

# Review recent events
ppdm-cli health results --orderby "timestamp desc"
```

### 4. Export for Analysis
```bash
# Export health data for external analysis
ppdm-cli health results --output json > health-results.json

# Create health reports
ppdm-cli health results --output csv > health-report.csv
```

### 5. Use Debug Mode for Troubleshooting
```bash
# Trigger health checks with debug mode
ppdm-cli --debug health trigger

# View detailed API calls
ppdm-cli --debug health show-result --id <health-check-id>
```

## Troubleshooting

### Health Check Not Triggering

```bash
# Check if health check type exists
ppdm-cli health list-types

# Use debug mode
ppdm-cli --debug health trigger

# Check PPDM connection
ppdm-cli status
```

### No Results Returned

```bash
# Check if health checks completed
ppdm-cli health results

# Try without filter
ppdm-cli health results

# Use debug mode
ppdm-cli --debug health results
```

### Entity Not Found

```bash
# List all entities
ppdm-cli health entities

# Check entity ID
ppdm-cli health entities --filter 'id eq "<entity-id>"'

# Use debug mode
ppdm-cli --debug health events --entity-id <entity-id>
```

### Filter Not Working

```bash
# Check filter syntax
ppdm-cli health results --filter 'status eq "FAILED"'

# Try without filter
ppdm-cli health results

# Use debug mode to see API calls
ppdm-cli --debug health results --filter 'status eq "FAILED"'
```

## Related Commands

- **monitoring-metrics** - Monitor system metrics and health status
- **monitor** - Real-time activity monitoring
- **alerts** - Manage PPDM alerts
- **status** - Check PPDM system status

## API Information

### V2 Health Checks
- **Operation ID**: getHealthCheckTypes
- **API Version**: v2
- **Endpoint**: GET /api/v2/health-check-types
- **API Reference**: ppdm-public-v2-20.3.0.0.yaml

- **Operation ID**: triggerHealthCheck
- **API Version**: v2
- **Endpoint**: POST /api/v2/health-checks
- **API Reference**: ppdm-public-v2-20.3.0.0.yaml

- **Operation ID**: getHealthCheckResult
- **API Version**: v2
- **Endpoint**: GET /api/v2/health-checks/{id}
- **API Reference**: ppdm-public-v2-20.3.0.0.yaml

### V3 Health Monitoring
- **Operation ID**: getHealthEntities
- **API Version**: v3
- **Endpoint**: GET /api/v3/health-entities
- **API Reference**: ppdm-public-v3-20.3.0.0.yaml

- **Operation ID**: getHealthEvents
- **API Version**: v3
- **Endpoint**: GET /api/v3/health-events
- **API Reference**: ppdm-public-v3-20.3.0.0.yaml

## Health Check Types

Common health check types include:
- **STORAGE_SYSTEM**: Storage system health
- **NETWORK**: Network connectivity
- **DATABASE**: Database status
- **SERVICE**: Service health
- **RESOURCE**: Resource utilization
- **BACKUP**: Backup operation health
- **REPLICATION**: Replication health
- **RECOVERY**: Recovery operation health

---

*For more information about other tools, see the [tools documentation index](README.md).*
