# Monitoring Metrics Tool

## Overview

The `monitoring-metrics` command provides comprehensive visibility into PowerProtect Data Manager system performance, health status, and operational metrics. It enables proactive monitoring, capacity planning, and troubleshooting through multiple metric categories.

## Features

### Comprehensive Metrics Coverage
- **Activity metrics**: Monitor job status and performance
- **Alert metrics**: Track system notifications and alerts
- **Resource utilization**: Analyze system resource usage
- **SLA compliance**: Monitor service level agreement compliance
- **Storage systems**: Check storage capacity and performance
- **System health**: Review system health issues and metrics

### API Version Strategy
- **V3 endpoints**: Preferred for activity metrics
- **V2 endpoints**: Used for other comprehensive system monitoring
- **Consistent interface**: Unified command structure across all metrics

## Available Commands

- `activity-metrics` - Monitor activity metrics (API: getActivityMetrics)
- `alert-metrics` - Monitor alert metrics and notifications (API: getAlertMetrics)
- `resource-metrics` - Monitor resource utilization metrics (API: getResourceMetrics)
- `sla-metrics` - Monitor SLA compliance metrics (API: getSlaMetrics)
- `storage-system-metrics` - Monitor storage system metrics (API: getStorageSystemMetrics)
- `system-health-issues` - Monitor system health issues (API: getSystemHealthIssues)
- `system-health-metrics` - Monitor system health metrics (API: getSystemHealthMetrics)

## Usage

### Activity Metrics

```bash
# List all activity metrics (V3 preferred)
ppdm-cli monitoring-metrics activity-metrics

# Filter activity metrics by time range
ppdm-cli monitoring-metrics activity-metrics --from "2024-01-01T00:00:00Z" --to "2024-01-31T23:59:59Z"

# Output in JSON format
ppdm-cli monitoring-metrics activity-metrics --output json
```

### Alert Metrics

```bash
# Show alert metrics
ppdm-cli monitoring-metrics alert-metrics

# Filter by severity
ppdm-cli monitoring-metrics alert-metrics --filter 'severity eq "CRITICAL"'

# Output in CSV format
ppdm-cli monitoring-metrics alert-metrics --output csv > alerts.csv
```

### Resource Metrics

```bash
# Monitor resource utilization
ppdm-cli monitoring-metrics resource-metrics

# Filter by resource type
ppdm-cli monitoring-metrics resource-metrics --filter 'type eq "CPU"'

# Show in JSON format
ppdm-cli monitoring-metrics resource-metrics --output json
```

### SLA Metrics

```bash
# Check SLA compliance
ppdm-cli monitoring-metrics sla-metrics

# Filter by SLA status
ppdm-cli monitoring-metrics sla-metrics --filter 'status eq "COMPLIANT"'

# Output in YAML format
ppdm-cli monitoring-metrics sla-metrics --output yaml
```

### Storage System Metrics

```bash
# Monitor storage systems
ppdm-cli monitoring-metrics storage-system-metrics

# Filter by storage system type
ppdm-cli monitoring-metrics storage-system-metrics --filter 'type eq "DATADOMAIN"'

# Show in JSON format
ppdm-cli monitoring-metrics storage-system-metrics --output json
```

### System Health

```bash
# Review system health issues
ppdm-cli monitoring-metrics system-health-issues

# Monitor system health metrics
ppdm-cli monitoring-metrics system-health-metrics

# Filter by health status
ppdm-cli monitoring-metrics system-health-issues --filter 'status eq "CRITICAL"'
```

## Common Flags

| Flag | Short | Description | Default |
|------|-------|-------------|---------|
| `--filter` | `-f` | Filter expression | No filter |
| `--orderby` | | Order by field | No ordering |
| `--output` | `-o` | Output format (table, json, yaml, csv, json-raw) | table |
| `--page` | `-p` | Page number for pagination | 1 |
| `--page-size` | `-s` | Page size for pagination | 100 |

## Examples

### Monitor All Metrics

```bash
# Activity metrics
ppdm-cli monitoring-metrics activity-metrics

# Alert metrics
ppdm-cli monitoring-metrics alert-metrics

# Resource metrics
ppdm-cli monitoring-metrics resource-metrics

# SLA metrics
ppdm-cli monitoring-metrics sla-metrics

# Storage system metrics
ppdm-cli monitoring-metrics storage-system-metrics

# System health metrics
ppdm-cli monitoring-metrics system-health-metrics
```

### Filter and Sort Metrics

```bash
# Filter activity metrics by time range
ppdm-cli monitoring-metrics activity-metrics --from "2024-01-01T00:00:00Z" --to "2024-01-31T23:59:59Z"

# Filter alert metrics by severity
ppdm-cli monitoring-metrics alert-metrics --filter 'severity eq "CRITICAL"'

# Order resource metrics by utilization
ppdm-cli monitoring-metrics resource-metrics --orderby "utilization desc"

# Filter SLA metrics by status
ppdm-cli monitoring-metrics sla-metrics --filter 'status eq "NON_COMPLIANT"'
```

### Export Metrics

```bash
# Export activity metrics to JSON
ppdm-cli monitoring-metrics activity-metrics --output json > activity-metrics.json

# Export alert metrics to CSV
ppdm-cli monitoring-metrics alert-metrics --output csv > alert-metrics.csv

# Export resource metrics to YAML
ppdm-cli monitoring-metrics resource-metrics --output yaml > resource-metrics.yaml
```

## Use Cases

### 1. Proactive Monitoring

