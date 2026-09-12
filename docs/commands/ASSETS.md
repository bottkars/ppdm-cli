# Assets Command Reference

## Overview

The `assets` command provides comprehensive management of PowerProtect Data Manager assets. It allows users to list, search, and view details of protected resources including virtual machines, file systems, databases, and other infrastructure components.

## Commands

### list

List all assets with comprehensive filtering options.

#### Syntax
```bash
ppdm-cli assets list [flags]
```

#### Flags
- `--detailed` - Show detailed asset information
- `--filter string` - Filter expression (e.g., "hostname eq 'server01'")
- `--hostname string` - Filter by hostname (exact match)
- `--name string` - Filter by exact asset name
- `--name-contains string` - Filter by asset name containing text
- `--name-starts-with string` - Filter by asset name starting with text
- `--order-by string` - Order by field (e.g., "hostname asc")
- `-o, --output string` - Output format (table, json, yaml, csv, json-raw, yaml-raw)
- `--page int` - Page number for pagination
- `--page-size int` - Number of items per page (default 100)
- `--protection-statuses strings` - Filter by protection statuses (comma-separated)
- `--protectionStatus string` - Filter by protection status (PROTECTED, UNPROTECTED)
- `--query string` - Custom OData query filter
- `--show-all` - Show all assets without pagination
- `--sort string` - Sort by field (e.g., hostname, name, type, status, createdAt)
- `--status strings` - Filter by asset status (comma-separated: AVAILABLE, UNAVAILABLE, NOT_DETECTED, etc.)
- `--subtype string` - Filter by asset subtype
- `--types strings` - Filter by asset types (comma-separated)
- `-h, --help` - Help for list command

#### Examples

##### Basic Usage
```bash
# List all assets
ppdm-cli assets list

# Show detailed information
ppdm-cli assets list --detailed

# Show help
ppdm-cli assets list --help
```

##### Type Filtering
```bash
# Show only VM assets
ppdm-cli assets list --types "VMWARE_VIRTUAL_MACHINE"

# Show multiple asset types
ppdm-cli assets list --types "FILE_SYSTEM,VMWARE_VIRTUAL_MACHINE"

# Filter by subtype
ppdm-cli assets list --subtype "LINUX"
```

##### Name Filtering
```bash
# Filter by exact name
ppdm-cli assets list --name "server01"

# Filter by name containing text
ppdm-cli assets list --name-contains "prod"

# Filter by name starting with text
ppdm-cli assets list --name-starts-with "web"
```

##### Hostname Filtering
```bash
# Filter by hostname
ppdm-cli assets list --hostname "server01"
```

##### Status Filtering
```bash
# Filter by status
ppdm-cli assets list --status "AVAILABLE"

# Filter by multiple statuses
ppdm-cli assets list --status "AVAILABLE,UNAVAILABLE"

# Filter by protection status
ppdm-cli assets list --protectionStatus "PROTECTED"

# Combined status and protection status
ppdm-cli assets list --status "NOT_DETECTED" --protectionStatus "PROTECTED"

# Multiple protection statuses
ppdm-cli assets list --protection-statuses "PROTECTED,UNPROTECTED"
```

##### Custom Filtering
```bash
# Custom filter expression
ppdm-cli assets list --filter "hostname eq 'server01'"

# Custom OData query
ppdm-cli assets list --query "type eq 'VMWARE_VIRTUAL_MACHINE' and size gt 1000000000"

# Combine filters
ppdm-cli assets list --hostname "server01" --query "protectionStatus eq 'PROTECTED'"

# Complex query
ppdm-cli assets list --name "database" --query "size gt 1000000000"

# Time-based filtering
ppdm-cli assets list --query "createdAt gt '2025-01-01T00:00:00Z'"
```

##### Pagination and Sorting
```bash
# Custom page size
ppdm-cli assets list --page 2 --page-size 50

# Sort by field
ppdm-cli assets list --sort "hostname"

# Order by field
ppdm-cli assets list --order-by "hostname asc"

# Show all assets without pagination
ppdm-cli assets list --show-all
```

