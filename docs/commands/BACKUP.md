# Backup Command Reference

## Overview

The `backup` command provides comprehensive backup operations for PowerProtect Data Manager. It enables users to create manual backups, manage server disaster recovery backups, configure VM backup settings, and reconcile backup metadata.

## Available Commands

- `create` - Create a manual backup for assets (API: CreateProtection)
- `server-dr` - Manage server disaster recovery backups (API: getServerDrBackups, createServerDrBackup, deleteServerDrBackup, getServerDrBackup, updateServerDrBackup)
- `vm-settings` - Manage VM backup settings (API: getVmBackupSettings, updateVmBackupSettings)
- `reconcile` - Reconcile backup metadata (API: server-disaster-recovery-backup-reconciliation)

## API Endpoints & Operation IDs

| Command | API Endpoint | Method | Operation ID | API Version |
|----------|-------------|--------|--------------|-------------|
| `backup create` | `/api/v3/protections` | POST | CreateProtection | v3 |
| `backup server-dr list` | `/api/v2/server-disaster-recovery-backups` | GET | getServerDrBackups | v2 |
| `backup server-dr create` | `/api/v2/server-disaster-recovery-backups` | POST | createServerDrBackup | v2 |
| `backup server-dr delete` | `/api/v2/server-disaster-recovery-backups/{id}` | DELETE | deleteServerDrBackup | v2 |
| `backup server-dr get` | `/api/v2/server-disaster-recovery-backups/{id}` | GET | getServerDrBackup | v2 |
| `backup server-dr update` | `/api/v2/server-disaster-recovery-backups/{id}` | PUT | updateServerDrBackup | v2 |
| `backup vm-settings get` | `/api/v2/common-settings/VM_BACKUP_SETTING` | GET | getVmBackupSettings | v2 |
| `backup vm-settings update` | `/api/v2/common-settings/VM_BACKUP_SETTING` | PUT | updateVmBackupSettings | v2 |
| `backup reconcile` | `/api/v2/server-disaster-recovery-backup-reconciliation` | POST | server-disaster-recovery-backup-reconciliation | v2 |

## Examples

### Create Manual Backup

```bash
# Backup specific assets (only protected assets will be processed)
ppdm-cli backup create --assets asset1,asset2

# Backup specific assets with custom policy override
ppdm-cli backup create --assets asset1,asset2 --policy policy-id

# Backup all PROTECTED assets in a policy (unprotected assets automatically skipped)
ppdm-cli backup create --policy policy-id --all-assets

# Create backup with custom settings
ppdm-cli backup create --assets asset1 --backup-level FULL --retention-days 30

# Create incremental backup
ppdm-cli backup create --assets asset1 --backup-level INCREMENTAL

# Create backup with custom objective
ppdm-cli backup create --assets asset1 --objective objective-id
```

### Server Disaster Recovery Backups

```bash
# List all server DR backups
ppdm-cli backup server-dr list

# Create a server DR backup
ppdm-cli backup server-dr create --name "daily-backup" --asset-id "asset-id"

# Get specific server DR backup details
ppdm-cli backup server-dr get --id backup-123

# Update server DR backup configuration
ppdm-cli backup server-dr update --id backup-123 --name "updated-backup"

# Delete a server DR backup
ppdm-cli backup server-dr delete --id backup-123
```

### VM Backup Settings

```bash
# Get VM backup settings
ppdm-cli backup vm-settings get

# Update VM backup settings
ppdm-cli backup vm-settings update --setting value

# Update VM backup settings from file
ppdm-cli backup vm-settings update --from-file settings.json
```

### Backup Reconciliation

```bash
# Reconcile backup metadata for a specific backup
ppdm-cli backup reconcile --backup-id backup-123

# Reconcile all backup metadata
ppdm-cli backup reconcile --all
```

## Output Formats

### Table Format (Default)
```bash
ppdm-cli backup create --assets asset1
```

### JSON Format
```bash
ppdm-cli backup create --assets asset1 --output json
```

### YAML Format
```bash
ppdm-cli backup create --assets asset1 --output yaml
```