```bash
# Monitor system health regularly
ppdm-cli monitoring-metrics system-health-metrics

# Check for critical alerts
ppdm-cli monitoring-metrics alert-metrics --filter 'severity eq "CRITICAL"'
```

### 2. Capacity Planning

```bash
# Monitor storage system capacity
ppdm-cli monitoring-metrics storage-system-metrics

# Analyze resource utilization trends
ppdm-cli monitoring-metrics resource-metrics --orderby "utilization desc"
```

### 3. SLA Compliance

```bash
# Check SLA compliance status
ppdm-cli monitoring-metrics sla-metrics

# Identify non-compliant SLAs
ppdm-cli monitoring-metrics sla-metrics --filter 'status eq "NON_COMPLIANT"'
```

### 4. Performance Analysis

```bash
# Analyze activity performance
ppdm-cli monitoring-metrics activity-metrics --from "2024-01-01T00:00:00Z" --to "2024-01-31T23:59:59Z"

# Monitor resource utilization
ppdm-cli monitoring-metrics resource-metrics
```

### 5. Troubleshooting

```bash
# Check system health issues
ppdm-cli monitoring-metrics system-health-issues

# Review recent alerts
ppdm-cli monitoring-metrics alert-metrics --orderby "timestamp desc"
```

## Tips and Best Practices

### 1. Regular Health Checks
```bash
# Daily health check
ppdm-cli monitoring-metrics system-health-metrics

# Weekly SLA review
ppdm-cli monitoring-metrics sla-metrics
```

### 2. Capacity Planning
```bash
# Monitor storage capacity monthly
ppdm-cli monitoring-metrics storage-system-metrics

# Track resource utilization trends
ppdm-cli monitoring-metrics resource-metrics --orderby "utilization desc"
```

### 3. Alert Management
```bash
# Check for critical alerts regularly
ppdm-cli monitoring-metrics alert-metrics --filter 'severity eq "CRITICAL"'

# Review alert history
ppdm-cli monitoring-metrics alert-metrics --orderby "timestamp desc"
```

### 4. Performance Monitoring
```bash
# Monitor activity performance
ppdm-cli monitoring-metrics activity-metrics

# Analyze trends over time
ppdm-cli monitoring-metrics activity-metrics --from "2024-01-01T00:00:00Z" --to "2024-01-31T23:59:59Z"
```

### 5. Export for Analysis
```bash
# Export metrics for external analysis
ppdm-cli monitoring-metrics activity-metrics --output json > metrics.json

# Create CSV reports
ppdm-cli monitoring-metrics alert-metrics --output csv > alerts.csv
```

## Troubleshooting

### No Metrics Returned

```bash
# Check if metrics exist for time range
ppdm-cli monitoring-metrics activity-metrics --from "2024-01-01T00:00:00Z" --to "2024-01-31T23:59:59Z"

# Try without time filter
ppdm-cli monitoring-metrics activity-metrics

# Use debug mode
ppdm-cli --debug monitoring-metrics activity-metrics
```

### Filter Not Working

```bash
# Check filter syntax
ppdm-cli monitoring-metrics alert-metrics --filter 'severity eq "CRITICAL"'

# Try without filter
ppdm-cli monitoring-metrics alert-metrics

# Use debug mode to see API calls
ppdm-cli --debug monitoring-metrics alert-metrics --filter 'severity eq "CRITICAL"'
```

### Performance Issues

```bash
# Reduce page size
ppdm-cli monitoring-metrics activity-metrics --page-size 50

# Use specific time range
ppdm-cli monitoring-metrics activity-metrics --from "2024-01-01T00:00:00Z" --to "2024-01-31T23:59:59Z"

# Use pagination
ppdm-cli monitoring-metrics activity-metrics --page 1 --page-size 50
```

## Related Commands

- **activities** - Monitor PPDM activities
- **monitor** - Real-time activity monitoring
- **health** - Monitor PPDM system health
- **metrics** - Retrieve PPDM metrics
- **alerts** - Manage PPDM alerts

## API Information

### Activity Metrics
- **Operation ID**: getActivityMetrics
- **API Version**: v3
- **Endpoint**: GET /api/v3/activity-metrics
- **API Reference**: ppdm-public-v3-20.3.0.0.yaml

### Alert Metrics
- **Operation ID**: getAlertMetrics
- **API Version**: v2
- **Endpoint**: GET /api/v2/alert-metrics
- **API Reference**: ppdm-public-v2-20.3.0.0.yaml

### Resource Metrics
- **Operation ID**: getResourceMetrics
- **API Version**: v2
- **Endpoint**: GET /api/v2/resource-metrics
- **API Reference**: ppdm-public-v2-20.3.0.0.yaml

### SLA Metrics
- **Operation ID**: getSlaMetrics
- **API Version**: v2
- **Endpoint**: GET /api/v2/sla-metrics
- **API Reference**: ppdm-public-v2-20.3.0.0.yaml

### Storage System Metrics
- **Operation ID**: getStorageSystemMetrics
- **API Version**: v2
- **Endpoint**: GET /api/v2/storage-system-metrics
- **API Reference**: ppdm-public-v2-20.3.0.0.yaml

### System Health Metrics
- **Operation ID**: getSystemHealthMetrics
- **API Version**: v2
- **Endpoint**: GET /api/v2/system-health-metrics
- **API Reference**: ppdm-public-v2-20.3.0.0.yaml

---

*For more information about other tools, see the [tools documentation index](README.md).*
