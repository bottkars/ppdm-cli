# Protection Policies Command Reference

## Overview

The `protection-policies` command provides comprehensive management of PowerProtect Data Manager protection policies using V3 API operations. It allows users to create, list, update, delete, and manage backup and protection policies with enhanced functionality including batch operations, policy settings management, and improved filtering capabilities.

## Commands

### list

List all protection policies with comprehensive filtering options.

#### Syntax
```bash
ppdm-cli protection-policies list [flags]
```

#### Flags
- `-f, --filter string` - Filter expression (e.g., "name co 'Production'")
- `--orderby string` - Order by field (e.g., "name desc")
- `-o, --output string` - Output format (table, json, yaml, csv, json-raw)
- `-p, --page int` - Page number for pagination (default 1)
- `-s, --page-size int` - Page size for pagination (default 100)
- `--query string` - Custom OData query filter
- `--type string` - Filter by policy type
- `-h, --help` - Help for list command

#### Examples

##### Basic Usage
```bash
# List all protection policies
ppdm-cli protection-policies list

# Show help
ppdm-cli protection-policies list --help
```

##### Name Filtering
```bash
# Filter policies by name
ppdm-cli protection-policies list --filter "name co 'Production'"

# Filter by exact name
ppdm-cli protection-policies list --filter "name eq 'Daily Backup'"

# Filter by name starting with
ppdm-cli protection-policies list --filter "name sw 'Database'"
```

##### Type Filtering
```bash
# Filter by policy type
ppdm-cli protection-policies list --type "BACKUP"

# Filter by multiple types
ppdm-cli protection-policies list --query "type eq 'BACKUP' or type eq 'REPLICATION'"
```

##### Status Filtering
```bash
# Filter by enabled policies
ppdm-cli protection-policies list --filter "enabled eq true"

# Filter by disabled policies
ppdm-cli protection-policies list --filter "enabled eq false"
```

##### Custom Filtering
```bash
# Custom OData query
ppdm-cli protection-policies list --query "numberOfAssets gt 0"

# Complex filter
ppdm-cli protection-policies list --query "enabled eq true and numberOfAssets gt 0"

# Filter by asset count
ppdm-cli protection-policies list --filter "numberOfAssets eq 0"
```

##### Pagination and Sorting
```bash
# Custom page size
ppdm-cli protection-policies list --page 1 --page-size 50

# Order by name
ppdm-cli protection-policies list --orderby "name asc"

# Order by asset count
ppdm-cli protection-policies list --orderby "numberOfAssets desc"
```

##### Output Formats
```bash
# Table format (default)
ppdm-cli protection-policies list

# JSON format
ppdm-cli protection-policies list --output json

# YAML format
ppdm-cli protection-policies list --output yaml

# CSV format for reporting
ppdm-cli protection-policies list --output csv > policies.csv

# JSON-Raw for automation
ppdm-cli protection-policies list --output json-raw
```

#### Output Fields

The table output includes the following fields:
- **ID** - Policy identifier
- **NAME** - Policy name
- **TYPE** - Policy type (BACKUP, REPLICATION, etc.)
- **ENABLED** - Whether policy is enabled
- **ASSET COUNT** - Number of assets protected
- **CREATED AT** - When the policy was created
- **UPDATED AT** - When the policy was last updated

#### Sample Output

```
ID                                    NAME                    TYPE    ENABLED   ASSET COUNT   CREATED AT
12345678-1234-1234-1234-123456789012  Daily Backup Policy      BACKUP  true      15            2024-01-15T10:00:00Z
87654321-4321-4321-4321-210987654321  Weekly Replication      REPLICATION true      8             2024-01-10T14:30:00Z
```

### show

Show detailed information about a specific protection policy.

#### Syntax
```bash
ppdm-cli protection-policies show --id <policy-id> [flags]
```

#### Flags
- `--id string` - Policy ID (required)
- `-o, --output string` - Output format (table, json, yaml, csv, json-raw)
- `-h, --help` - Help for show command

#### Examples

##### Basic Usage
```bash
# Show policy details
ppdm-cli protection-policies show --id "12345678-1234-1234-1234-123456789012"

# Show in JSON format
ppdm-cli protection-policies show --id "12345678-1234-1234-1234-123456789012" --output json

# Show in YAML format
ppdm-cli protection-policies show --id "12345678-1234-1234-1234-123456789012" --output yaml

# Show raw API response
ppdm-cli protection-policies show --id "12345678-1234-1234-1234-123456789012" --output json-raw
```

### create

Create a new protection policy.

#### Syntax
```bash
ppdm-cli protection-policies create --from-file <policy.json> [flags]
```

#### Flags
- `--from-file string` - Policy configuration file (JSON)
- `-h, --help` - Help for create command

