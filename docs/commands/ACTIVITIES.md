# Activities Command Reference

## Overview

The `activities` command provides comprehensive monitoring and management of PowerProtect Data Manager activities. It allows users to track backup, restore, and other operational activities with advanced filtering and real-time monitoring capabilities.

## Commands

### list

List all activities with comprehensive filtering options.

#### Syntax
```bash
ppdm-cli activities list [flags]
```

#### Flags
- `--filter string` - Filter expression (e.g., 'state eq "RUNNING"')
- `--state string` - Filter by activity state (COMPLETED, RUNNING, QUEUED, CANCELING, RETRYING)
- `--category string` - Filter by activity category
- `--type string` - Filter by activity type (TASK, JOB, JOB_GROUP)
- `--orderby string` - Order by field (e.g., "startedAt DESC")
- `-o, --output string` - Output format (table, json, yaml, csv, json-raw, yaml-raw)
- `--page int` - Page number for pagination
- `--page-size int` - Number of items per page (default 100)
- `-h, --help` - Help for list command

#### Examples

##### Basic Usage
```bash
# List all activities
ppdm-cli activities list

# Show help
ppdm-cli activities list --help
```

##### Status Filtering
```bash
# List running activities
ppdm-cli activities list --state "RUNNING"

# List completed activities
ppdm-cli activities list --state "COMPLETED"

# List failed activities
ppdm-cli activities list --result "FAILED"

# List cancelled activities
ppdm-cli activities list --state "CANCELING"

# List activities with warnings
ppdm-cli activities list --result "COMPLETED_WITH_EXCEPTIONS"
```

##### Type Filtering
```bash
# List backup activities
ppdm-cli activities list --category "BACKUP"

# List restore activities
ppdm-cli activities list --category "RESTORE"

# List replication activities
ppdm-cli activities list --category "REPLICATE"

# List discovery activities
ppdm-cli activities list --category "DISCOVERY"
```

##### Time-based Filtering
```bash
# List activities from today
ppdm-cli activities list --latest

# List activities with custom filter
ppdm-cli activities list --filter 'startedAt ge "2024-01-01T00:00:00Z"'

# List activities in date range
ppdm-cli activities list --filter 'startedAt ge "2024-01-01T00:00:00Z" and startedAt le "2024-01-31T23:59:59Z"'
```

##### Asset-based Filtering
```bash
# List activities for specific asset
ppdm-cli activities list --search "database"

# List activities with custom filter
ppdm-cli activities list --filter 'assetRef.name lk "%database%"'

# List activities for multiple assets
ppdm-cli activities list --filter 'assetRef.name lk "%server%" or assetRef.name lk "%database%"'
```

##### Combined Filtering
```bash
# Combine state and category filters
ppdm-cli activities list --state "RUNNING" --category "BACKUP"

# Complex filter with multiple conditions
ppdm-cli activities list --filter 'state eq "FAILED" and category eq "BACKUP" and startedAt ge "2024-01-01T00:00:00Z"'

# Filter by policy name
ppdm-cli activities list --filter 'protectionPolicy.name eq "Daily Backup"'
```

##### Output Formats
```bash
# Table format (default)
ppdm-cli activities list

# JSON format
ppdm-cli activities list --output json

# YAML format
ppdm-cli activities list --output yaml

# CSV format for reporting
ppdm-cli activities list --output csv > activities.csv

# JSON-Raw for automation
ppdm-cli activities list --output json-raw

# YAML-Raw for automation
ppdm-cli activities list --output yaml-raw
```

##### Pagination and Sorting
```bash
# Custom page size
ppdm-cli activities list --page 1 --page-size 50

# Order by start time (newest first)
ppdm-cli activities list --orderby "startedAt DESC"

# Order by activity name
ppdm-cli activities list --orderby "name ASC"

# Order by state
ppdm-cli activities list --orderby "state ASC"
```

#### Output Fields

The table output includes the following fields:
- **ID** - Activity identifier
- **NAME** - Activity name
- **TYPE** - Activity type (BACKUP, RESTORE, etc.)
- **STATUS** - Current status (RUNNING, COMPLETED, FAILED, etc.)
- **START TIME** - When the activity started
- **END TIME** - When the activity completed (if applicable)
- **DURATION** - Total duration of the activity
- **ASSET** - Associated asset name
- **POLICY** - Protection policy name (if applicable)

#### Sample Output

```
ID                                    NAME                    TYPE    STATUS      START TIME              END TIME                DURATION   ASSET                        POLICY
12345678-1234-1234-1234-123456789012  Daily Backup - DB01     BACKUP   COMPLETED   2024-01-15T02:00:00Z   2024-01-15T02:15:30Z   15m30s     database-server-01        Daily Backup Policy
87654321-4321-4321-4321-210987654321  Restore - File01        RESTORE  RUNNING     2024-01-15T10:30:00Z   -                       5m45s      file-server-02           -
```

