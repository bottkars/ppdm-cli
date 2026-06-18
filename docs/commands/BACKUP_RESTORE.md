# Backup and Restore Command Reference

## Overview

The `backup-restore` command provides comprehensive backup and restore operations for PowerProtect Data Manager. It enables users to create backups, manage backup copies, and perform restore operations with advanced filtering and monitoring capabilities.

## Commands

### list-copies

List backup copies for assets with comprehensive filtering options.

#### Syntax
```bash
ppdm-cli backup-restore list-copies --asset-id <asset-id> [flags]
```

#### Flags
- `--asset-id string` - Asset ID (required)
- `-f, --filter string` - Filter expression
- `--orderby string` - Order by field (e.g., "creationTime desc")
- `-o, --output string` - Output format (table, json, yaml, csv, json-raw, yaml-raw)
- `-h, --help` - Help for list-copies command

#### Examples

##### Basic Usage
```bash
# List backup copies for an asset
ppdm-cli backup-restore list-copies --asset-id "12345678-1234-1234-1234-123456789012"

# Show help
ppdm-cli backup-restore list-copies --help
```

##### Filtering Copies
```bash
# List active copies only
ppdm-cli backup-restore list-copies --asset-id "asset-id" --filter 'isActive eq true'

# List copies by creation date
ppdm-cli backup-restore list-copies --asset-id "asset-id" --filter 'creationTime ge "2024-01-01T00:00:00Z"'

# List copies by size
ppdm-cli backup-restore list-copies --asset-id "asset-id" --filter 'size gt 1073741824'  # > 1GB

# List expired copies
ppdm-cli backup-restore list-copies --asset-id "asset-id" --filter 'expirationTime lt "$(date -Iseconds)"'
```

##### Output Formats
```bash
# Table format (default)
ppdm-cli backup-restore list-copies --asset-id "asset-id"

# JSON format
ppdm-cli backup-restore list-copies --asset-id "asset-id" --output json

# CSV format for reporting
ppdm-cli backup-restore list-copies --asset-id "asset-id" --output csv > backup_copies.csv

# JSON-Raw for automation
ppdm-cli backup-restore list-copies --asset-id "asset-id" --output json-raw
```

##### Sorting
```bash
# Sort by creation time (newest first)
ppdm-cli backup-restore list-copies --asset-id "asset-id" --orderby "creationTime desc"

# Sort by size (largest first)
ppdm-cli backup-restore list-copies --asset-id "asset-id" --orderby "size desc"

# Sort by expiration time
ppdm-cli backup-restore list-copies --asset-id "asset-id" --orderby "expirationTime asc"
```

#### Output Fields

The table output includes the following fields:
- **ID** - Copy identifier
- **NAME** - Copy name
- **CREATION TIME** - When the copy was created
- **SIZE** - Copy size
- **STATUS** - Copy status (ACTIVE, EXPIRED, etc.)
- **RETENTION** - Retention period
- **LOCATION** - Storage location

### show-copy

Show detailed information about a specific backup copy.

#### Syntax
```bash
ppdm-cli backup-restore show-copy --copy-id <copy-id> [flags]
```

#### Flags
- `--copy-id string` - Copy ID (required)
- `-o, --output string` - Output format (table, json, yaml, csv, json-raw, yaml-raw)
- `-h, --help` - Help for show-copy command

#### Examples

##### Basic Usage
```bash
# Show copy details
ppdm-cli backup-restore show-copy --copy-id "copy-id"

# Show in JSON format
ppdm-cli backup-restore show-copy --copy-id "copy-id" --output json

# Show raw API response
ppdm-cli backup-restore show-copy --copy-id "copy-id" --output json-raw
```

### restore

Restore data from a backup copy to a target location.

#### Syntax
```bash
ppdm-cli backup-restore restore --copy-id <copy-id> --target-host <host> --target-path <path> [flags]
```

#### Flags
- `--copy-id string` - Copy ID to restore from (required)
- `--target-host string` - Target host for restore (required)
- `--target-path string` - Target path for restore (required)
- `--restore-type string` - Type of restore (FULL, INCREMENTAL)
- `--overwrite` - Overwrite existing files
- `--preserve-permissions` - Preserve original permissions
- `--dry-run` - Preview restore without executing
- `-h, --help` - Help for restore command

#### Examples

##### Basic Restore
```bash
# Restore to specific host and path
ppdm-cli backup-restore restore \
  --copy-id "copy-id" \
  --target-host "target-server" \
  --target-path "/restore/location"

# Full restore with overwrite
ppdm-cli backup-restore restore \
  --copy-id "copy-id" \
  --target-host "target-server" \
  --target-path "/restore/location" \
  --restore-type FULL \
  --overwrite
```