#### Examples

##### Basic Usage
```bash
# Create policy from file
ppdm-cli protection-policies create --from-file policy.json

# Create with dry-run
ppdm-cli protection-policies create --from-file policy.json --dry-run
```

### update

Update an existing protection policy.

#### Syntax
```bash
ppdm-cli protection-policies update --from-file <policy.json> [flags]
```

#### Flags
- `--from-file string` - Policy configuration file (JSON)
- `-h, --help` - Help for update command

#### Examples

##### Basic Usage
```bash
# Update policy from file
ppdm-cli protection-policies update --from-file policy.json

# Update with dry-run
ppdm-cli protection-policies update --from-file policy.json --dry-run
```

### delete

Delete a protection policy.

#### Syntax
```bash
ppdm-cli protection-policies delete --id <policy-id> [flags]
```

#### Flags
- `--id string` - Policy ID (required)
- `--force` - Force deletion without confirmation
- `-h, --help` - Help for delete command

#### Examples

##### Basic Usage
```bash
# Delete a policy
ppdm-cli protection-policies delete --id "12345678-1234-1234-1234-123456789012"

# Force deletion without confirmation
ppdm-cli protection-policies delete --id "12345678-1234-1234-1234-123456789012" --force
```

### search

Search protection policies by name with flexible filtering.

#### Syntax
```bash
ppdm-cli protection-policies search <search-term> [flags]
```

#### Examples

##### Basic Search
```bash
# Search for policies
ppdm-cli protection-policies search "Daily"

# Search with output format
ppdm-cli protection-policies search "Backup" --output json
```

### download

Download protection policy configuration.

#### Syntax
```bash
ppdm-cli protection-policies download --id <policy-id> [flags]
```

#### Flags
- `--id string` - Policy ID (required)
- `--output string` - Output file path
- `-h, --help` - Help for download command

#### Examples

##### Basic Usage
```bash
# Download policy configuration
ppdm-cli protection-policies download --id "12345678-1234-1234-1234-123456789012"

# Download to specific file
ppdm-cli protection-policies download --id "12345678-1234-1234-1234-123456789012" --output policy.json
```

### batch-update

Update multiple policies in batch operations.

#### Syntax
```bash
ppdm-cli protection-policies batch-update --policy-ids <ids> --json <json> [flags]
```

#### Flags
- `--policy-ids string` - Comma-separated policy IDs
- `--json string` - JSON update data
- `--dry-run` - Dry run to preview changes
- `-h, --help` - Help for batch-update command

#### Examples

##### Basic Usage
```bash
# Update multiple policies
ppdm-cli protection-policies batch-update --policy-ids "policy-123,policy-456" --json '{"disabled": false}'

# Dry run to preview
ppdm-cli protection-policies batch-update --policy-ids "policy-123,policy-456" --json '{"disabled": false}' --dry-run
```

### protect

Trigger on-demand protection for a specific asset.

#### Syntax
```bash
ppdm-cli protection-policies protect --policy-id <policy-id> --asset-id <asset-id> [flags]
```

#### Flags
- `--policy-id string` - Policy ID (required)
- `--asset-id string` - Asset ID (required)
- `-h, --help` - Help for protect command

#### Examples

##### Basic Usage
```bash
# Trigger on-demand protection
ppdm-cli protection-policies protect --policy-id "policy-123" --asset-id "asset-456"
```

### show-objective

Show protection policy objectives details.

#### Syntax
```bash
ppdm-cli protection-policies show-objective --id <policy-id> [flags]
```

#### Flags
- `--id string` - Policy ID (required)
- `-o, --output string` - Output format (table, json, yaml, csv, json-raw)
- `-h, --help` - Help for show-objective command

#### Examples

##### Basic Usage
```bash
# Show policy objectives
ppdm-cli protection-policies show-objective --id "12345678-1234-1234-1234-123456789012"

# Show in JSON format
ppdm-cli protection-policies show-objective --id "12345678-1234-1234-1234-123456789012" --output json
```

### asset-assignments

Manage asset assignments for protection policies.

#### Syntax
```bash
ppdm-cli protection-policies asset-assignments [command]
```

#### Available Commands
- `list` - List asset assignments
- `add` - Add asset to policy
- `remove` - Remove asset from policy

#### Examples

##### Basic Usage
```bash
# List asset assignments
ppdm-cli protection-policies asset-assignments list --policy-id "policy-123"

# Add asset to policy
ppdm-cli protection-policies asset-assignments add --policy-id "policy-123" --asset-id "asset-456"

# Remove asset from policy
ppdm-cli protection-policies asset-assignments remove --policy-id "policy-123" --asset-id "asset-456"
```

### rules

Manage rules for protection policies.