##### Output Formats
```bash
# Table format (default)
ppdm-cli assets list

# JSON format
ppdm-cli assets list --output json

# YAML format
ppdm-cli assets list --output yaml

# CSV format for reporting
ppdm-cli assets list --output csv > assets.csv

# JSON-Raw for automation
ppdm-cli assets list --output json-raw

# YAML-Raw for automation
ppdm-cli assets list --output yaml-raw
```

#### Output Fields

The table output includes the following fields:
- **ID** - Asset identifier
- **NAME** - Asset name
- **HOSTNAME** - Asset hostname
- **TYPE** - Asset type (VMWARE_VIRTUAL_MACHINE, FILE_SYSTEM, etc.)
- **STATUS** - Current status (AVAILABLE, UNAVAILABLE, NOT_DETECTED, etc.)
- **PROTECTION STATUS** - Protection status (PROTECTED, UNPROTECTED)
- **CREATED AT** - When the asset was created

#### Sample Output

```
ID                                    NAME                    HOSTNAME          TYPE                    STATUS      PROTECTION STATUS
12345678-1234-1234-1234-123456789012  database-server-01       db-server-01      VMWARE_VIRTUAL_MACHINE  AVAILABLE   PROTECTED
87654321-4321-4321-4321-210987654321  file-share-prod          fs-prod          FILE_SYSTEM             AVAILABLE   PROTECTED
```

### search

Search assets by name.

#### Syntax
```bash
ppdm-cli assets search <search-term> [flags]
```

#### Examples

##### Basic Search
```bash
# Search for assets
ppdm-cli assets search "database"

# Search with output format
ppdm-cli assets search "server" --output json
```

### show

Show detailed information about a specific asset.

#### Syntax
```bash
ppdm-cli assets show --id <asset-id> [flags]
```

#### Flags
- `--id string` - Asset ID (required)
- `-o, --output string` - Output format (table, json, yaml, csv, json-raw, yaml-raw)
- `-h, --help` - Help for show command

#### Examples

##### Basic Usage
```bash
# Show asset details
ppdm-cli assets show --id "12345678-1234-1234-1234-123456789012"

# Show in JSON format
ppdm-cli assets show --id "12345678-1234-1234-1234-123456789012" --output json

# Show in YAML format
ppdm-cli assets show --id "12345678-1234-1234-1234-123456789012" --output yaml

# Show raw API response
ppdm-cli assets show --id "12345678-1234-1234-1234-123456789012" --output json-raw
```

## Asset Types

### Common Asset Types

| Type | Description | Example |
|------|-------------|---------|
| `VMWARE_VIRTUAL_MACHINE` | VMware virtual machines | ESXi VMs |
| `FILE_SYSTEM` | File systems | NFS shares, CIFS shares |
| `ORACLE_DATABASE` | Oracle databases | Oracle DB instances |
| `SQL_SERVER_DATABASE` | SQL Server databases | SQL Server instances |
| `MICROSOFT_EXCHANGE` | Microsoft Exchange | Exchange servers |
| `MICROSOFT_SHAREPOINT` | Microsoft SharePoint | SharePoint farms |
| `AWS_EC2_INSTANCE` | AWS EC2 instances | Cloud VMs |
| `AZURE_VIRTUAL_MACHINE` | Azure virtual machines | Cloud VMs |

### Asset Status Values

| Status | Description | Next Action |
|--------|-------------|-------------|
| `AVAILABLE` | Asset is available for protection | Can be protected |
| `UNAVAILABLE` | Asset is currently unavailable | Check connectivity |
| `NOT_DETECTED` | Asset not detected by PPDM | Run discovery |
| `DELETING` | Asset is being deleted | Wait for completion |
| `DELETED` | Asset has been deleted | Restore if needed |

### Protection Status Values

| Status | Description | Next Action |
|--------|-------------|-------------|
| `PROTECTED` | Asset is protected | Monitor backups |
| `UNPROTECTED` | Asset is not protected | Create protection policy |
| `PROTECTING` | Protection in progress | Monitor progress |
| `PROTECTION_FAILED` | Protection failed | Investigate and retry |