##### Advanced Restore Options
```bash
# Restore with permission preservation
ppdm-cli backup-restore restore \
  --copy-id "copy-id" \
  --target-host "target-server" \
  --target-path "/restore/location" \
  --preserve-permissions

# Dry run to preview restore
ppdm-cli backup-restore restore \
  --copy-id "copy-id" \
  --target-host "target-server" \
  --target-path "/restore/location" \
  --dry-run
```

### list-restored-copies

List restored copies with filtering options.

#### Syntax
```bash
ppdm-cli backup-restore list-restored-copies [flags]
```

#### Flags
- `-f, --filter string` - Filter expression
- `--orderby string` - Order by field
- `-o, --output string` - Output format (table, json, yaml, csv, json-raw, yaml-raw)
- `-h, --help` - Help for list-restored-copies command

#### Examples

##### Basic Usage
```bash
# List all restored copies
ppdm-cli backup-restore list-restored-copies

# Filter by source copy
ppdm-cli backup-restore list-restored-copies --filter 'sourceCopy/id eq "source-copy-id"'

# Filter by target host
ppdm-cli backup-restore list-restored-copies --filter 'targetHost eq "target-server"'

# Filter by restore date
ppdm-cli backup-restore list-restored-copies --filter 'restoreTime ge "2024-01-01T00:00:00Z"'
```

### show-restored-copy

Show detailed information about a restored copy.

#### Syntax
```bash
ppdm-cli backup-restore show-restored-copy --id <restored-id> [flags]
```

#### Flags
- `--id string` - Restored copy ID (required)
- `-o, --output string` - Output format (table, json, yaml, csv, json-raw, yaml-raw)
- `-h, --help` - Help for show-restored-copy command

#### Examples

##### Basic Usage
```bash
# Show restored copy details
ppdm-cli backup-restore show-restored-copy --id "restored-id"

# Show in JSON format
ppdm-cli backup-restore show-restored-copy --id "restored-id" --output json

# Show raw API response
ppdm-cli backup-restore show-restored-copy --id "restored-id" --output json-raw
```

## Backup Command

### create

Create a new backup operation for an asset.

#### Syntax
```bash
ppdm-cli backup create --asset-id <asset-id> --policy-id <policy-id> [flags]
```

#### Flags
- `--asset-id string` - Asset ID to backup (required)
- `--policy-id string` - Protection policy ID (required)
- `--name string` - Backup name
- `--description string` - Backup description
- `--priority string` - Backup priority (LOW, NORMAL, HIGH)
- `--dry-run` - Preview backup without executing
- `-h, --help` - Help for create command

#### Examples

##### Basic Backup
```bash
# Create backup with policy
ppdm-cli backup create \
  --asset-id "asset-id" \
  --policy-id "policy-id"

# Create backup with custom name
ppdm-cli backup create \
  --asset-id "asset-id" \
  --policy-id "policy-id" \
  --name "Custom Backup Name" \
  --description "Backup for system maintenance"
```

##### Advanced Backup Options
```bash
# High priority backup
ppdm-cli backup create \
  --asset-id "asset-id" \
  --policy-id "policy-id" \
  --priority HIGH

# Dry run to preview backup
ppdm-cli backup create \
  --asset-id "asset-id" \
  --policy-id "policy-id" \
  --dry-run
```

## Use Cases

### Daily Backup Operations

```bash
# Create daily backup
ppdm-cli backup create --asset-id "database-server-01" --policy-id "daily-backup-policy"

# Monitor backup progress
ppdm-cli activities monitor --filter 'type eq "BACKUP" and status eq "RUNNING"'

# Check backup copies
ppdm-cli backup-restore list-copies --asset-id "database-server-01"
```

### Restore Operations

```bash
# Find latest backup copy
ppdm-cli backup-restore list-copies --asset-id "database-server-01" --orderby "creationTime desc"

# Restore from latest copy
ppdm-cli backup-restore restore \
  --copy-id "latest-copy-id" \
  --target-host "recovery-server" \
  --target-path "/recovery/database"

# Monitor restore progress
ppdm-cli activities monitor --filter 'type eq "RESTORE" and status eq "RUNNING"'
```

### Backup Analysis

```bash
# Analyze backup sizes
ppdm-cli backup-restore list-copies --asset-id "asset-id" --output json | \
jq '.content[] | {name: .name, size: .size, creationTime: .creationTime}'

# Find expired copies
ppdm-cli backup-restore list-copies --asset-id "asset-id" --filter 'expirationTime lt "$(date -Iseconds)"'

# Export backup inventory
ppdm-cli backup-restore list-copies --asset-id "asset-id" --output csv > backup_inventory.csv
```