### show

Show detailed information about a specific activity.

#### Syntax
```bash
ppdm-cli activities show --id <activity-id> [flags]
```

#### Flags
- `--id string` - Activity ID (required)
- `-o, --output string` - Output format (table, json, yaml, csv, json-raw, yaml-raw)
- `-h, --help` - Help for show command

#### Examples

##### Basic Usage
```bash
# Show activity details
ppdm-cli activities show --id "12345678-1234-1234-1234-123456789012"

# Show in JSON format
ppdm-cli activities show --id "12345678-1234-1234-1234-123456789012" --output json

# Show raw API response
ppdm-cli activities show --id "12345678-1234-1234-1234-123456789012" --output json-raw
```

### monitor

Real-time monitoring of activities with live updates.

#### Syntax
```bash
ppdm-cli activities monitor [flags]
```

#### Flags
- `--filter string` - Filter expression for monitoring
- `--refresh-interval duration` - Refresh interval (default 5s)
- `-o, --output string` - Output format (table, json)
- `-h, --help` - Help for monitor command

#### Examples

##### Basic Monitoring
```bash
# Monitor all activities
ppdm-cli activities monitor

# Monitor running activities only
ppdm-cli activities monitor --filter 'status eq "RUNNING"'

# Monitor backup activities
ppdm-cli activities monitor --filter 'type eq "BACKUP"'

# Monitor with custom refresh interval
ppdm-cli activities monitor --refresh-interval 10s

# Monitor in JSON format
ppdm-cli activities monitor --output json
```

##### Advanced Monitoring
```bash
# Monitor failed activities
ppdm-cli activities monitor --filter 'status eq "FAILED"'

# Monitor specific asset activities
ppdm-cli activities monitor --filter 'asset/name co "database"'

# Monitor long-running activities
ppdm-cli activities monitor --filter 'duration gt "1h"'
```

### cancel

Cancel a running activity.

#### Syntax
```bash
ppdm-cli activities cancel --id <activity-id> [flags]
```

#### Flags
- `--id string` - Activity ID (required)
- `--force` - Force cancellation without confirmation
- `-h, --help` - Help for cancel command

#### Examples

##### Basic Cancellation
```bash
# Cancel an activity
ppdm-cli activities cancel --id "12345678-1234-1234-1234-123456789012"

# Force cancellation without confirmation
ppdm-cli activities cancel --id "12345678-1234-1234-1234-123456789012" --force
```

### retry

Retry a failed activity.

#### Syntax
```bash
ppdm-cli activities retry --id <activity-id> [flags]
```

#### Flags
- `--id string` - Activity ID (required)
- `--force` - Force retry without confirmation
- `-h, --help` - Help for retry command

#### Examples

##### Basic Retry
```bash
# Retry a failed activity
ppdm-cli activities retry --id "12345678-1234-1234-1234-123456789012"

# Force retry without confirmation
ppdm-cli activities retry --id "12345678-1234-1234-1234-123456789012" --force
```

## Activity Types

### Common Activity Types

| Type | Description | Typical Use |
|------|-------------|-------------|
| `BACKUP` | Backup operations | Creating backup copies |
| `RESTORE` | Restore operations | Restoring from backups |
| `REPLICATION` | Replication operations | Copying data between systems |
| `DISCOVERY` | Discovery operations | Finding new assets |
| `VERIFICATION` | Verification operations | Verifying backup integrity |
| `CLEANUP` | Cleanup operations | Removing expired data |
| `MIGRATION` | Migration operations | Moving data between systems |

### Activity Status Values

| Status | Description | Next Action |
|--------|-------------|-------------|
| `RUNNING` | Activity currently executing | Monitor progress |
| `COMPLETED` | Activity finished successfully | Review results |
| `FAILED` | Activity failed | Investigate and retry |
| `CANCELED` | Activity was cancelled | Check cancellation reason |
| `COMPLETED_WITH_WARNINGS` | Completed with warnings | Review warnings |
| `PENDING` | Activity waiting to start | Check queue status |
| `PAUSED` | Activity temporarily paused | Resume or cancel |

## Use Cases

### Daily Operations

```bash
# Check today's activities
ppdm-cli activities list --filter 'startTime ge "$(date -d 'today' -Iseconds)"'

# Monitor running backups
ppdm-cli activities monitor --filter 'type eq "BACKUP" and status eq "RUNNING"'

# Check for failed activities
ppdm-cli activities list --filter 'status eq "FAILED"'
```

### Troubleshooting

```bash
# Investigate failed activities
ppdm-cli activities list --filter 'status eq "FAILED"' --output json

# Show detailed error information
ppdm-cli activities show --id "failed-activity-id" --output json-raw

# Monitor problematic activities
ppdm-cli activities monitor --filter 'status eq "FAILED" or status eq "COMPLETED_WITH_WARNINGS"'
```

### Reporting