#### Syntax
```bash
ppdm-cli protection-policies rules [command]
```

#### Available Commands
- `list` - List policy rules
- `add` - Add rule to policy
- `remove` - Remove rule from policy

#### Examples

##### Basic Usage
```bash
# List policy rules
ppdm-cli protection-policies rules list --policy-id "policy-123"

# Add rule to policy
ppdm-cli protection-policies rules add --policy-id "policy-123" --from-file rule.json

# Remove rule from policy
ppdm-cli protection-policies rules remove --policy-id "policy-123" --rule-id "rule-456"
```

## Policy Types

### Common Policy Types

| Type | Description | Use Case |
|------|-------------|----------|
| `BACKUP` | Backup policies | Regular backup operations |
| `REPLICATION` | Replication policies | Data replication between systems |
| `ARCHIVE` | Archive policies | Long-term data archiving |
| `RESTORE` | Restore policies | Restore operations |

## Use Cases

### Daily Operations

```bash
# Check all enabled policies
ppdm-cli protection-policies list --filter "enabled eq true"

# Check policies with no assets
ppdm-cli protection-policies list --filter "numberOfAssets eq 0"

# Check backup policies
ppdm-cli protection-policies list --type "BACKUP"
```

### Policy Management

```bash
# Create new policy
ppdm-cli protection-policies create --from-file policy.json

# Update existing policy
ppdm-cli protection-policies update --from-file policy.json

# Delete policy
ppdm-cli protection-policies delete --id "policy-id"
```

### Troubleshooting

```bash
# Show detailed policy information
ppdm-cli protection-policies show --id "policy-id" --output json-raw

# Check policy objectives
ppdm-cli protection-policies show-objective --id "policy-id"

# Review asset assignments
ppdm-cli protection-policies asset-assignments list --policy-id "policy-id"
```

### Reporting

```bash
# Generate policy inventory report
ppdm-cli protection-policies list --output csv > policies.csv

# Generate enabled policies report
ppdm-cli protection-policies list --filter "enabled eq true" --output json > enabled_policies.json

# Export policies with no assets
ppdm-cli protection-policies list --filter "numberOfAssets eq 0" --output csv > unassigned_policies.csv
```

### Automation Scripts

```bash
#!/bin/bash
# Policy monitoring script

echo "=== Enabled Policies ==="
ppdm-cli protection-policies list --filter "enabled eq true"

echo "=== Policies with No Assets ==="
ppdm-cli protection-policies list --filter "numberOfAssets eq 0"

echo "=== Backup Policies ==="
ppdm-cli protection-policies list --type "BACKUP"

# Export to JSON for processing
ppdm-cli protection-policies list --output json-raw > current_policies.json
```

## Tips and Best Practices

### 1. Use Efficient Filtering
Filter at the API level for better performance:
```bash
# Good: Filter at API level
ppdm-cli protection-policies list --filter "enabled eq true"

# Avoid: Get all data then filter
ppdm-cli protection-policies list | grep true
```

### 2. Use Raw Formats for Automation
Use `json-raw` for clean automation output:
```bash
ppdm-cli protection-policies list --output json-raw 2>/dev/null | jq '.content[].enabled'
```

### 3. Monitor Unassigned Policies
Regularly check for policies with no assets:
```bash
ppdm-cli protection-policies list --filter "numberOfAssets eq 0"
```

### 4. Use Pagination for Large Datasets
```bash
# Process policies in batches
ppdm-cli protection-policies list --page 1 --page-size 100 --type "BACKUP"
```

### 5. Export for Analysis
```bash
# Export to CSV for Excel analysis
ppdm-cli protection-policies list --output csv > policies_analysis.csv

# Export to JSON for custom processing
ppdm-cli protection-policies list --output json-raw > policies_raw.json
```

## Troubleshooting

### Common Issues

#### 1. No Policies Found
```bash
# Check policy types
ppdm-cli protection-policies list --type "BACKUP"

# Try without filters first
ppdm-cli protection-policies list

# Check filter syntax
ppdm-cli protection-policies list --filter "name eq 'Daily Backup'"
```

#### 2. Authentication Issues
```bash
# Test connection
ppdm-cli config test --host production

# Use debug mode
ppdm-cli --debug protection-policies list
```

#### 3. Performance Issues
```bash
# Use pagination
ppdm-cli protection-policies list --page 1 --page-size 50

# Use specific filters
ppdm-cli protection-policies list --type "BACKUP"
```

### Debug Mode

Use debug mode for detailed information:
```bash
ppdm-cli --debug protection-policies list --type "BACKUP"
```

This will show:
- API endpoint details
- Request parameters
- Response timing
- Error details

---

*For more information about other commands, see the [main command reference](../COMMAND_REFERENCE.md).*
