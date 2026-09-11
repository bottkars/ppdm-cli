# Infrastructure Objects Command Reference

## Overview

The `infrastructure-objects` command manages infrastructure objects in PowerProtect Data Manager. It provides comprehensive capabilities for listing, filtering, and analyzing discovered infrastructure components.

## Commands

### list

List all infrastructure objects with advanced filtering capabilities.

#### Syntax
```bash
ppdm-cli infrastructure-objects list [flags]
```

#### Flags
- `-c, --category string` - Filter by infrastructure object category
- `-t, --type string` - Filter by infrastructure object type
- `-v, --vendor string` - Filter by vendor
- `-f, --filter string` - Custom filter expression
- `--orderby string` - Order by field (e.g., "name asc")
- `-o, --output string` - Output format (table, json, yaml, csv, json-raw, yaml-raw)
- `-h, --help` - Help for list command

#### Examples

##### Basic Usage
```bash
# List all infrastructure objects
ppdm-cli infrastructure-objects list

# Show help
ppdm-cli infrastructure-objects list --help
```

##### Category Filtering
```bash
# List storage systems
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM

# List network hosts
ppdm-cli infrastructure-objects list --category NETWORK_HOST

# List application hosts
ppdm-cli infrastructure-objects list --category APPLICATION_HOST

# List inventory sources
ppdm-cli infrastructure-objects list --category INVENTORY_SOURCE

# List virtualization clusters
ppdm-cli infrastructure-objects list --category HYPERVISOR_CLUSTER

# List virtualization managers
ppdm-cli infrastructure-objects list --category HYPERVISOR_MANAGER

# List virtualization servers
ppdm-cli infrastructure-objects list --category HYPERVISOR_SERVER

# List cloud storage profiles
ppdm-cli infrastructure-objects list --category CLOUD_STORAGE_PROFILE

# List storage appliances
ppdm-cli infrastructure-objects list --category STORAGE_APPLIANCE

# List application systems
ppdm-cli infrastructure-objects list --category APPLICATION_SYSTEM
```

##### Type Filtering
```bash
# List VMware ESX hosts
ppdm-cli infrastructure-objects list --type VMWARE_ESX_HOST

# List VMware vCenter servers
ppdm-cli infrastructure-objects list --type VMWARE_VCENTER

# List Data Domain appliances
ppdm-cli infrastructure-objects list --type DATA_DOMAIN_APPLIANCE

# List Generic application hosts
ppdm-cli infrastructure-objects list --type GENERIC_APPLICATION_HOST

# List Nutanix clusters
ppdm-cli infrastructure-objects list --type NUTANIX_CLUSTER
```

##### Vendor Filtering
```bash
# List VMware objects
ppdm-cli infrastructure-objects list --vendor VMWARE

# List DataDomain objects
ppdm-cli infrastructure-objects list --vendor DATADOMAIN

# List Generic objects
ppdm-cli infrastructure-objects list --vendor GENERIC

# List Microsoft objects
ppdm-cli infrastructure-objects list --vendor MICROSOFT
```

##### Combined Filtering
```bash
# Combine category and vendor filters
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --vendor DATADOMAIN

# Combine type and vendor filters
ppdm-cli infrastructure-objects list --type VMWARE_ESX_HOST --vendor VMWARE

# Combine all filters
ppdm-cli infrastructure-objects list \
  --category STORAGE_SYSTEM \
  --vendor DATADOMAIN \
  --type DATA_DOMAIN_APPLIANCE
```

##### Output Formats
```bash
# Table format (default)
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM

# JSON format
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --output json

# YAML format
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --output yaml

# CSV format
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --output csv

# JSON-Raw for automation
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --output json-raw

# YAML-Raw for automation
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --output yaml-raw
```

##### Pagination
```bash
# Custom page size
ppdm-cli infrastructure-objects list --page 1 --size 10

# Order by name
ppdm-cli infrastructure-objects list --orderby "name asc"

# Order by type
ppdm-cli infrastructure-objects list --orderby "type desc"
```

##### Custom Filters
```bash
# Filter by status
ppdm-cli infrastructure-objects list --filter 'availabilityStatus/value eq "AVAILABLE"'

# Filter by name
ppdm-cli infrastructure-objects list --filter 'name co "server"'

# Complex filter
ppdm-cli infrastructure-objects list --filter 'vendor eq "VMWARE" and type eq "VMWARE_ESX_HOST"'
```

##### Tab Completion
```bash
# Category tab completion
ppdm-cli infrastructure-objects list --category <TAB>
# Shows: APPLICATION_HOST, APPLICATION_SYSTEM, CLOUD_STORAGE_PROFILE, etc.

# Type tab completion
ppdm-cli infrastructure-objects list --type <TAB>
# Shows: CLOUD_STORAGE_PROFILE_ECS, DATA_DOMAIN_APPLIANCE, etc.

# Output format tab completion
ppdm-cli infrastructure-objects list --output <TAB>
# Shows: table, json, yaml, csv, json-raw, yaml-raw
```

#### Output Fields

