# Features Command Reference

## Overview

The features command provides access to PowerProtect Data Manager (PPDM) feature management through the V2 API. Features represent the various capabilities and modules available in PPDM, such as VM protection, NAS protection, replication, cloud disaster recovery, and more.

## Available Commands

### List Features

**Command:** `ppdm-cli features list`

**Description:** List all PPDM features with optional filtering, sorting, and pagination.

**API Operation:**
- **Operation ID:** getFeatures
- **API Version:** v2
- **Endpoint:** GET /api/v2/features
- **API Reference:** ppdm-public-v2-20.3.0.0.yaml

**Examples:**
```bash
# List all features (shows all 47 features)
ppdm-cli features list

# List with pagination
ppdm-cli features list --page 1 --page-size 20

# Filter by feature ID
ppdm-cli features list --filter 'id eq "VM_DIRECT"'

# Filter by status
ppdm-cli features list --filter 'status eq "ENABLED"'
ppdm-cli features list --filter 'status eq "DISABLED"'

# Sort by name
ppdm-cli features list --order-by "name asc"

# Output in JSON format
ppdm-cli features list --output json
```

**Output Formats:**
- table (default)
- json
- yaml
- csv
- json-raw
- yaml-raw

**Filter Examples:**
```bash
# Filter by feature ID
ppdm-cli features list --filter 'id eq "VM_DIRECT"'

# Filter by status
ppdm-cli features list --filter 'status eq "ENABLED"'

# Complex filter
ppdm-cli features list --filter 'id eq "VM_DIRECT" and status eq "ENABLED"'

# Filter by unavailable reason
ppdm-cli features list --filter 'unavailable eq "PLATFORM"'
```

### Show Feature Details

**Command:** `ppdm-cli features show [feature-id]`

**Description:** Show detailed information for a specific feature by ID.

**API Operation:**
- **Operation ID:** getFeatures (with filter)
- **API Version:** v2
- **Endpoint:** GET /api/v2/features
- **API Reference:** ppdm-public-v2-20.3.0.0.yaml

**Examples:**
```bash
# Show feature by ID (positional argument)
ppdm-cli features show VM_DIRECT

# Show feature by ID (flag)
ppdm-cli features show --id VM_DIRECT

# Output in JSON format
ppdm-cli features show VM_DIRECT --output json

# Output in YAML format
ppdm-cli features show VM_DIRECT --output yaml
```

**Output Formats:**
- table (default)
- json
- yaml
- csv
- json-raw
- yaml-raw

### Enable Feature

**Command:** `ppdm-cli features enable [feature-id]`

**Description:** Enable a PPDM feature by setting its status to ENABLED.

**API Operation:**
- **Operation ID:** patchFeaturesBatch
- **API Version:** v2
- **Endpoint:** PATCH /api/v2/features-batch
- **API Reference:** ppdm-public-v2-20.3.0.0.yaml

**Examples:**
```bash
# Enable feature by ID (positional argument)
ppdm-cli features enable NAS_NDMP

# Enable feature by ID (flag)
ppdm-cli features enable --id NAS_NDMP

# Enable with auto-enable child features
ppdm-cli features enable NAS_NDMP --auto-enable-child-features

# Output in JSON format
ppdm-cli features enable NAS_NDMP --output json
```

**Output Formats:**
- table (default)
- json
- yaml
- csv
- json-raw
- yaml-raw

### Disable Feature

**Command:** `ppdm-cli features disable [feature-id]`

**Description:** Disable a PPDM feature by setting its status to DISABLED.

**API Operation:**
- **Operation ID:** patchFeaturesBatch
- **API Version:** v2
- **Endpoint:** PATCH /api/v2/features-batch
- **API Reference:** ppdm-public-v2-20.3.0.0.yaml

**Examples:**
```bash
# Disable feature by ID (positional argument)
ppdm-cli features disable NAS_NDMP

# Disable feature by ID (flag)
ppdm-cli features disable --id NAS_NDMP

# Disable with auto-enable child features
ppdm-cli features disable NAS_NDMP --auto-enable-child-features

# Output in JSON format
ppdm-cli features disable NAS_NDMP --output json
```

**Output Formats:**
- table (default)
- json
- yaml
- csv
- json-raw
- yaml-raw

### Batch Update Feature Status

**Command:** `ppdm-cli features batch-update`

**Description:** Batch update feature status (ENABLE/DISABLE) in a single request.

**API Operation:**
- **Operation ID:** patchFeaturesBatch
- **API Version:** v2
- **Endpoint:** PATCH /api/v2/features-batch
- **API Reference:** ppdm-public-v2-20.3.0.0.yaml

**Examples:**
```bash
# Batch update features from JSON file
ppdm-cli features batch-update --file features.json

# Batch update with auto-enable child features
ppdm-cli features batch-update --file features.json --auto-enable-child-features

# Output in JSON format
ppdm-cli features batch-update --file features.json --output json
```

**JSON File Format:**
```json
{
  "requests": [
    {
      "id": "1",
      "body": {
        "id": "NAS_NDMP",
        "status": "ENABLED"
      }
    },
    {
      "id": "2",
      "body": {
        "id": "STORAGE_DIRECT",
        "status": "DISABLED"
      }
    }
  ]
}
```

**Note:** The `id` field in the request (e.g., "1", "2") is a sequence identifier, while the `body.id` is the actual feature ID (e.g., "NAS_NDMP").