### CSV Format
```bash
ppdm-cli backup server-dr list --output csv > backups.csv
```

### JSON-Raw (Automation)
```bash
ppdm-cli backup create --assets asset1 --output json-raw
```

### YAML-Raw (Automation)
```bash
ppdm-cli backup create --assets asset1 --output yaml-raw
```

## Protection Status Behavior

The `backup create` command only processes assets with ProtectionStatus="PROTECTED":
- Only assets with ProtectionStatus="PROTECTED" will be backed up
- Unprotected assets will be skipped with warning messages
- Use --debug to see detailed filtering information
- Check unprotected assets: `ppdm-cli assets list --protection-status UNPROTECTED`

## Server Disaster Recovery Backup Flags

### create
- `--name string` - Backup name (required)
- `--asset-id string` - Asset ID (required)
- `--description string` - Backup description
- `--priority string` - Backup priority (LOW, NORMAL, HIGH)

### list
- `-f, --filter string` - Filter expression
- `--orderby string` - Order by field
- `-o, --output string` - Output format
- `-p, --page int` - Page number
- `-s, --page-size int` - Page size

### get
- `--id string` - Backup ID (required)
- `-o, --output string` - Output format

### update
- `--id string` - Backup ID (required)
- `--name string` - Backup name
- `--description string` - Backup description
- `--priority string` - Backup priority

### delete
- `--id string` - Backup ID (required)

## VM Backup Settings Flags

### get
- `-o, --output string` - Output format

### update
- `--setting string` - Setting value
- `--from-file string` - Load settings from JSON file

## Backup Reconciliation Flags

- `--backup-id string` - Backup ID to reconcile
- `--all` - Reconcile all backup metadata
- `--dry-run` - Preview reconciliation without executing

## Tips and Best Practices

### 1. Check Protection Status Before Backup
```bash
# Check which assets are protected
ppdm-cli assets list --protection-status PROTECTED

# Check which assets are unprotected
ppdm-cli assets list --protection-status UNPROTECTED
```

### 2. Use Appropriate Backup Levels
```bash
# Full backup for complete protection
ppdm-cli backup create --assets asset1 --backup-level FULL

# Incremental backup for frequent changes
ppdm-cli backup create --assets asset1 --backup-level INCREMENTAL

# Synthetic full for efficient storage
ppdm-cli backup create --assets asset1 --backup-level SYNTHETIC_FULL
```

### 3. Set Appropriate Retention
```bash
# Short-term retention (7 days)
ppdm-cli backup create --assets asset1 --retention-days 7

# Long-term retention (30 days)
ppdm-cli backup create --assets asset1 --retention-days 30
```

### 4. Use Debug Mode for Troubleshooting
```bash
ppdm-cli --debug backup create --assets asset1
```

### 5. Monitor Backup Progress
```bash
# Monitor running backups
ppdm-cli activities list --filter 'type eq "BACKUP" and status eq "RUNNING"'

# Monitor specific backup
ppdm-cli activities show --id activity-id
```

## Troubleshooting

### Common Issues

#### 1. Assets Not Backed Up
```bash
# Check protection status
ppdm-cli assets list --protection-status UNPROTECTED

# Assign asset to protection policy first
ppdm-cli protection-policies asset-assignments create --policy-id policy-id --asset-id asset-id
```

#### 2. Backup Creation Fails
```bash
# Check asset availability
ppdm-cli assets show --id asset-id

# Check policy exists
ppdm-cli protection-policies show --id policy-id

# Use debug mode
ppdm-cli --debug backup create --assets asset1
```

#### 3. Server DR Backup Issues
```bash
# Check asset exists
ppdm-cli assets show --id asset-id

# List existing server DR backups
ppdm-cli backup server-dr list

# Check backup details
ppdm-cli backup server-dr get --id backup-id
```

## Related Commands

- **activities** - Monitor backup activities
- **assets** - Manage protected assets
- **protection-policies** - Manage backup policies
- **copies** - Analyze backup copies
- **backup-restore** - Restore from backups

---

*For more information about other commands, see the [main command reference](../COMMAND_REFERENCE.md).*