The table output includes the following fields:
- **ID** - Unique identifier (full ID, not truncated)
- **NAME** - Object name
- **TYPE** - Infrastructure object type
- **VENDOR** - Vendor name
- **STATUS** - Availability status
- **CATEGORY** - Comma-separated list of categories

#### Sample Output

```
ID                                    NAME                                  TYPE                          VENDOR      STATUS         CATEGORY
7e7b329c-c4ce-4e08-b862-b3e7baecc905  nasug.home.labbuildr.com:/ISO         GENERIC_NAS_APPLIANCE         GENERIC     AVAILABLE_NEW  STORAGE_SYSTEM, STORAGE_APPLIANCE
2dd0268b-b36b-54b0-89f5-a446268aa617  hpemini.home.labbuildr.com             VMWARE_ESX_HOST               VMWARE     AVAILABLE      NETWORK_HOST
3a39d533-9d27-5923-8072-dd64c39a22ec  esxi-mgmt.home.labbuildr.com           VMWARE_ESX_HOST               VMWARE     AVAILABLE      NETWORK_HOST
69c8ac3a-3eca-55f1-a2e0-347e63a90540  vcsa1.home.labbuildr.com              VMWARE_VCENTER               VMWARE     UNKNOWN        INVENTORY_SOURCE
```

### show

Show detailed information about a specific infrastructure object.

#### Syntax
```bash
ppdm-cli infrastructure-objects show --id <object-id> [flags]
```

#### Flags
- `--id string` - Infrastructure object ID (required)
- `-o, --output string` - Output format (table, json, yaml, csv, json-raw, yaml-raw)
- `-h, --help` - Help for show command

#### Examples

##### Basic Usage
```bash
# Show object details
ppdm-cli infrastructure-objects show --id "7e7b329c-c4ce-4e08-b862-b3e7baecc905"

# Show in JSON format
ppdm-cli infrastructure-objects show --id "7e7b329c-c4ce-4e08-b862-b3e7baecc905" --output json

# Show raw API response
ppdm-cli infrastructure-objects show --id "7e7b329c-c4ce-4e08-b862-b3e7baecc905" --output json-raw
```

## Categories Reference

### Available Categories

| Category | Description | Example Objects |
|----------|-------------|-----------------|
| `APPLICATION_HOST` | Application server hosts | GENERIC_APPLICATION_HOST |
| `APPLICATION_SYSTEM` | Application system clusters | FILE_SYSTEM_CLUSTER |
| `CLOUD_STORAGE_PROFILE` | Cloud storage configurations | OBJECT_STORAGE_PROFILE_AZURE |
| `HYPERVISOR_CLUSTER` | Virtualization clusters | HYPERV_CLUSTER, NUTANIX_CLUSTER |
| `HYPERVISOR_MANAGER` | Virtualization management | PRISM_CENTRAL |
| `HYPERVISOR_SERVER` | Virtualization servers | HYPERV_SERVER, NUTANIX_SERVER |
| `INVENTORY_SOURCE` | Discovery and inventory sources | VMWARE_VCENTER, DEFAULT_APP_GROUP |
| `NETWORK_HOST` | Network-connected hosts | VMWARE_ESX_HOST |
| `STORAGE_APPLIANCE` | Storage appliance devices | DATA_DOMAIN_APPLIANCE |
| `STORAGE_SYSTEM` | Storage system devices | GENERIC_NAS_APPLIANCE |

### Types Reference

### Available Types

| Type | Category | Description |
|------|----------|-------------|
| `CLOUD_STORAGE_PROFILE_ECS` | CLOUD_STORAGE_PROFILE | Dell ECS cloud storage |
| `DATA_DOMAIN_APPLIANCE` | STORAGE_SYSTEM | Data Domain storage appliance |
| `DEFAULT_APP_GROUP` | INVENTORY_SOURCE | Default application group |
| `EXTERNAL_DATA_DOMAIN_INTERFACE` | INVENTORY_SOURCE | External Data Domain interface |
| `FILE_SYSTEM_CLUSTER` | APPLICATION_SYSTEM | File system cluster |
| `GENERIC_APPLICATION_HOST` | APPLICATION_HOST | Generic application host |
| `GENERIC_APPLICATION_SYSTEM` | APPLICATION_SYSTEM | Generic application system |
| `GENERIC_NAS_APPLIANCE` | STORAGE_SYSTEM | Generic NAS appliance |
| `GENERIC_NAS_MANAGEMENT_SERVER` | INVENTORY_SOURCE | Generic NAS management server |
| `OBJECT_STORAGE_PROFILE_AZURE` | CLOUD_STORAGE_PROFILE | Azure object storage |
| `VMWARE_ESX_CLUSTER` | HYPERVISOR_CLUSTER | VMware ESX cluster |
| `VMWARE_ESX_HOST` | NETWORK_HOST | VMware ESX host |
| `VMWARE_VCENTER` | INVENTORY_SOURCE | VMware vCenter server |
| `HYPERV_CLUSTER` | HYPERVISOR_CLUSTER | Hyper-V cluster |
| `HYPERV_SERVER` | HYPERVISOR_SERVER | Hyper-V server |
| `KUBERNETES_CLUSTER` | HYPERVISOR_CLUSTER | Kubernetes cluster |
| `NUTANIX_CLUSTER` | HYPERVISOR_CLUSTER | Nutanix cluster |
| `NUTANIX_SERVER` | HYPERVISOR_SERVER | Nutanix server |
| `PRISM_CENTRAL` | HYPERVISOR_MANAGER | Nutanix Prism Central |