### Automation Scripts

```bash
#!/bin/bash
# Backup management script

ASSET_ID="database-server-01"
POLICY_ID="daily-backup-policy"

echo "=== Creating Backup ==="
ppdm-cli backup create --asset-id "$ASSET_ID" --policy-id "$POLICY_ID"

echo "=== Monitoring Backup ==="
ppdm-cli activities monitor --filter 'type eq "BACKUP" and status eq "RUNNING"'

echo "=== Listing Backup Copies ==="
ppdm-cli backup-restore list-copies --asset-id "$ASSET_ID" --output table
```

### Disaster Recovery

```bash
# Find all backups for critical assets
ppdm-cli backup-restore list-copies --asset-id "critical-server-01" --filter 'isActive eq true'

# Restore to disaster recovery site
ppdm-cli backup-restore restore \
  --copy-id "critical-backup-id" \
  --target-host "dr-server" \
  --target-path "/dr/restores" \
  --overwrite

# Verify restore
ppdm-cli backup-restore list-restored-copies --filter 'targetHost eq "dr-server"'
```

## Tips and Best Practices

### 1. Use Dry Run for Testing
Always test restore operations with dry run:
```bash
ppdm-cli backup-restore restore --copy-id "copy-id" --target-host "target" --target-path "/path" --dry-run
```

### 2. Monitor Operations
Use monitoring for real-time awareness:
```bash
ppdm-cli activities monitor --filter 'type eq "BACKUP" or type eq "RESTORE"'
```

### 3. Use Proper Filtering
Filter at the API level for better performance:
```bash
# Good: Filter at API level
ppdm-cli backup-restore list-copies --asset-id "asset-id" --filter 'isActive eq true'

# Avoid: Get all data then filter
ppdm-cli backup-restore list-copies --asset-id "asset-id" | grep ACTIVE
```

### 4. Export for Analysis
```bash
# Export backup inventory
ppdm-cli backup-restore list-copies --asset-id "asset-id" --output csv > backup_analysis.csv

# Export restored copies
ppdm-cli backup-restore list-restored-copies --output json > restore_history.json
```

### 5. Use JSON-Raw for Automation
```bash
ppdm-cli backup-restore list-copies --asset-id "asset-id" --output json-raw 2>/dev/null | jq '.content[].id'
```

## Troubleshooting

### Common Issues

#### 1. Backup Creation Fails
```bash
# Check asset and policy exist
ppdm-cli asset-management show --id "asset-id"
ppdm-cli protection-policies show --id "policy-id"

# Check asset availability
ppdm-cli asset-management list --filter 'id eq "asset-id" and availabilityStatus/value eq "AVAILABLE"'

# Use debug mode
ppdm-cli --debug backup create --asset-id "asset-id" --policy-id "policy-id"
```

#### 2. Restore Fails
```bash
# Check copy exists and is active
ppdm-cli backup-restore show-copy --copy-id "copy-id"

# Check target host availability
ppdm-cli hosts show --id "target-host-id"

# Check target path permissions
ppdm-cli backup-restore restore --copy-id "copy-id" --target-host "target" --target-path "/path" --dry-run
```

#### 3. No Copies Found
```bash
# Check asset has backups
ppdm-cli activities list --filter 'asset/id eq "asset-id" and type eq "BACKUP" and status eq "COMPLETED"'

# Check copy filter
ppdm-cli backup-restore list-copies --asset-id "asset-id" --filter 'isActive eq true'
```

### Debug Mode

Use debug mode for detailed information:
```bash
ppdm-cli --debug backup-restore restore --copy-id "copy-id" --target-host "target" --target-path "/path"
```

This will show:
- API endpoint details
- Request parameters
- Response timing
- Error details

## Copy Status Values

| Status | Description | Action Required |
|--------|-------------|-----------------|
| `ACTIVE` | Copy is available for restore | No action needed |
| `EXPIRED` | Copy has expired | Cannot restore from expired copy |
| `CORRUPTED` | Copy is corrupted | Investigation needed |
| `IN_PROGRESS` | Copy is being created | Monitor progress |
| `CANCELLED` | Copy creation was cancelled | Check cancellation reason |

## Restore Types

| Type | Description | Use Case |
|------|-------------|----------|
| `FULL` | Complete restore of all data | Full system recovery |
| `INCREMENTAL` | Restore changes since last backup | Quick recovery of recent changes |
| `SELECTIVE` | Restore specific files/folders | Partial recovery needs |

---

*For more information about other commands, see the [main command reference](../COMMAND_REFERENCE.md).*
