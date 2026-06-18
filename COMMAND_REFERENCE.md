# PPDM CLI Complete Command Reference

## Table of Contents

- [Overview](#overview)
- [Utility Commands](#utility-commands)
- [Core Management Commands](#core-management-commands)
- [Asset Management Commands](#asset-management-commands)
- [Protection & Backup Commands](#protection--backup-commands)
- [Monitoring Commands](#monitoring-commands)
- [Configuration Commands](#configuration-commands)
- [Infrastructure Commands](#infrastructure-commands)
- [Security & Identity Commands](#security--identity-commands)
- [Advanced Commands](#advanced-commands)
- [System Commands](#system-commands)
- [Deprecated Commands](#deprecated-commands)

---

## Overview

The PowerProtect Data Manager (PPDM) CLI provides comprehensive management capabilities for PPDM 20.1. This reference covers all available commands with examples and usage patterns.

### Global Flags

```bash
--debug                    Enable debug/trace mode for API calls and responses
--dry-run                  Show API calls and JSON bodies without executing (secrets redacted)
-h, --help                 Help for any command
--ppdm-server string       Select specific PPDM server from configuration
-v, --version             Show version information
--log-level string        Log level (error, warn, info, debug)
--quiet                   Suppress log messages for clean output
--show-api-calls          Show redacted API requests and responses
--ppdm-log string         Enable OpenTelemetry logging to file or stdout
--otel-enabled            Enable OpenTelemetry observability (traces and metrics)
```

### Output Formats

Most commands support multiple output formats:
- `table` (default) - Human-readable table format
- `json` - Structured JSON output
- `yaml` - YAML format output
- `csv` - CSV format for data export
- `json-raw` - Raw API response JSON (for automation)
- `yaml-raw` - Raw API response YAML (for automation)

---

## Utility Commands

### about
Display comprehensive information about the PPDM CLI tool, including its purpose, capabilities, and version information.

#### Examples
```bash
# Show CLI information
ppdm-cli about

# Show CLI version and build details
ppdm-cli about --output json
```

### completion
Generate shell completion scripts for bash, zsh, fish, or PowerShell.

#### Examples
```bash
# Generate bash completion
ppdm-cli completion bash > /etc/bash_completion.d/ppdm-cli

# Generate zsh completion
ppdm-cli completion zsh > ~/.zsh/completions/_ppdm-cli

# Generate fish completion
ppdm-cli completion fish > ~/.config/fish/completions/ppdm-cli.fish

# Generate PowerShell completion
ppdm-cli completion powershell > ppdm-cli.ps1
```

#### Installation
```bash
# Load completion immediately (bash)
source <(ppdm-cli completion bash)

# Load completion immediately (zsh)
autoload -U compinit && compinit
```

### help
Get help about any command or subcommand.

#### Examples
```bash
# Show main help
ppdm-cli help

# Show help for specific command
ppdm-cli help assets

# Show help for subcommand
ppdm-cli help assets list
```

### version
Show PPDM CLI version information and build details.

#### Examples
```bash
# Show version (short)
ppdm-cli version

# Show detailed version information
ppdm-cli version --output json

# Show version with build details
ppdm-cli version --output yaml
```

---

## Core Management Commands

### account
Account management in PowerProtect Data Manager provides operations for managing user credentials including password changes and policy retrieval.

#### Available Operations
- Change password for authenticated users
- Get system-wide password policy requirements

#### Examples
```bash
# Change password (requires current password)
ppdm-cli account change-password --username <USERNAME> --password currentpass --new-password newpass

# Change password using JSON file
ppdm-cli account change-password --from-file change-password.json

# Get password policy
ppdm-cli account password-policy

# Show account information
ppdm-cli account show --output json
```

#### Notes
- For password reset functionality, please use the UI workflow
- Password changes require current password for security
- Policy retrieval shows system-wide password requirements

### activities
List and monitor activities in PowerProtect Data Manager.

#### Available Commands
- `activities-browser` - Interactive browser for PPDM job groups ONLY
- `failed` - List failed activities with restartable flag
- `list` - List all activities (API: getAllActivities)
- `retry` - Retry failed activities
- `retry-single` - Retry a single failed activity
- `show` - Show activity details (API: getActivityById)
- `statistics` - Show storage and performance statistics for activities (API: getAllActivities)
- `steps` - List steps for a specific activity

#### API Endpoints & Operation IDs
| Command | API Endpoint | Method | Operation ID | API Version |
|---------|-------------|--------|--------------|-------------|
| `activities list` | `/api/v3/activities` | GET | `getAllActivities` | v3 |
| `activities show` | `/api/v3/activities/{id}` | GET | `getActivityById` | v3 |
| `activities statistics` | `/api/v3/activities` | GET | `getAllActivities` | v3 |

#### Examples
```bash
# List all activities
ppdm-cli activities list

# List activities with filtering
ppdm-cli activities list --filter 'status eq "RUNNING"'

# Show activity details
ppdm-cli activities show --id "activity-id"

# Show failed activities with restartable flag
ppdm-cli activities failed

# Retry failed activities
ppdm-cli activities retry --filter 'status eq "FAILED"'

# Retry single failed activity
ppdm-cli activities retry-single --id "activity-id"

# Show storage and performance statistics
ppdm-cli activities statistics BACKUP --page-size 50

# List steps for a specific activity
ppdm-cli activities steps --id "activity-id"

# Export activities to CSV
ppdm-cli activities list --output csv > activities.csv
```

#### Output Formats
```bash
# Table format (default)
ppdm-cli activities list

# JSON format
ppdm-cli activities list --output json

# JSON-Raw for automation
ppdm-cli activities list --output json-raw
```

#### Statistics Categories
- **BACKUP** - Backup operations with storage statistics (compression, deduplication, transfer rates)
- **REPLICATE** - Replication operations with different metrics

#### Statistics Examples
```bash
# Show backup storage efficiency
ppdm-cli activities statistics BACKUP --search "database"

# Show backup statistics for time period
ppdm-cli activities statistics BACKUP --period "7d"

# Export statistics to JSON for analysis
ppdm-cli activities statistics BACKUP --output json | jq '.[] | {id, assetSizeGB: (.statistics.assetSizeInBytes / 1024 / 1024 / 1024), compressionRatio: (.statistics.reductionPercentage)}'
```

### activities-browser
Interactive browser for PPDM job groups ONLY.

This command provides a hierarchical navigation system:
- Level 1: List ONLY job groups (filterable by category, type, status, search) - excludes all jobs and tasks
- Level 2: Select a job group to show detailed information
- Level 3: Show individual tasks/sub-tasks of the selected job group

#### Important Notes
- Main window shows ONLY JOB_GROUP type activities with children
- Individual jobs and tasks are NOT shown in the main list

#### Navigation
- Use cursor keys to navigate
- Press Enter to drill down
- Press 'q' to quit at any level
- Press 'r' to refresh data

#### Examples
```bash
# Launch interactive browser
ppdm-cli activities-browser

# Browser with specific filter
ppdm-cli activities-browser --filter 'status eq "RUNNING"'

# Browser with category filter
ppdm-cli activities-browser --category BACKUP
```

### activity-cancellations-batch
Cancel multiple activities in batch.

#### Examples
```bash
# Cancel activities by filter
ppdm-cli activity-cancellations-batch cancel --filter 'status eq "RUNNING"'

# Cancel specific activities
ppdm-cli activity-cancellations-batch cancel --activity-ids "id1,id2,id3"

# Cancel with confirmation
ppdm-cli activity-cancellations-batch cancel --filter 'status eq "RUNNING"' --confirm
```

### activity-retries-batch
Retry multiple activities in batch.

#### Examples
```bash
# Retry failed activities
ppdm-cli activity-retries-batch retry --filter 'status eq "FAILED"'

# Retry specific activities
ppdm-cli activity-retries-batch retry --activity-ids "id1,id2,id3"

# Retry with dry-run
ppdm-cli activity-retries-batch retry --filter 'status eq "FAILED"' --dry-run
```

### activity
Manage PPDM activities (separate from activities command group).

#### API Operations
- getAllActivities - List all activities
- getActivityCategories - Get activity categories

#### Examples
```bash
# List activities
ppdm-cli activity list

# Show activity categories
ppdm-cli activity categories

# Get specific activity
ppdm-cli activity show --id "activity-id"
```
---

## Asset Management Commands

### agent-management
Manage PowerProtect Data Manager agent operations.

#### Examples
```bash
# List all agents
ppdm-cli agent-management list

# Show agent details
ppdm-cli agent-management show --id "agent-id"

# Install agent on host
ppdm-cli agent-management install --host "hostname" --package "linux-x64"

# Uninstall agent
ppdm-cli agent-management uninstall --id "agent-id"
```

### ai
AI-powered command interface with multi-provider LLM integration.

#### Features
- Natural language command processing
- Context-aware assistance
- Multi-provider LLM support
- Interactive chat interface

#### Examples
```bash
# Start AI assistant
ppdm-cli ai chat

# Ask for help with specific task
ppdm-cli ai ask "How do I create a backup policy?"

# Generate command from natural language
ppdm-cli ai generate "Show me all failed backup activities"

# Get configuration help
ppdm-cli ai explain "protection policies"
```

### asset-management
Manage PowerProtect Data Manager asset operations.

#### Examples
```bash
# List all assets
ppdm-cli asset-management list

# Show asset details
ppdm-cli asset-management show --id "asset-id"

# Search assets by name
ppdm-cli asset-management search --name "database"

# Enable asset for protection
ppdm-cli asset-management enable --id "asset-id"
```

### assets
Manage PPDM assets using V2 API operations.

#### API Endpoints & Operation IDs
| Command | API Endpoint | Method | Operation ID | API Version |
|---------|-------------|--------|--------------|-------------|
| `assets list` | `/api/v2/assets` | GET | `getAssets` | v2 |
| `assets show` | `/api/v2/assets/{id}` | GET | `getAsset` | v2 |
| `assets search` | `/api/v2/assets` | GET | `getAssets` | v2 |

#### Available Commands
- `list` - List all assets (API: getAssets)
- `search` - Search assets by name
- `show` - Show asset details (API: getAsset)

#### Examples
```bash
# List all assets
ppdm-cli assets list

# List assets with filtering
ppdm-cli assets list --filter 'type eq "VMWARE_VIRTUAL_MACHINE"'

# Search assets by name
ppdm-cli assets search --name "production"

# Show asset details
ppdm-cli assets show --id "asset-id"

# Export assets to CSV
ppdm-cli assets list --output csv > assets.csv
```

---

## Protection & Backup Commands

### backup
Create backup operations with protection policy enforcement.

#### API Operations
- createServerDrBackup - Create server disaster recovery backup
- getServerDrBackups - List server disaster recovery backups
- deleteServerDrBackup - Delete server disaster recovery backup
- getServerDrBackup - Get server disaster recovery backup details
- updateServerDrBackup - Update server disaster recovery backup
- getVmBackupSettings - Get VM backup settings
- updateVmBackupSettings - Update VM backup settings
- server-disaster-recovery-backup-reconciliation - Reconcile backups

#### Examples
```bash
# Create server disaster recovery backup
ppdm-cli backup create-server-dr --name "DR-Backup" --asset-id "asset-id"

# List server DR backups
ppdm-cli backup list-server-dr

# Get VM backup settings
ppdm-cli backup get-vm-settings --asset-id "vm-id"

# Update VM backup settings
ppdm-cli backup update-vm-settings --asset-id "vm-id" --settings-file settings.json

# Reconcile backups
ppdm-cli backup reconcile-server-dr --asset-id "asset-id"
```

### backup-browser
Interactive TUI browser for selecting assets and starting backups.

#### Features
- Interactive asset selection
- Filter by asset type and name
- Quick backup initiation
- Real-time backup monitoring

#### Examples
```bash
# Launch interactive backup browser
ppdm-cli backup-browser

# Browser with specific asset type filter
ppdm-cli backup-browser --asset-type "VMWARE_VIRTUAL_MACHINE"

# Browser with name filter
ppdm-cli backup-browser --search "production"
```

### backup-restore
Manage PowerProtect Data Manager backup and restore operations.

#### Examples
```bash
# List available backups
ppdm-cli backup-restore list-backups --asset-id "asset-id"

# Restore from backup
ppdm-cli backup-restore restore --backup-id "backup-id" --target-id "target-id"

# Show restore status
ppdm-cli backup-restore status --restore-id "restore-id"

# Cancel restore operation
ppdm-cli backup-restore cancel --restore-id "restore-id"
```
---

## Monitoring Commands

### alerts
Manage PPDM alerts (API: getAlerts).

#### API Endpoints & Operation IDs
| Command | API Endpoint | Method | Operation ID | API Version |
|---------|-------------|--------|--------------|-------------|
| `alerts list` | `/api/v2/alerts` | GET | `getAlerts` | v2 |
| `alerts show` | `/api/v2/alerts/{id}` | GET | `getAlert` | v2 |

#### Examples
```bash
# List all alerts
ppdm-cli alerts list

# List critical alerts
ppdm-cli alerts list --filter 'severity eq "CRITICAL"'

# Show alert details
ppdm-cli alerts show --id "alert-id"

# Export alerts to JSON
ppdm-cli alerts list --output json > alerts.json
```

### monitor
Real-time monitoring of PPDM activities (API: getAllActivities).

#### Features
- Real-time activity monitoring
- Configurable refresh intervals
- Filter by activity type and status
- Export capabilities

#### Examples
```bash
# Start real-time monitoring
ppdm-cli monitor

# Monitor with specific filter
ppdm-cli monitor --filter 'status eq "RUNNING"'

# Monitor with custom refresh interval
ppdm-cli monitor --interval 30

# Monitor specific activity type
ppdm-cli monitor --category BACKUP
```

### monitoring-metrics
Monitor PPDM system metrics and health status (API: getSystemHealthMetrics).

#### Examples
```bash
# Show system health metrics
ppdm-cli monitoring-metrics health

# Show performance metrics
ppdm-cli monitoring-metrics performance

# Show resource utilization
ppdm-cli monitoring-metrics resources

# Export metrics to JSON
ppdm-cli monitoring-metrics health --output json
```

### web-monitor
Start web UI for PPDM activity monitoring (API: getAllActivities).

#### Features
- Web-based monitoring interface
- Real-time activity updates
- Responsive design
- WebSocket live updates

#### Examples
```bash
# Start web monitor on default port 8080
ppdm-cli web-monitor

# Start on custom port
ppdm-cli web-monitor --port 9090

# Start with custom host
ppdm-cli web-monitor --host 0.0.0.0 --port 8080

# Start with specific refresh interval
ppdm-cli web-monitor --interval 10
```

---

## Configuration Commands

### config
Manage PPDM configuration.

#### Available Commands
- `list` - List all configurations
- `add` - Add new PPDM server configuration
- `remove` - Remove PPDM server configuration
- `change` - Update existing configuration
- `default` - Set default PPDM server
- `show` - Show configuration details

#### Examples
```bash
# List all PPDM server configurations
ppdm-cli config list

# Add new PPDM server
ppdm-cli config add --ppdm-server "ppdm.example.com" --username <USERNAME> --password secret

# Set default server
ppdm-cli config default --name "production"

# Show current configuration
ppdm-cli config show

# Update server configuration
ppdm-cli config change --name "production" --username newuser --password newpass
```

### configurations
Manage PPDM appliance configurations (API: getConfigurations).

#### API Endpoints & Operation IDs
| Command | API Endpoint | Method | Operation ID | API Version |
|---------|-------------|--------|--------------|-------------|
| `configurations list` | `/api/v2/configurations` | GET | `getConfigurations` | v2 |
| `configurations show` | `/api/v2/configurations/{id}` | GET | `getConfiguration` | v2 |
| `configurations status` | `/api/v2/configurations/status` | GET | `getConfigStatus` | v2 |

#### Examples
```bash
# List all appliance configurations
ppdm-cli configurations list

# Show configuration details
ppdm-cli configurations show --id "config-id"

# Get configuration status
ppdm-cli configurations status

# Export configurations to JSON
ppdm-cli configurations list --output json
```

### common-settings
Manage PPDM common settings (API: getCommonSettings).

#### Examples
```bash
# List common settings
ppdm-cli common-settings list

# Show specific setting
ppdm-cli common-settings show --key "setting-key"

# Update common setting
ppdm-cli common-settings update --key "setting-key" --value "new-value"
```

---

## Infrastructure Commands

### infrastructure-nodes
Manage PowerProtect Data Manager infrastructure nodes (API: getInfrastructureNodes).

#### Examples
```bash
# List all infrastructure nodes
ppdm-cli infrastructure-nodes list

# Show node details
ppdm-cli infrastructure-nodes show --id "node-id"

# Get node status
ppdm-cli infrastructure-nodes status --id "node-id"
```

### infrastructure-objects
Manage infrastructure objects in PowerProtect Data Manager (API: getInfrastructureObjects).

#### Examples
```bash
# List infrastructure objects
ppdm-cli infrastructure-objects list

# Show object details
ppdm-cli infrastructure-objects show --id "object-id"

# Search objects by type
ppdm-cli infrastructure-objects list --filter 'type eq "HOST"'
```

### inventory-sources
Manage inventory sources in PowerProtect Data Manager (API: getInventorySources).

#### API Endpoints & Operation IDs
| Command | API Endpoint | Method | Operation ID | API Version |
|---------|-------------|--------|--------------|-------------|
| `inventory-sources list` | `/api/v2/inventory-sources` | GET | `getInventorySources` | v2 |
| `inventory-sources show` | `/api/v2/inventory-sources/{id}` | GET | `getInventorySource` | v2 |

#### Examples
```bash
# List all inventory sources
ppdm-cli inventory-sources list

# Show inventory source details
ppdm-cli inventory-sources show --id "source-id"

# Create new inventory source
ppdm-cli inventory-sources create --name "vCenter" --type "VMWARE_VCENTER" --address "vcenter.example.com"
```

### nodes
Manage PPDM appliance nodes (API: getNodes).

#### Examples
```bash
# List all appliance nodes
ppdm-cli nodes list

# Show node details
ppdm-cli nodes show --id "node-id"

# Get node health status
ppdm-cli nodes health --id "node-id"
```

### storage-systems
Manage PPDM storage systems (API: getStorageSystems).

#### Examples
```bash
# List all storage systems
ppdm-cli storage-systems list

# Show storage system details
ppdm-cli storage-systems show --id "storage-id"

# Get storage capacity
ppdm-cli storage-systems capacity --id "storage-id"
```

---

## Security & Identity Commands

### active-directory
Manage Active Directory identity providers in PPDM.

#### Examples
```bash
# List all Active Directory identity providers
ppdm-cli active-directory list

# Get default AD configuration
ppdm-cli active-directory default-config

# Get specific AD provider
ppdm-cli active-directory get --locator ad-provider-123

# Create new AD provider
ppdm-cli active-directory create --name "AD Corp" --host "dc.corp.com" --port 389

# Delete AD provider
ppdm-cli active-directory delete --locator ad-provider-123

# Update AD provider
ppdm-cli active-directory update --locator ad-provider-123 --name "Updated AD"
```

### audit-compliance
Manage PowerProtect Data Manager audit and compliance operations (API: getAuditLogs).

#### Examples
```bash
# List audit logs
ppdm-cli audit-compliance list

# Filter audit logs by date
ppdm-cli audit-compliance list --start-date "2023-01-01" --end-date "2023-01-31"

# Search audit logs
ppdm-cli audit-compliance search --query "user login"

# Export audit logs
ppdm-cli audit-compliance list --output json > audit-logs.json
```

### certificates
Manage PPDM certificates (API: getCertificates).

#### Examples
```bash
# List all certificates
ppdm-cli certificates list

# Show certificate details
ppdm-cli certificates show --id "cert-id"

# Upload certificate
ppdm-cli certificates upload --file certificate.pem --name "SSL Cert"

# Delete certificate
ppdm-cli certificates delete --id "cert-id"
```

### credentials
Manage PPDM credentials (API: getCredentials).

#### API Endpoints & Operation IDs
| Command | API Endpoint | Method | Operation ID | API Version |
|---------|-------------|--------|--------------|-------------|
| `credentials list` | `/api/v2/credentials` | GET | `getCredentials` | v2 |
| `credentials show` | `/api/v2/credentials/{id}` | GET | `getCredential` | v2 |

#### Examples
```bash
# List all credentials
ppdm-cli credentials list

# Show credential details
ppdm-cli credentials show --id "credential-id"

# Create new credential
ppdm-cli credentials create --name "vCenter Creds" --type "USERNAME_PASSWORD" --username <USERNAME> --password secret

# Delete credential
ppdm-cli credentials delete --id "credential-id"
```

### identity-access-management
Manage PowerProtect Data Manager Identity and Access Management (API: getIdentityProviders).

#### Examples
```bash
# List identity providers
ppdm-cli identity-access-management list

# Show identity provider details
ppdm-cli identity-access-management show --id "provider-id"

# Create identity provider
ppdm-cli identity-access-management create --name "LDAP" --type "LDAP" --config ldap-config.json

# Update identity provider
ppdm-cli identity-access-management update --id "provider-id" --config updated-config.json
```

### identity-provider
Manage identity providers and authentication systems.

#### Examples
```bash
# List identity providers
ppdm-cli identity-provider list

# Show provider configuration
ppdm-cli identity-provider show --name "Active Directory"

# Test provider connectivity
ppdm-cli identity-provider test --name "Active Directory"

# Sync provider users
ppdm-cli identity-provider sync --name "Active Directory"
```

---

## Advanced Commands

### ai
AI-powered command interface with multi-provider LLM integration.

#### Features
- Natural language command processing
- Context-aware assistance
- Multi-provider LLM support
- Interactive chat interface

#### Examples
```bash
# Start AI assistant
ppdm-cli ai chat

# Ask for help with specific task
ppdm-cli ai ask "How do I create a backup policy?"

# Generate command from natural language
ppdm-cli ai generate "Show me all failed backup activities"

# Get configuration help
ppdm-cli ai explain "protection policies"
```

### avamar
Manage Avamar inventory sources and assets.

#### Examples
```bash
# List Avamar inventory sources
ppdm-cli avamar list-sources

# Show Avamar asset details
ppdm-cli avamar show-asset --id "asset-id"

# Configure Avamar integration
ppdm-cli avamar configure --host "avamar.example.com" --port 27000
```

### copies
Analyze PPDM backup copies (API: getCopies).

#### API Endpoints & Operation IDs
| Command | API Endpoint | Method | Operation ID | API Version |
|---------|-------------|--------|--------------|-------------|
| `copies list` | `/api/v2/copies` | GET | `getCopies` | v2 |
| `copies show` | `/api/v2/copies/{id}` | GET | `getCopy` | v2 |

#### Examples
```bash
# List all backup copies
ppdm-cli copies list

# Show copy details
ppdm-cli copies show --id "copy-id"

# Filter copies by asset
ppdm-cli copies list --filter 'assetId eq "asset-id"'

# Export copies to CSV
ppdm-cli copies list --output csv > copies.csv
```

### copies-browser
Interactive TUI browser for browsing and sorting backup copies.

#### Features
- Interactive copy selection
- Sort by date, size, type
- Filter by asset and status
- Quick copy actions

#### Examples
```bash
# Launch interactive copies browser
ppdm-cli copies-browser

# Browser with specific asset filter
ppdm-cli copies-browser --asset-id "asset-id"

# Browser sorted by date
ppdm-cli copies-browser --sort-by date
```

### curl
Direct API access to any PPDM endpoint.

#### Features
- Direct API endpoint access
- Custom HTTP methods
- Request/response inspection
- Authentication handled automatically

#### Examples
```bash
# GET request to API endpoint
ppdm-cli curl GET /api/v2/assets

# POST request with body
ppdm-cli curl POST /api/v2/protection-policies --body '{"name":"Test Policy"}'

# Custom headers
ppdm-cli curl GET /api/v2/activities --headers "X-Custom-Header: value"

# Show response details
ppdm-cli curl GET /api/v2/configurations --show-response
```

### datadomain-mtrees
Manage PowerProtect Data Domain MTrees (API: getDataDomainMTrees).

#### Examples
```bash
# List DataDomain MTrees
ppdm-cli datadomain-mtrees list

# Show MTree details
ppdm-cli datadomain-mtrees show --id "mtree-id"

# Create MTree
ppdm-cli datadomain-mtrees create --name "backup-mtree" --datastore-id "ds-id"

# Delete MTree
ppdm-cli datadomain-mtrees delete --id "mtree-id"
```

### discoveries
Manage PowerProtect Data Manager discoveries (API: getAllDiscoveries).

#### Examples
```bash
# List all discoveries
ppdm-cli discoveries list

# Show discovery details
ppdm-cli discoveries show --id "discovery-id"

# Trigger new discovery
ppdm-cli discoveries create --inventory-source-id "source-id"

# Show discovery status
ppdm-cli discoveries status --id "discovery-id"
```

### exported-copies
Manage exported copies.

#### Examples
```bash
# List exported copies
ppdm-cli exported-copies list

# Show exported copy details
ppdm-cli exported-copies show --id "copy-id"

# Export copy to cloud
ppdm-cli exported-copies export --copy-id "copy-id" --target "s3://bucket"

# Cancel export
ppdm-cli exported-copies cancel --export-id "export-id"
```

### ghvdm-host-configuration-batch
Manage GHVDM host configuration batch operations.

#### Examples
```bash
# List batch configurations
ppdm-cli ghvdm-host-configuration-batch list

# Create batch configuration
ppdm-cli ghvdm-host-configuration-batch create --config-file batch.json

# Apply batch configuration
ppdm-cli ghvdm-host-configuration-batch apply --batch-id "batch-id"

# Show batch status
ppdm-cli ghvdm-host-configuration-batch status --batch-id "batch-id"
```

### health-event-catalogs
Manage health event catalogs.

#### Examples
```bash
# List health event catalogs
ppdm-cli health-event-catalogs list

# Show catalog details
ppdm-cli health-event-catalogs show --catalog-id "catalog-id"

# Search health events
ppdm-cli health-event-catalogs search --query "storage"
```

### kubernetes
Manage Kubernetes clusters.

#### Examples
```bash
# List Kubernetes clusters
ppdm-cli kubernetes list

# Show cluster details
ppdm-cli kubernetes show --name "prod-cluster"

# Add Kubernetes cluster
ppdm-cli kubernetes add --name "dev-cluster" --endpoint "https://k8s-dev.example.com"

# Remove Kubernetes cluster
ppdm-cli kubernetes remove --name "old-cluster"
```

### metrics
Retrieve PPDM metrics (API: getProtectionMetrics, getAssetProtectionMetrics, getResourceMetrics).

#### Examples
```bash
# Get protection metrics
ppdm-cli metrics protection

# Get asset protection metrics
ppdm-cli metrics assets --asset-id "asset-id"

# Get resource metrics
ppdm-cli metrics resources --resource-type "STORAGE"

# Export metrics to JSON
ppdm-cli metrics protection --output json > metrics.json
```

### migrate
Migrate exported copy to another datastore (API: migrateExportedCopy).

#### Examples
```bash
# Migrate exported copy
ppdm-cli migrate --copy-id "copy-id" --target-datastore "new-store"

# List available migrations
ppdm-cli migrate list

# Show migration status
ppdm-cli migrate status --migration-id "migration-id"

# Cancel migration
ppdm-cli migrate cancel --migration-id "migration-id"
```

### resource-group
Manage PPDM resource groups (API: getResourceGroups).

#### Examples
```bash
# List resource groups
ppdm-cli resource-group list

# Show resource group details
ppdm-cli resource-group show --id "group-id"

# Create resource group
ppdm-cli resource-group create --name "Production" --description "Production resources"

# Update resource group
ppdm-cli resource-group update --id "group-id" --description "Updated description"
```

### snmp-hosts
Manage SNMP hosts (API: getSnmpHosts).

#### Examples
```bash
# List SNMP hosts
ppdm-cli snmp-hosts list

# Show SNMP host details
ppdm-cli snmp-hosts show --id "host-id"

# Add SNMP host
ppdm-cli snmp-hosts add --host "snmp.example.com" --community "public"

# Remove SNMP host
ppdm-cli snmp-hosts remove --id "host-id"
```

### snmp-service
Manage SNMP service configuration (API: getSnmpService).

#### Examples
```bash
# Show SNMP service configuration
ppdm-cli snmp-service show

# Update SNMP service
ppdm-cli snmp-service update --enabled true --community "newcommunity"

# Test SNMP configuration
ppdm-cli snmp-service test
```

### syslogs
Syslogs configuration management (API: getSyslogsConfigurations).

#### Examples
```bash
# List syslog configurations
ppdm-cli syslogs list

# Show syslog configuration
ppdm-cli syslogs show --id "config-id"

# Create syslog configuration
ppdm-cli syslogs create --host "syslog.example.com" --port 514

# Test syslog configuration
ppdm-cli syslogs test --id "config-id"
---

## System Commands

### health
Monitor PPDM system health (API: getHealthCheckTypes).

#### Examples
```bash
# Run health check
ppdm-cli health check

# Show specific health check type
ppdm-cli health show --type "STORAGE"

# Run comprehensive health check
ppdm-cli health comprehensive

# Export health report
ppdm-cli health check --output json > health-report.json
```

### status
Check PPDM system status (API: systemStatus).

#### Examples
```bash
# Show system status
ppdm-cli status

# Show detailed status
ppdm-cli status --detailed

# Check specific component
ppdm-cli status --component "database"

# Export status to JSON
ppdm-cli status --output json
```

### storage
Manage storage profiles and verification (API: verifyStorageProfile).

#### Examples
```bash
# List storage profiles
ppdm-cli storage list

# Verify storage profile
ppdm-cli storage verify --profile-id "profile-id"

# Show storage capacity
ppdm-cli storage capacity

# Create storage profile
ppdm-cli storage create --name "Backup Storage" --type "DATA_DOMAIN"
```

### update
Update PPDM CLI to the latest version (API: selfUpdate).

#### Examples
```bash
# Check for updates
ppdm-cli update check

# Update to latest version
ppdm-cli update install

# Update to specific version
ppdm-cli update install --version "20.1.0.2"

# Show update history
ppdm-cli update history
```

### upgrade
Manage PPDM upgrades (API: getUpgradePackages).

#### Examples
```bash
# List available upgrade packages
ppdm-cli upgrade list

# Show upgrade package details
ppdm-cli upgrade show --package-id "package-id"

# Download upgrade package
ppdm-cli upgrade download --package-id "package-id"

# Check upgrade compatibility
ppdm-cli upgrade check --package-id "package-id"
```

---

## Protection Policy Commands

### protection-configurations
Manage protection policy configurations (API: createProtectionPolicyConfiguration).

#### Examples
```bash
# List protection configurations
ppdm-cli protection-configurations list

# Show configuration details
ppdm-cli protection-configurations show --id "config-id"

# Create protection configuration
ppdm-cli protection-configurations create --name "Config" --settings-file settings.json

# Update protection configuration
ppdm-cli protection-configurations update --id "config-id" --settings-file updated.json
```

### protection-policies
Manage protection policies (API: getProtectionPolicies, getProtectionPolicyById).

#### API Endpoints & Operation IDs
| Command | API Endpoint | Method | Operation ID | API Version |
|---------|-------------|--------|--------------|-------------|
| `protection-policies list` | `/api/v3/protection-policies` | GET | `getProtectionPolicies` | v3 |
| `protection-policies show` | `/api/v3/protection-policies/{id}` | GET | `getProtectionPolicyById` | v3 |

#### Available Commands
- `list` - List protection policies (API: getProtectionPolicies)
- `show` - Show protection policy details (API: getProtectionPolicyById)
- `show-objective` - Show protection policy objectives details
- `create` - Create protection policy with flexible scheduling and configuration
- `delete` - Delete protection policy (API: deleteProtectionPolicy)
- `download` - Download protection policy configuration (API: getProtectionPolicyById)
- `search` - Search protection policies by name with flexible filtering
- `update` - Update protection policy from file (API: partialUpdateProtectionPolicy)
- `asset-assignments` - Manage asset assignments for protection policies

#### Examples
```bash
# List all protection policies
ppdm-cli protection-policies list

# Filter policies by name
ppdm-cli protection-policies list -f "name co \"Production\""

# Show specific policy details
ppdm-cli protection-policies show --id "policy-123"

# Create new protection policy
ppdm-cli protection-policies create --from-file policy.json

# Update multiple policies (batch)
ppdm-cli protection-policies batch-update --policy-ids "policy-123,policy-456" --json '{"disabled": false}'

# Configure global policy settings
ppdm-cli protection-policies settings --view "full"

# Trigger on-demand protection
ppdm-cli protection-policies protect --policy-id "policy-123" --asset-id "asset-456"
```

### protection-policy-summaries
Manage PowerProtect Data Manager V3 protection policy summaries (API: getProtectionPolicySummaries).

#### Examples
```bash
# List protection policy summaries
ppdm-cli protection-policy-summaries list

# Show policy summary details
ppdm-cli protection-policy-summaries show --id "policy-id"

# Get policy statistics
ppdm-cli protection-policy-summaries statistics

# Export summaries to JSON
ppdm-cli protection-policy-summaries list --output json > summaries.json
```

---

## Deprecated Commands

### identity-access
Manage PPDM identity access provisions [DEPRECATED].

#### Note
This command is deprecated. Use `identity-access-management` instead.

#### Examples
```bash
# List identity access provisions (deprecated)
ppdm-cli identity-access list

# Show access details (deprecated)
ppdm-cli identity-access show --id "access-id"
```

### policies
Manage PPDM protection policies [DEPRECATED].

#### Note
This command is deprecated. Use `protection-policies` instead.

#### Examples
```bash
# List policies (deprecated)
ppdm-cli policies list

# Show policy details (deprecated)
ppdm-cli policies show --id "policy-id"
```

---

## Documentation Summary

This comprehensive command reference covers all 58 PPDM CLI commands organized into logical categories:

- **Utility Commands** (4): about, completion, help, version
- **Core Management Commands** (8): account, activities, activities-browser, activity-cancellations-batch, activity-retries-batch, activity, agent-management, ai
- **Asset Management Commands** (3): asset-management, assets, audit-compliance
- **Protection & Backup Commands** (3): backup, backup-browser, backup-restore
- **Monitoring Commands** (4): alerts, monitor, monitoring-metrics, web-monitor
- **Configuration Commands** (3): config, configurations, common-settings
- **Infrastructure Commands** (5): infrastructure-nodes, infrastructure-objects, inventory-sources, nodes, storage-systems
- **Security & Identity Commands** (7): active-directory, certificates, credentials, identity-access-management, identity-provider
- **Advanced Commands** (17): ai, avamar, copies, copies-browser, curl, datadomain-mtrees, discoveries, exported-copies, ghvdm-host-configuration-batch, health-event-catalogs, kubernetes, metrics, migrate, resource-group, snmp-hosts, snmp-service, syslogs
- **System Commands** (4): health, status, storage, update, upgrade
- **Protection Policy Commands** (3): protection-configurations, protection-policies, protection-policy-summaries
- **Deprecated Commands** (2): identity-access, policies

Each command includes:
- Comprehensive descriptions
- API operation IDs and endpoints where applicable
- Practical examples
- Output format options
- Usage patterns and best practices

---

## Global Flags Reference

All PPDM CLI commands support the following global flags:

```bash
--debug                    Enable debug/trace mode for API calls and responses
--dry-run                  Show API calls and JSON bodies without executing (secrets redacted)
-h, --help                 Help for any command
--ppdm-server string       Select specific PPDM server from configuration
-v, --version             Show version information
--log-level string        Log level (error, warn, info, debug)
--quiet                   Suppress log messages for clean output
--show-api-calls          Show redacted API requests and responses
--ppdm-log string         Enable OpenTelemetry logging to file or stdout
--otel-enabled            Enable OpenTelemetry observability (traces and metrics)
```

## Output Formats

Most commands support multiple output formats:
- `table` (default) - Human-readable table format
- `json` - Structured JSON output
- `yaml` - YAML format output
- `csv` - CSV format for data export
- `json-raw` - Raw API response JSON (for automation)
- `yaml-raw` - Raw API response YAML (for automation)

## API Operations Reference

This documentation includes API operation IDs for all commands that interact with PPDM APIs. The operation IDs follow this pattern:

- **Operation ID**: The specific API operation identifier
- **API Version**: The PPDM API version (v1, v2, v3)
- **Endpoint**: The HTTP method and API endpoint
- **API Reference**: The PPDM API specification file
- **Dell API**: Reference to Dell's official API documentation

For complete API documentation, see the Dell PPDM API documentation at:
https://apidog.dell.com/apidoc/docs-site/3569/llms.txt

---

**PPDM CLI Complete Command Reference - Version 20.1.0.1**

*This documentation covers all 58 PPDM CLI commands with comprehensive examples, API references, and usage patterns.*