## Use Cases

### Infrastructure Analysis

```bash
# Analyze storage infrastructure
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --output csv > storage_analysis.csv

# Analyze network infrastructure
ppdm-cli infrastructure-objects list --category NETWORK_HOST --output json > network_analysis.json

# Analyze virtualization infrastructure
ppdm-cli infrastructure-objects list --category HYPERVISOR_CLUSTER --vendor VMWARE
```

### Health Monitoring

```bash
# Check availability status
ppdm-cli infrastructure-objects list --filter 'availabilityStatus/value ne "AVAILABLE"'

# Monitor storage systems
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --filter 'availabilityStatus/value eq "ERROR"'

# Check application hosts
ppdm-cli infrastructure-objects list --category APPLICATION_HOST --filter 'availabilityStatus/value ne "AVAILABLE_NEW"'
```

### Capacity Planning

```bash
# Export all infrastructure for capacity planning
ppdm-cli infrastructure-objects list --output csv > infrastructure_inventory.csv

# Filter by vendor for vendor-specific planning
ppdm-cli infrastructure-objects list --vendor VMWARE --output json > vmware_inventory.json

# Storage capacity analysis
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --type DATA_DOMAIN_APPLIANCE
```

### Automation Scripts

```bash
#!/bin/bash
# Infrastructure inventory script

echo "=== Storage Systems ==="
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --output table

echo "=== Network Hosts ==="
ppdm-cli infrastructure-objects list --category NETWORK_HOST --output table

echo "=== Application Hosts ==="
ppdm-cli infrastructure-objects list --category APPLICATION_HOST --output table

echo "=== Virtualization Infrastructure ==="
ppdm-cli infrastructure-objects list --category HYPERVISOR_CLUSTER --output table

# Export to JSON for processing
ppdm-cli infrastructure-objects list --output json-raw > full_inventory.json
```

### Troubleshooting

```bash
# Find objects with issues
ppdm-cli infrastructure-objects list --filter 'availabilityStatus/value ne "AVAILABLE"'

# Check specific object details
ppdm-cli infrastructure-objects show --id "problematic-object-id" --output json-raw

# Debug connection issues
ppdm-cli --debug infrastructure-objects list --category STORAGE_SYSTEM
```

## Tips and Best Practices

### 1. Use Tab Completion
Take advantage of tab completion to discover available categories and types:
```bash
ppdm-cli infrastructure-objects list --category <TAB>
ppdm-cli infrastructure-objects list --type <TAB>
```

### 2. Filter at Source
Use API filters instead of client-side filtering for better performance:
```bash
# Good: Filter at API level
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM

# Avoid: Filter after getting all data
ppdm-cli infrastructure-objects list | grep STORAGE_SYSTEM
```

### 3. Use Raw Formats for Automation
Use `json-raw` or `yaml-raw` for automation scripts to get clean API responses:
```bash
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --output json-raw 2>/dev/null | jq '.content[].name'
```

### 4. Combine Filters Effectively
Combine multiple filters for precise results:
```bash
ppdm-cli infrastructure-objects list --category STORAGE_SYSTEM --vendor DATADOMAIN --filter 'availabilityStatus/value eq "AVAILABLE"'
```

### 5. Use Pagination for Large Datasets
Use pagination when working with large numbers of objects:
```bash
ppdm-cli infrastructure-objects list --page 1 --size 50 --category STORAGE_SYSTEM
```

## Troubleshooting

### Common Issues

#### 1. No Results Found
```bash
# Check if category exists
ppdm-cli infrastructure-objects list --category <TAB>

# Try without filters first
ppdm-cli infrastructure-objects list

# Check filter syntax
ppdm-cli infrastructure-objects list --filter 'name co "test"'
```

#### 2. Authentication Issues
```bash
# Test connection
ppdm-cli config test --host production

# Check configuration
ppdm-cli config list

# Use debug mode
ppdm-cli --debug infrastructure-objects list
```

#### 3. Output Format Issues
```bash
# Use json-raw for clean automation output
ppdm-cli infrastructure-objects list --output json-raw 2>/dev/null

# Redirect stderr to avoid pollution
ppdm-cli infrastructure-objects list --output json-raw 2>/dev/null | jq '.'
```

### Debug Mode

Use debug mode for detailed information:
```bash
ppdm-cli --debug infrastructure-objects list --category STORAGE_SYSTEM
```

This will show:
- API endpoint details
- Request parameters
- Response headers
- Timing information

---

*For more information about other commands, see the [main command reference](../COMMAND_REFERENCE.md).*