**Output Formats:**
- table (default)
- json
- yaml
- csv
- json-raw
- yaml-raw

## Feature Properties

Each feature contains the following properties:

- **id** (string): Feature identifier (e.g., "VM_DIRECT", "NAS_NDMP")
- **name** (string): Feature name (e.g., "VM Protection and Restore")
- **status** (enum): Feature status - "ENABLED" or "DISABLED"
- **apiRules** (array): API access rules for the feature
- **togglers** (array): Feature toggle configurations
- **boundService** (string): Associated service (e.g., "ghvdm", "nasdm")
- **parents** (array): Parent feature IDs
- **jobRules** (array): Job execution rules
- **unavailable** (array): Unavailability reasons - "PLATFORM" or "LICENSE"

## Common Features

### VM Protection Features
- **VM_DIRECT**: VM Protection and Restore
- **VM_APP_AWARE**: Application Aware of VM Protection
- **VM_FLR**: File Level Restore of VM Protection
- **VM_IA**: Instant Access of VM Protection
- **VM_IR**: Instant Restore of VM Protection

### NAS Protection Features
- **NAS**: NAS protection and restore
- **NAS_SHARE_SNAPSHOT**: NAS Next-Gen Protection and Restore
- **NAS_NDMP**: NAS protection and restore based on NDMP

### Storage Features
- **STORAGE_DIRECT**: VMAX_STORAGE_GROUP protection and restore
- **FILE_SYSTEM**: FILE_SYSTEM protection and restore

### Cloud Features
- **CLOUD_DISASTER_RECOVERY**: Cloud Disaster Recovery
- **CLOUD_NATIVE_DIRECT**: CLOUD_NATIVE protection and restore
- **A2O**: Archive To Object

### Other Features
- **BACKUP**: Backup operations
- **RESTORE**: Restore operations
- **REPLICATION**: Replication operations
- **ANOD**: Anomaly Detection
- **HYPER_V**: Hyper-V VM Protection and Restore
- **NUTANIX_VM**: NUTANIX AHV VM Protection and Restore

## Filter Syntax

Features support OData filter expressions for advanced filtering:

```bash
# Filter by ID
--filter 'id eq "VM_DIRECT"'

# Filter by status
--filter 'status eq "ENABLED"'
--filter 'status eq "DISABLED"'

# Filter by unavailable reason
--filter 'unavailable eq "PLATFORM"'
--filter 'unavailable eq "LICENSE"'

# Complex filters
--filter 'id eq "VM_DIRECT" and status eq "ENABLED"'
--filter 'status eq "DISABLED" and unavailable eq "PLATFORM"'
```

## Pagination

Features support pagination for large result sets:

```bash
# Get first page
ppdm-cli features list --page 1 --page-size 20

# Get second page
ppdm-cli features list --page 2 --page-size 20
```

## Sorting

Features can be sorted by any field:

```bash
# Sort by name ascending
ppdm-cli features list --order-by "name asc"

# Sort by name descending
ppdm-cli features list --order-by "name desc"

# Sort by status
ppdm-cli features list --order-by "status asc"
```

## Output Formats

All commands support multiple output formats:

- **table**: Human-readable table format (default)
- **json**: Structured JSON output
- **yaml**: Structured YAML output
- **csv**: Comma-separated values
- **json-raw**: Raw JSON response from API
- **yaml-raw**: Raw YAML response from API

## Multi-Host Support

All commands support multi-host configuration:

```bash
# Use specific PPDM server
ppdm-cli features list --ppdm-server prod
ppdm-cli features show VM_DIRECT --ppdm-server prod
ppdm-cli features batch-update --file features.json --ppdm-server prod
```

## Dry-Run Mode

Test batch updates without executing them:

```bash
# Dry-run batch update
ppdm-cli features batch-update --file features.json --dry-run
```

## Debug Mode

Enable debug mode to see API call details:

```bash
# Enable debug mode
ppdm-cli features list --debug
ppdm-cli features show VM_DIRECT --debug
ppdm-cli features batch-update --file features.json --debug
```

## Examples

### List all enabled features
```bash
ppdm-cli features list --filter 'status eq "ENABLED"'
```

### List all disabled features
```bash
ppdm-cli features list --filter 'status eq "DISABLED"'
```

### Show VM protection feature details
```bash
ppdm-cli features show VM_DIRECT
```

### Enable NAS NDMP feature
```bash
ppdm-cli features enable NAS_NDMP
```

### Disable NAS NDMP feature
```bash
ppdm-cli features disable NAS_NDMP
```

### Enable multiple features
```json
{
  "requests": [
    {
      "id": "1",
      "body": {
        "id": "NAS_NDMP",
        "status": "ENABLED"
      }
    },
    {
      "id": "2",
      "body": {
        "id": "STORAGE_DIRECT",
        "status": "ENABLED"
      }
    }
  ]
}
```

```bash
ppdm-cli features batch-update --file enable-features.json
```

### Disable multiple features
```json
{
  "requests": [
    {
      "id": "1",
      "body": {
        "id": "STORAGE_DIRECT",
        "status": "DISABLED"
      }
    },
    {
      "id": "2",
      "body": {
        "id": "RE-IP",
        "status": "DISABLED"
      }
    }
  ]
}
```

```bash
ppdm-cli features batch-update --file disable-features.json
```

## API Documentation

For complete API documentation, see:
- **API Reference:** ppdm-public-v2-20.3.0.0.yaml
- **Dell API Documentation:** https://www.dell.com/support/powerprotect