## Use Cases

### Daily Operations

```bash
# Check all available assets
ppdm-cli assets list --status "AVAILABLE"

# Check unprotected assets
ppdm-cli assets list --protectionStatus "UNPROTECTED"

# Check VM assets
ppdm-cli assets list --types "VMWARE_VIRTUAL_MACHINE"
```

### Troubleshooting

```bash
# Investigate unavailable assets
ppdm-cli assets list --status "UNAVAILABLE" --detailed

# Show detailed asset information
ppdm-cli assets show --id "asset-id" --output json-raw

# Check assets with protection failures
ppdm-cli assets list --query "protectionStatus eq 'UNPROTECTED' and status eq 'AVAILABLE'"
```

### Reporting

```bash
# Generate asset inventory report
ppdm-cli assets list --output csv > asset_inventory.csv

# Generate protected assets report
ppdm-cli assets list --protectionStatus "PROTECTED" --output json > protected_assets.json

# Export unprotected assets for analysis
ppdm-cli assets list --protectionStatus "UNPROTECTED" --output csv > unprotected_assets.csv
```

### Automation Scripts

```bash
#!/bin/bash
# Asset monitoring script

echo "=== Available Assets ==="
ppdm-cli assets list --status "AVAILABLE"

echo "=== Unprotected Assets ==="
ppdm-cli assets list --protectionStatus "UNPROTECTED"

echo "=== VM Assets ==="
ppdm-cli assets list --types "VMWARE_VIRTUAL_MACHINE"

# Export to JSON for processing
ppdm-cli assets list --output json-raw > current_assets.json
```

### Discovery and Onboarding

```bash
# Search for new assets
ppdm-cli assets search "new-server"

# Check asset details before protection
ppdm-cli assets show --id "asset-id" --detailed

# Verify asset status
ppdm-cli assets list --hostname "server01" --status "AVAILABLE"
```

## Tips and Best Practices

### 1. Use Efficient Filtering
Filter at the API level for better performance:
```bash
# Good: Filter at API level
ppdm-cli assets list --types "VMWARE_VIRTUAL_MACHINE"

# Avoid: Get all data then filter
ppdm-cli assets list | grep VMWARE_VIRTUAL_MACHINE
```

### 2. Use Raw Formats for Automation
Use `json-raw` for clean automation output:
```bash
ppdm-cli assets list --output json-raw 2>/dev/null | jq '.content[].protectionStatus'
```

### 3. Monitor Unprotected Assets
Regularly check for unprotected assets:
```bash
ppdm-cli assets list --protectionStatus "UNPROTECTED"
```

### 4. Use Pagination for Large Datasets
```bash
# Process assets in batches
ppdm-cli assets list --page 1 --page-size 100 --types "VMWARE_VIRTUAL_MACHINE"
```

### 5. Export for Analysis
```bash
# Export to CSV for Excel analysis
ppdm-cli assets list --output csv > assets_analysis.csv

# Export to JSON for custom processing
ppdm-cli assets list --output json-raw > assets_raw.json
```

## Troubleshooting

### Common Issues

#### 1. No Assets Found
```bash
# Check asset types
ppdm-cli assets list --types "VMWARE_VIRTUAL_MACHINE"

# Try without filters first
ppdm-cli assets list

# Check filter syntax
ppdm-cli assets list --filter "hostname eq 'server01'"
```

#### 2. Authentication Issues
```bash
# Test connection
ppdm-cli config test --host production

# Use debug mode
ppdm-cli --debug assets list
```

#### 3. Performance Issues
```bash
# Use pagination
ppdm-cli assets list --page 1 --page-size 50

# Use specific filters
ppdm-cli assets list --types "VMWARE_VIRTUAL_MACHINE"
```

### Debug Mode

Use debug mode for detailed information:
```bash
ppdm-cli --debug assets list --types "VMWARE_VIRTUAL_MACHINE"
```

This will show:
- API endpoint details
- Request parameters
- Response timing
- Error details

---

*For more information about other commands, see the [main command reference](../COMMAND_REFERENCE.md).*