```bash
# Generate daily activity report
ppdm-cli activities list --filter 'startTime ge "$(date -d 'today' -Iseconds)"' --output csv > daily_activities.csv

# Generate weekly summary
ppdm-cli activities list --filter 'startTime ge "$(date -d '1 week ago' -Iseconds)"' --output json > weekly_activities.json

# Export failed activities for analysis
ppdm-cli activities list --filter 'status eq "FAILED"' --output csv > failed_activities.csv
```

### Automation Scripts

```bash
#!/bin/bash
# Activity monitoring script

echo "=== Running Activities ==="
ppdm-cli activities list --filter 'status eq "RUNNING"'

echo "=== Failed Activities (Last 24 Hours) ==="
ppdm-cli activities list --filter 'status eq "FAILED" and startTime ge "$(date -d '1 day ago' -Iseconds)"'

echo "=== Long Running Activities (> 2 hours) ==="
ppdm-cli activities list --filter 'status eq "RUNNING" and duration gt "2h"'

# Export to JSON for processing
ppdm-cli activities list --output json-raw > current_activities.json
```

### Performance Analysis

```bash
# Analyze backup performance
ppdm-cli activities list --filter 'type eq "BACKUP" and status eq "COMPLETED"' --output json | \
jq '.content[] | {name: .name, duration: .duration, size: .result.size}'

# Find slow activities
ppdm-cli activities list --filter 'duration gt "4h"' --output table

# Analyze success rates
ppdm-cli activities list --filter 'type eq "BACKUP"' --output json | \
jq '.content | group_by(.status) | map({status: .[0].status, count: length})'
```

## Batch Operations

### activity-cancellations-batch

Cancel multiple activities in batch operations.

#### Examples
```bash
# Cancel all running activities
ppdm-cli activity-cancellations-batch cancel --filter 'status eq "RUNNING"'

# Cancel activities by type
ppdm-cli activity-cancellations-batch cancel --filter 'type eq "BACKUP" and status eq "RUNNING"'

# Cancel specific activities
ppdm-cli activity-cancellations-batch cancel --activity-ids "id1,id2,id3"

# Dry run to preview
ppdm-cli activity-cancellations-batch cancel --filter 'status eq "RUNNING"' --dry-run
```

### activity-retries-batch

Retry multiple failed activities in batch operations.

#### Examples
```bash
# Retry all failed activities
ppdm-cli activity-retries-batch retry --filter 'status eq "FAILED"'

# Retry failed backups
ppdm-cli activity-retries-batch retry --filter 'status eq "FAILED" and type eq "BACKUP"'

# Retry specific activities
ppdm-cli activity-retries-batch retry --activity-ids "id1,id2,id3"

# Dry run to preview
ppdm-cli activity-retries-batch retry --filter 'status eq "FAILED"' --dry-run
```

## Tips and Best Practices

### 1. Use Efficient Filtering
Filter at the API level for better performance:
```bash
# Good: Filter at API level
ppdm-cli activities list --filter 'status eq "RUNNING"'

# Avoid: Get all data then filter
ppdm-cli activities list | grep RUNNING
```

### 2. Use Raw Formats for Automation
Use `json-raw` for clean automation output:
```bash
ppdm-cli activities list --output json-raw 2>/dev/null | jq '.content[].status'
```

### 3. Monitor Critical Activities
Use monitoring for real-time awareness:
```bash
# Monitor critical activities
ppdm-cli activities monitor --filter 'status eq "FAILED" or (status eq "RUNNING" and duration gt "2h")'
```

### 4. Use Pagination for Large Datasets
```bash
# Process activities in batches
ppdm-cli activities list --page 1 --size 100 --filter 'status eq "COMPLETED"'
```

### 5. Export for Analysis
```bash
# Export to CSV for Excel analysis
ppdm-cli activities list --output csv > activities_analysis.csv

# Export to JSON for custom processing
ppdm-cli activities list --output json-raw > activities_raw.json
```

## Troubleshooting

### Common Issues

#### 1. No Activities Found
```bash
# Check time range
ppdm-cli activities list --filter 'startTime ge "2024-01-01T00:00:00Z"'

# Try without filters first
ppdm-cli activities list

# Check filter syntax
ppdm-cli activities list --filter 'status eq "COMPLETED"'
```

#### 2. Authentication Issues
```bash
# Test connection
ppdm-cli config test --host production

# Use debug mode
ppdm-cli --debug activities list
```

#### 3. Performance Issues
```bash
# Use pagination
ppdm-cli activities list --page 1 --size 50

# Use specific filters
ppdm-cli activities list --filter 'status eq "RUNNING"'
```

### Debug Mode

Use debug mode for detailed information:
```bash
ppdm-cli --debug activities list --filter 'status eq "RUNNING"'
```

This will show:
- API endpoint details
- Request parameters
- Response timing
- Error details

---

*For more information about other commands, see the [main command reference](../COMMAND_REFERENCE.md).*
