# PPDM CLI Command Reference Guide

## 📋 Table of Contents

### 🎯 Quick Navigation
| Command | Description | Link |
|----------|-------------|-------|
| **account** | Manage PowerProtect Data Manager account operations | [Account Management](#account-management) |
| **active-directory** | Manage Active Directory identity providers | [Active Directory](#active-directory) |
| **activities** | Monitor PPDM activities | [Activities Monitoring](#activities-monitoring) |
| **activities-browser** | Interactive browser for PPDM job groups | [Activities Browser](#activities-browser) |
| **activity-cancellations-batch** | Cancel multiple activities in batch | [Activity Cancellations](#activity-cancellations-batch) |
| **activity-categories** | Manage activity categories and types | [Activity Categories](#activity-categories) |
| **activity-initiated-types** | List activity initiated types | [Activity Initiated Types](#activity-initiated-types) |
| **activity-retries-batch** | Retry multiple activities in batch | [Activity Retries](#activity-retries-batch) |
| **activity-subcategories** | Manage activity subcategories and their mappings | [Activity Subcategories](#activity-subcategories) |
| **agent-management** | Manage PowerProtect Data Manager agent operations | [Agent Management](#agent-management) |
| **alerts** | Manage PPDM alerts | [Alerts Management](#alerts-management) |
| **asset-management** | Manage PowerProtect Data Manager asset operations | [Asset Management](#asset-management) |
| **assets** | Manage PPDM assets | [Assets Management](#assets-management) |
| **audit-compliance** | Manage PowerProtect Data Manager audit and compliance operations | [Audit Compliance](#audit-compliance) |
| **avamar** | Manage Avamar inventory sources and assets | [Avamar Management](#avamar-management) |
| **backup** | Create backup operations with protection policy enforcement | [Backup Operations](#backup-operations) |
| **backup-browser** | Interactive TUI browser for selecting assets and starting backups | [Backup Browser](#backup-browser) |
| **backup-restore** | Manage PowerProtect Data Manager backup and restore operations | [Backup Restore](#backup-restore) |
| **common-settings** | Manage PPDM common settings | [Common Settings](#common-settings) |
| **completion** | Generate completion script for specified shell | [Shell Completion](#shell-completion) |
| **config** | Manage PPDM configuration | [Configuration](#configuration) |
| **configurations** | Manage PPDM appliance configurations | [Configurations](#configurations) |
| **copies** | Analyze PPDM backup copies | [Copies Analysis](#copies-analysis) |
| **copies-browser** | Interactive TUI browser for browsing and sorting backup copies | [Copies Browser](#copies-browser) |
| **credentials** | Manage PPDM credentials | [Credentials Management](#credentials-management) |
| **curl** | Direct API access to any PPDM endpoint | [Direct API Access](#direct-api-access) |
| **datadomain-mtrees** | Manage PowerProtect Data Domain MTrees | [DataDomain MTrees](#datadomain-mtrees) |
| **ghvdm-host-configuration-batch** | Manage GHVDM host configuration batch operations | [GHVDM Host Configuration](#ghvdm-host-configuration) |
| **health** | Monitor PPDM system health | [Health Monitoring](#health-monitoring) |
| **health-event-catalogs** | Manage health event catalogs | [Health Event Catalogs](#health-event-catalogs) |
| **help** | Help about any command | [Help](#help) |
| **hosts** | Manage PowerProtect Data Manager hosts | [Hosts Management](#hosts-management) |
| **identity-access** | Manage PPDM identity access provisions | [Identity Access](#identity-access) |
| **identity-access-management** | Manage PowerProtect Data Manager Identity and Access Management | [Identity Access Management](#identity-access-management) |
| **identity-providers** | Manage identity providers and authentication systems | [Identity Providers](#identity-providers) |
| **infrastructure-nodes** | Manage PowerProtect Data Manager infrastructure nodes | [Infrastructure Nodes](#infrastructure-nodes) |
| **inventory-sources** | Manage inventory sources in PowerProtect Data Manager | [Inventory Sources](#inventory-sources) |
| **kubernetes** | Manage Kubernetes clusters | [Kubernetes Management](#kubernetes-management) |
| **metrics** | Retrieve PPDM metrics | [Metrics](#metrics) |
| **migrate** | Migrate exported copy to another datastore | [Migration](#migration) |
| **monitor** | Real-time monitoring of PPDM activities | [Real-time Monitoring](#real-time-monitoring) |
| **monitoring-metrics** | Monitor PPDM system metrics and health status | [Monitoring Metrics](#monitoring-metrics) |
| **multi-schedule** | Manage multi-schedule functionality | [Multi-Schedule](#multi-schedule) |
| **nodes** | Manage PPDM appliance nodes | [Nodes Management](#nodes-management) |
| **policies** | Manage PPDM protection policies | [Policies Management](#policies-management) |
| **protection-configurations** | Manage protection policy configurations | [Protection Configurations](#protection-configurations) |
| **protection-policies** | Manage protection policies with enhanced V3 functionality | [Protection Policies](#protection-policies) |
| **protection-policy-summaries** | Manage PowerProtect Data Manager V3 protection policy summaries | [Protection Policy Summaries](#protection-policy-summaries) |
| **resource-group** | Manage PPDM resource groups | [Resource Groups](#resource-groups) |
| **snmp-hosts** | Manage SNMP hosts | [SNMP Hosts](#snmp-hosts) |
| **snmp-service** | Manage SNMP service configuration | [SNMP Service](#snmp-service) |
| **status** | Check PPDM system status | [Status](#status) |
| **storage-systems** | Manage storage systems | [Storage Systems](#storage-systems) |
| **upgrade** | Manage PPDM upgrades | [Upgrade Management](#upgrade-management) |
| **verify-cloud-storage** | Verify cloud storage profile connection | [Cloud Storage Verification](#cloud-storage-verification) |
| **verify-object-storage** | Verify object storage profile connection | [Object Storage Verification](#object-storage-verification) |
| **version** | Show version information | [Version](#version) |

### 📚 Detailed Sections
- [Global Configuration](#-global-configuration) - Multi-host setup and environment variables
- [Global Flags](#-global-flags) - Common flags available for all commands
- [Filter Expressions](#-filter-expressions) - Filtering syntax and examples
- [Protection Policies](#protection-policies) - Policy management with multi-schedule support
- [Multi-Schedule Functionality](#multi-schedule) - Advanced multi-schedule policy creation
- [PPDM-Baremetal Testing](#ppdm-baremetal-testing) - Live testing methodology with PPDM
- [Assets](#assets-management) - Asset management and operations
- [Activities](#activities-monitoring) - Activity monitoring and management
- [Inventory](#inventory-sources) - Inventory source management
- [Nodes](#nodes-management) - Infrastructure node management
- [Storage](#storage-systems) - Storage system management
- [Users](#users-management) - User management
- [Tenants](#tenants-management) - Tenant management
- [System Health](#health-monitoring) - Health monitoring
- [Advanced Usage](#advanced-usage) - Advanced CLI usage patterns
- [Error Handling](#error-handling) - Error handling and troubleshooting
- [Output Formats](#-output-formats) - Available output formats and examples
- [Error Handling](#-error-handling) - Common errors and troubleshooting
- [Advanced Usage](#-advanced-usage) - Multi-host operations and debug mode

---

## Overview

This guide provides comprehensive reference documentation for all PPDM CLI commands, including usage examples, flags, and output formats.

## 🌐 Global Configuration

### Multi-Host Setup
Create `~/.ppdm/config.json` with multiple hosts:

```json
{
  "hosts": {
    "staging": {
      "host": "ppdm-staging.example.com",
      "port": 8443,
      "username": "admin",
      "password": "password",
      "insecure": true
    },
    "production": {
      "host": "ppdm-prod.example.com", 
      "port": 8443,
      "username": "admin",
      "password": "password",
      "insecure": false
    }
  },
  "default": "staging"
}
```

### Environment Variables
```bash
export PPDM_HOST="ppdm-server.example.com"
export PPDM_USERNAME="admin"
export PPDM_PASSWORD="your-password"
export PPDM_PORT="8443"
export PPDM_DEBUG="true"
export PPDM_CONFIG_FILE="/path/to/config.json"
```

## 🎯 Global Flags

| Flag | Type | Description | Example |
|------|------|-------------|---------|
| `--host` | string | Specify PPDM host (multi-host) | `--host staging` |
| `--debug` | bool | Enable debug mode | `--debug` |
| `--output` | string | Output format | `--output json` |
| `--filter` | string | Filter expression | `--filter "name eq 'test'"` |
| `--orderby` | string | Order by field | `--orderby "createdAt desc"` |
| `--page` | int | Page number | `--page 2` |
| `--page-size` | int | Page size | `--page-size 50` |
| `--dry-run` | bool | Preview API calls | `--dry-run` |

## 📋 Commands

### 🏗️ Core Management Commands

#### Account Management {#account-management}
```bash
ppdm-cli account [subcommand] [flags]
```
**Description**: Manage PowerProtect Data Manager account operations and settings.

#### Active Directory {#active-directory}
```bash
ppdm-cli active-directory [subcommand] [flags]
```
**Description**: Manage Active Directory identity providers and authentication.

#### Asset Management {#asset-management}
```bash
ppdm-cli asset-management [subcommand] [flags]
```
**Description**: Manage PowerProtect Data Manager asset operations with advanced features.

#### Assets Management {#assets-management}

#### Configuration {#configuration}
```bash
ppdm-cli config [subcommand] [flags]
```
**Description**: Manage PPDM configuration files and settings.

#### Credentials Management {#credentials-management}
```bash
ppdm-cli credentials [subcommand] [flags]
```
**Description**: Manage PPDM credentials and authentication profiles.

#### Help {#help}
```bash
ppdm-cli help [command]
```
**Description**: Get help information about any command.

#### Hosts Management {#hosts-management}
```bash
ppdm-cli hosts [subcommand] [flags]
```
**Description**: Manage PowerProtect Data Manager hosts and multi-host configurations.

#### Status {#status}
```bash
ppdm-cli status [flags]
```
**Description**: Check PPDM system status and health.

#### Version {#version}
```bash
ppdm-cli version [flags]
```
**Description**: Show PPDM CLI version information.

### 🔄 Data Protection Commands

#### Backup Operations {#backup-operations}
```bash
ppdm-cli backup [subcommand] [flags]
```
**Description**: Create backup operations with protection policy enforcement.

**Available subcommands:**
- `create` - Create a manual backup for assets (API: CreateProtection)
- `server-dr` - Manage server disaster recovery backups (API: getServerDrBackups, createServerDrBackup, deleteServerDrBackup, getServerDrBackup, updateServerDrBackup)
- `vm-settings` - Manage VM backup settings (API: getVmBackupSettings, updateVmBackupSettings)
- `reconcile` - Reconcile backup metadata (API: server-disaster-recovery-backup-reconciliation)

**Examples:**
```bash
# Create manual backup
ppdm-cli backup create --assets asset1,asset2

# List server DR backups
ppdm-cli backup server-dr list

# Get VM backup settings
ppdm-cli backup vm-settings get

# Reconcile backup metadata
ppdm-cli backup reconcile --backup-id backup-123
```

**Detailed documentation:** See [Backup Command Reference](commands/BACKUP.md) for comprehensive documentation.

#### Backup Browser {#backup-browser}
```bash
ppdm-cli backup-browser [flags]
```
**Description**: Interactive TUI browser for selecting assets and starting backups.

#### Backup Restore {#backup-restore}
```bash
ppdm-cli backup-restore [subcommand] [flags]
```
**Description**: Manage PowerProtect Data Manager backup and restore operations.

#### Protection Policies {#protection-policies}

#### Protection Configurations {#protection-configurations}
```bash
ppdm-cli protection-configurations [subcommand] [flags]
```
**Description**: Manage protection policy configurations.

#### Protection Policy Summaries {#protection-policy-summaries}
```bash
ppdm-cli protection-policy-summaries [subcommand] [flags]
```
**Description**: Manage PowerProtect Data Manager V3 protection policy summaries.

#### Policies Management {#policies-management}
```bash
ppdm-cli policies [subcommand] [flags]
```
**Description**: Manage PPDM protection policies (legacy command).

### 📊 Monitoring and Analysis Commands

#### Activities Monitoring {#activities-monitoring}

#### Activities Browser {#activities-browser}
```bash
ppdm-cli activities-browser [flags]
```
**Description**: Interactive browser for PPDM job groups and activities.

#### Activity Cancellations {#activity-cancellations-batch}
```bash
ppdm-cli activity-cancellations-batch [flags]
```
**Description**: Cancel multiple activities in batch operations.

#### Activity Categories {#activity-categories}
```bash
ppdm-cli activity-categories [subcommand] [flags]
```
**Description**: Manage activity categories and types.

#### Activity Initiated Types {#activity-initiated-types}
```bash
ppdm-cli activity-initiated-types [flags]
```
**Description**: List activity initiated types and configurations.

#### Activity Retries {#activity-retries-batch}
```bash
ppdm-cli activity-retries-batch [flags]
```
**Description**: Retry multiple activities in batch operations.

#### Activity Subcategories {#activity-subcategories}
```bash
ppdm-cli activity-subcategories [subcommand] [flags]
```
**Description**: Manage activity subcategories and their mappings.

#### Alerts Management {#alerts-management}

#### Copies Analysis {#copies-analysis}
```bash
ppdm-cli copies [subcommand] [flags]
```
**Description**: Analyze PPDM backup copies and metadata.

#### Copies Browser {#copies-browser}
```bash
ppdm-cli copies-browser [flags]
```
**Description**: Interactive TUI browser for browsing and sorting backup copies.

#### Health Monitoring {#health-monitoring}

#### Health Event Catalogs {#health-event-catalogs}
```bash
ppdm-cli health-event-catalogs [subcommand] [flags]
```
**Description**: Manage health event catalogs and monitoring.

#### Metrics {#metrics}
```bash
ppdm-cli metrics [subcommand] [flags]
```
**Description**: Retrieve PPDM system metrics and performance data.

#### Monitor {#real-time-monitoring}
```bash
ppdm-cli monitor [flags]
```
**Description**: Real-time monitoring of PPDM activities and system status.

#### Monitoring Metrics {#monitoring-metrics}
```bash
ppdm-cli monitoring-metrics [subcommand] [flags]
```
**Description**: Monitor PPDM system metrics and health status.

### 🏢 Infrastructure and Storage Commands

#### Nodes Management {#nodes-management}

#### Storage Systems {#storage-systems}

#### Infrastructure Nodes {#infrastructure-nodes}
```bash
ppdm-cli infrastructure-nodes [subcommand] [flags]
```
**Description**: Manage PowerProtect Data Manager infrastructure nodes.

#### DataDomain MTrees {#datadomain-mtrees}
```bash
ppdm-cli datadomain-mtrees [subcommand] [flags]
```
**Description**: Manage PowerProtect Data Domain MTrees and storage.

### 🔧 Advanced and Integration Commands

#### Identity and Access Management

#### Identity Providers {#identity-providers}
```bash
ppdm-cli identity-providers [subcommand] [flags]
```
**Description**: Manage identity providers and authentication systems.

#### Identity Access {#identity-access}
```bash
ppdm-cli identity-access [subcommand] [flags]
```
**Description**: Manage PPDM identity access provisions.

#### Identity Access Management {#identity-access-management}
```bash
ppdm-cli identity-access-management [subcommand] [flags]
```
**Description**: Manage PowerProtect Data Manager Identity and Access Management.

#### Agent Management {#agent-management}
```bash
ppdm-cli agent-management [subcommand] [flags]
```
**Description**: Manage PowerProtect Data Manager agent operations.

#### Audit Compliance {#audit-compliance}
```bash
ppdm-cli audit-compliance [subcommand] [flags]
```
**Description**: Manage PowerProtect Data Manager audit and compliance operations.

#### Avamar Management {#avamar-management}
```bash
ppdm-cli avamar [subcommand] [flags]
```
**Description**: Manage Avamar inventory sources and assets.

#### Kubernetes Management {#kubernetes-management}
```bash
ppdm-cli kubernetes [subcommand] [flags]
```
**Description**: Manage Kubernetes clusters and PPDM integration.

#### Inventory Sources {#inventory-sources}
```bash
ppdm-cli inventory-sources [subcommand] [flags]
```
**Description**: Manage inventory sources in PowerProtect Data Manager.

#### Resource Groups {#resource-groups}
```bash
ppdm-cli resource-group [subcommand] [flags]
```
**Description**: Manage PPDM resource groups and organization.

#### SNMP Management

#### SNMP Hosts {#snmp-hosts}
```bash
ppdm-cli snmp-hosts [subcommand] [flags]
```
**Description**: Manage SNMP hosts and monitoring configurations.

#### SNMP Service {#snmp-service}
```bash
ppdm-cli snmp-service [subcommand] [flags]
```
**Description**: Manage SNMP service configuration and settings.

### 🔄 Migration and Upgrade Commands

#### Migration {#migration}
```bash
ppdm-cli migrate [subcommand] [flags]
```
**Description**: Migrate exported copy to another datastore.

#### Upgrade Management {#upgrade-management}
```bash
ppdm-cli upgrade [subcommand] [flags]
```
**Description**: Manage PPDM upgrades and version management.

### 🔍 Verification and Testing Commands

#### Cloud Storage Verification {#cloud-storage-verification}
```bash
ppdm-cli verify-cloud-storage [flags]
```
**Description**: Verify cloud storage profile connection and configuration.

#### Object Storage Verification {#object-storage-verification}
```bash
ppdm-cli verify-object-storage [flags]
```
**Description**: Verify object storage profile connection and access.

#### Direct API Access {#direct-api-access}
```bash
ppdm-cli curl [endpoint] [flags]
```
**Description**: Direct API access to any PPDM endpoint for testing and integration.

#### Shell Completion {#shell-completion}
```bash
ppdm-cli completion [shell] [flags]
```
**Description**: Generate completion script for specified shell (bash, zsh, fish).

### ⚙️ Configuration and Settings Commands

#### Common Settings {#common-settings}
```bash
ppdm-cli common-settings [subcommand] [flags]
```
**Description**: Manage PPDM common settings and system configuration.

#### Configurations {#configurations}
```bash
ppdm-cli configurations [subcommand] [flags]
```
**Description**: Manage PPDM appliance configurations and settings.

#### GHVDM Host Configuration {#ghvdm-host-configuration}
```bash
ppdm-cli ghvdm-host-configuration-batch [flags]
```
**Description**: Manage GHVDM host configuration batch operations.
```bash
ppdm-cli assets list [flags]
```

**Description**: List all assets with filtering and pagination options.

**Examples**:
```bash
# List all assets
ppdm-cli assets list

# Filter by type
ppdm-cli assets list --filter "type eq 'FILE_SYSTEM'"

# Sort by creation date
ppdm-cli assets list --orderby "createdAt desc"

# Export to JSON
ppdm-cli assets list --output json > assets.json

# Get raw API response
ppdm-cli assets list --output json-raw
```

**Output Formats**:
- `table` - Human-readable table (default)
- `json` - Formatted JSON
- `yaml` - YAML format
- `csv` - Comma-separated values
- `json-raw` - Raw API response bytes only

#### Show Asset
```bash
ppdm-cli assets show <id> [flags]
```

**Description**: Display detailed information about a specific asset.

**Arguments**:
- `id` - Asset ID (required)

**Examples**:
```bash
# Show asset details
ppdm-cli assets show "12345678-1234-1234-1234-123456789012"

# Show in JSON format
ppdm-cli assets show "asset-id" --output json

# Get raw API response
ppdm-cli assets show "asset-id" --output json-raw
```

### Protection Policies {#protection-policies}

#### List Protection Policies
```bash
ppdm-cli protection-policies list [flags]
```

**Description**: List all protection policies with filtering options.

**Examples**:
```bash
# List all policies
ppdm-cli protection-policies list

# Filter by name
ppdm-cli protection-policies list --filter "name co 'backup'"

# Sort by name
ppdm-cli protection-policies list --orderby "name asc"

# Export to CSV
ppdm-cli protection-policies list --output csv > policies.csv
```

#### Show Protection Policy
```bash
ppdm-cli protection-policies show <id> [flags]
```

**Description**: Display detailed information about a protection policy.

**Arguments**:
- `id` - Policy ID (required)

#### Create Protection Policy
```bash
ppdm-cli protection-policies create [flags]
```

**Description**: Create a new protection policy using the unified V3 API with support for multiple asset types.

**🎯 Unified Command Structure**:
The create command now supports multiple asset types through a unified interface:
```bash
ppdm-cli protection-policies create --type <asset_type> [flags]
```

**📋 Supported Asset Types**:
- `VMWARE_VIRTUAL_MACHINE` - VMware virtual machine protection
- `ORACLE_DATABASE` - Oracle database protection  
- `GENERIC_APPLICATION_ASSET` - Generic application protection
- `KUBERNETES` - Kubernetes cluster protection
- `MICROSOFT_EXCHANGE_DATABASE` - Microsoft Exchange Database protection
- `MICROSOFT_SQL_DATABASE` - Microsoft SQL Database protection
- `HYPERV_VIRTUAL_MACHINE` - Hyper-V virtual machine protection
- `NUTANIX_VIRTUAL_MACHINE` - Nutanix virtual machine protection
- `NATIVEEDGE_VIRTUAL_MACHINE` - Native Edge virtual machine protection
- `NAS_SHARE` - Network Attached Storage share protection

**🔧 Available Flags**:
- `--type` - Asset type (VMWARE_VIRTUAL_MACHINE, ORACLE_DATABASE, GENERIC_APPLICATION_ASSET, KUBERNETES, MICROSOFT_EXCHANGE_DATABASE, MICROSOFT_SQL_DATABASE, HYPERV_VIRTUAL_MACHINE, NUTANIX_VIRTUAL_MACHINE, NATIVEEDGE_VIRTUAL_MACHINE, NAS_SHARE) [Tab completion enabled]
- `--name` - Policy name (required)
- `--description` - Policy description (optional)
- `--schedule` - Schedule type (DAILY, WEEKLY, MONTHLY, HOURLY, MINUTELY)
- `--storage-container-id` - Storage container ID for backups
- `--retention` - Retention value (default 7)
- `--retention-units` - Retention units (DAY, WEEK, MONTH, YEAR, HOUR, MINUTE)
- `--enable-indexing` - Enable metadata indexing for backup
- `--enable-anomaly-detection` - Enable anomaly detection (VMware only)
- `--exchange-consistency-check` - Exchange consistency check (NONE, ALL, LOGS_ONLY, DATABASE_ONLY) (Exchange only)
- `--sql-promotion-type` - SQL promotion type (ALL, NONE, NONE_WITH_WARNINGS) (SQL only)
- `--sql-skip-simple-database` - Skip SIMPLE recovery model databases during LOG backups (SQL only)
- `--sql-skip-unprotectable-state` - Skip SQL databases in unprotectable state (SQL only)
- `--sql-auto-promote` - Auto-promote non-full backups to full backups (SQL only)
- `--sql-parallelism` - SQL parallel streams (default 0) (SQL only)
- `--nas-ads-file-backup` - Enable ADS File Backup for NAS shares (NAS only)
- `--nas-continue-on` - Continue on errors (ACL_ACCESS_DENIED, DATA_ACCESS_DENIED, FILENAME_LENGTH_LIMIT_REACHED) (NAS only)
- `--nas-debug-enabled` - Enable DEBUG mode for NAS logs (NAS only)
- `--nas-indexing-enabled` - Enable metadata indexing for NAS backup (NAS only)
- `--nas-skip-on` - Skip on errors (FILENAME_LENGTH_LIMIT_REACHED) (NAS only)
- `--nas-exclude-filter-ids` - NAS exclusion filter IDs (NAS only)
- `--multi-schedule` - Enable multiple schedules in primary objective
- `--from-file` - Create from JSON file
- `--debug` - Enable debug/trace mode for API calls
- `--dry-run` - Show API calls without executing

**🚀 Examples**:

#### VMware Virtual Machine
```bash
# Basic VMware policy
ppdm-cli protection-policies create --type VMWARE_VIRTUAL_MACHINE --name "VM Backup Policy" --schedule DAILY --storage-container-id "container-123"

# VMware with multi-schedule (multiple backup operations)
ppdm-cli protection-policies create --type VMWARE_VIRTUAL_MACHINE --name "VM Multi Schedule" --schedule DAILY --multi-schedule --storage-container-id "container-123"

# VMware with all options
ppdm-cli protection-policies create \
  --name "Complete VM Backup Policy" \
  --type VMWARE_VIRTUAL_MACHINE \
  --schedule DAILY \
  --retention 30 \
  --retention-units DAYS \
  --storage-container-id "container-123" \
  --enable-anomaly-detection \
  --enable-indexing \
  --description "Comprehensive VM backup with all features"
```

#### Microsoft Exchange Database
```bash
# Basic Exchange policy
ppdm-cli protection-policies create --type MICROSOFT_EXCHANGE_DATABASE --name "Exchange Backup" --schedule DAILY --storage-container-id "container-123"

# Exchange with consistency check
ppdm-cli protection-policies create --type MICROSOFT_EXCHANGE_DATABASE --name "Exchange Full Check" --schedule DAILY --exchange-consistency-check ALL --storage-container-id "container-123"

# Exchange with multi-schedule (3 different backup operations)
ppdm-cli protection-policies create \
  --type MICROSOFT_EXCHANGE_DATABASE \
  --name "Exchange Multi Schedule" \
  --schedule DAILY \
  --multi-schedule \
  --exchange-consistency-check ALL \
  --storage-container-id "container-123" \
  --description "Exchange with daily, weekly, and monthly backups"

# High-frequency Exchange policy
ppdm-cli protection-policies create \
  --type MICROSOFT_EXCHANGE_DATABASE \
  --name "Exchange Frequent" \
  --schedule HOURLY \
  --exchange-consistency-check LOGS_ONLY \
  --retention 24 \
  --retention-units HOURS \
  --storage-container-id "container-123"
```

#### Hyper-V Virtual Machine
```bash
# Basic Hyper-V policy
ppdm-cli protection-policies create --type HYPERV_VIRTUAL_MACHINE --name "HyperV Backup Policy" --schedule DAILY --storage-container-id "container-123"

# Hyper-V with multi-schedule
ppdm-cli protection-policies create --type HYPERV_VIRTUAL_MACHINE --name "HyperV Multi" --schedule DAILY --multi-schedule --storage-container-id "container-123"
```

#### Nutanix Virtual Machine
```bash
# Basic Nutanix policy
ppdm-cli protection-policies create --type NUTANIX_VIRTUAL_MACHINE --name "Nutanix Backup Policy" --schedule DAILY --storage-container-id "container-123"

# Nutanix with multi-schedule
ppdm-cli protection-policies create --type NUTANIX_VIRTUAL_MACHINE --name "Nutanix Multi" --schedule DAILY --multi-schedule --storage-container-id "container-123"
```

#### Native Edge Virtual Machine
```bash
# Basic Native Edge policy
ppdm-cli protection-policies create --type NATIVEEDGE_VIRTUAL_MACHINE --name "Native Edge Backup" --schedule DAILY --storage-container-id "container-123"

# Native Edge with multi-schedule
ppdm-cli protection-policies create --type NATIVEEDGE_VIRTUAL_MACHINE --name "Native Edge Multi" --schedule DAILY --multi-schedule --storage-container-id "container-123"
```

#### Kubernetes Cluster
```bash
# Basic Kubernetes policy
ppdm-cli protection-policies create --type KUBERNETES --name "K8s Backup Policy" --schedule DAILY --storage-container-id "container-123"

# With high-frequency schedule
ppdm-cli protection-policies create --type KUBERNETES --name "K8s Frequent Backup" --schedule HOURLY --retention 24 --retention-units HOURS --storage-container-id "container-123"

# Production Kubernetes policy
ppdm-cli protection-policies create --type KUBERNETES --name "Production K8s" --schedule DAILY --retention 30 --retention-units DAYS --storage-container-id "prod-container" --description "Production Kubernetes cluster backup"
```

#### Microsoft SQL Database
```bash
# Basic SQL policy
ppdm-cli protection-policies create --type MICROSOFT_SQL_DATABASE --name "SQL Backup Policy" --schedule DAILY --storage-container-id "container-123"

# SQL with promotion type and parallelism
ppdm-cli protection-policies create --type MICROSOFT_SQL_DATABASE --name "SQL Advanced" --schedule DAILY --sql-promotion-type ALL --sql-parallelism 4 --storage-container-id "container-123"

# SQL with all options
ppdm-cli protection-policies create \
  --type MICROSOFT_SQL_DATABASE \
  --name "Complete SQL Backup Policy" \
  --schedule DAILY \
  --sql-promotion-type "NONE_WITH_WARNINGS" \
  --sql-skip-simple-database \
  --sql-skip-unprotectable-state \
  --sql-auto-promote \
  --sql-parallelism 8 \
  --storage-container-id "container-123" \
  --description "Comprehensive SQL backup with all options"

# SQL multi-schedule (creates 3 operations):
# 1. Daily FULL backup
# 2. Hourly LOG backup
# 3. Weekly DIFFERENTIAL backup
ppdm-cli protection-policies create \
  --type MICROSOFT_SQL_DATABASE \
  --name "SQL Multi Schedule" \
  --schedule DAILY \
  --multi-schedule \
  --sql-promotion-type ALL \
  --sql-parallelism 4 \
  --storage-container-id "container-123" \
  --description "SQL with daily, hourly, and weekly backups"

# High-frequency SQL policy for critical databases
ppdm-cli protection-policies create \
  --type MICROSOFT_SQL_DATABASE \
  --name "SQL Critical" \
  --schedule HOURLY \
  --sql-promotion-type ALL \
  --sql-skip-simple-database \
  --sql-parallelism 8 \
  --retention 72 \
  --retention-units HOURS \
  --storage-container-id "container-123"
```

#### NAS Share
```bash
# Basic NAS policy
ppdm-cli protection-policies create --type NAS_SHARE --name "NAS Backup Policy" --schedule DAILY --storage-container-id "container-123"

# NAS with ADS file backup and indexing
ppdm-cli protection-policies create --type NAS_SHARE --name "NAS Advanced" --schedule DAILY --nas-ads-file-backup --nas-indexing-enabled --storage-container-id "container-123"

# NAS with error handling options
ppdm-cli protection-policies create \
  --type NAS_SHARE \
  --name "NAS with Error Handling" \
  --schedule DAILY \
  --nas-continue-on ACL_ACCESS_DENIED,DATA_ACCESS_DENIED \
  --nas-skip-on FILENAME_LENGTH_LIMIT_REACHED \
  --nas-debug-enabled \
  --storage-container-id "container-123"

# NAS with all options
ppdm-cli protection-policies create \
  --type NAS_SHARE \
  --name "Complete NAS Backup Policy" \
  --schedule DAILY \
  --nas-ads-file-backup \
  --nas-continue-on ACL_ACCESS_DENIED,DATA_ACCESS_DENIED,FILENAME_LENGTH_LIMIT_REACHED \
  --nas-indexing-enabled \
  --nas-debug-enabled \
  --nas-exclude-filter-ids "filter-123","filter-456" \
  --storage-container-id "container-123" \
  --description "Comprehensive NAS backup with all options"

# NAS multi-schedule (creates 3 operations):
# 1. Daily FULL backup
# 2. Hourly INCREMENTAL backup
# 3. Weekly DIFFERENTIAL backup
ppdm-cli protection-policies create \
  --type NAS_SHARE \
  --name "NAS Multi Schedule" \
  --schedule DAILY \
  --multi-schedule \
  --nas-ads-file-backup \
  --nas-indexing-enabled \
  --nas-continue-on ACL_ACCESS_DENIED \
  --storage-container-id "container-123" \
  --description "NAS with multiple backup schedules"

# High-frequency NAS policy for critical file shares
ppdm-cli protection-policies create \
  --type NAS_SHARE \
  --name "NAS Critical Shares" \
  --schedule HOURLY \
  --nas-ads-file-backup \
  --nas-indexing-enabled \
  --nas-debug-enabled \
  --retention 48 \
  --retention-units HOURS \
  --storage-container-id "container-123"
```

#### Multi-Schedule Policies (NEW!)
```bash
# ⚠️ IMPORTANT: Retention period must be >= backup frequency to avoid data loss!
# Multi-schedule creates multiple backup operations in a single policy
# Each operation can have different schedules and backup levels

# ✅ SAFE: Exchange multi-schedule with adequate retention (30 days)
# Creates 3 operations:
# 1. Daily FULL backup
# 2. Weekly FULL backup  
# 3. Monthly SYNTHETIC_FULL backup
ppdm-cli protection-policies create \
  --type MICROSOFT_EXCHANGE_DATABASE \
  --name "Exchange Multi Schedule" \
  --schedule DAILY \
  --multi-schedule \
  --exchange-consistency-check ALL \
  --retention 30 \
  --retention-units DAYS \
  --storage-container-id "container-123" \
  --description "Exchange with daily, weekly, and monthly backups"

# ✅ SAFE: VMware multi-schedule with adequate retention (30 days)
# Creates 3 operations:
# 1. Daily FULL backup
# 2. Hourly INCREMENTAL backup
# 3. Weekly DIFFERENTIAL backup
ppdm-cli protection-policies create \
  --type VMWARE_VIRTUAL_MACHINE \
  --name "VM Multi Schedule" \
  --schedule DAILY \
  --multi-schedule \
  --enable-anomaly-detection \
  --retention 30 \
  --retention-units DAYS \
  --storage-container-id "container-123" \
  --description "VM with multiple backup schedules"

# ⚠️ DANGER: Risky configuration - inadequate retention for hourly backups!
# This creates HOURLY INCREMENTAL backups but only keeps them for 6 hours
# Data loss risk: Backups may be deleted before next backup cycle completes
ppdm-cli protection-policies create \
  --type VMWARE_VIRTUAL_MACHINE \
  --name "VM Risky Multi" \
  --schedule DAILY \
  --multi-schedule \
  --retention 6 \
  --retention-units HOURS \
  --storage-container-id "container-123" \
  --description "⚠️ RISKY: Hourly backups with 6-hour retention - DATA LOSS RISK!"

# Test multi-schedule with dry-run (recommended before production)
ppdm-cli protection-policies create \
  --type MICROSOFT_EXCHANGE_DATABASE \
  --name "Test Multi Schedule" \
  --schedule DAILY \
  --multi-schedule \
  --retention 30 \
  --retention-units DAYS \
  --dry-run \
  --debug
```

#### Oracle Database
```bash
# Basic Oracle policy
ppdm-cli protection-policies create --type ORACLE_DATABASE --name "Oracle Backup" --schedule DAILY --storage-container-id "container-123"

# Oracle with RMAN and catalog sync
ppdm-cli protection-policies create --type ORACLE_DATABASE --name "Oracle RMAN" --schedule DAILY --sync-catalog SYNC --parallelism 4 --storage-container-id "container-123"
```

#### Generic Application Asset
```bash
# Basic application policy
ppdm-cli protection-policies create --type GENERIC_APPLICATION_ASSET --name "App Backup" --schedule DAILY --storage-container-id "container-123"

# Application with all features
ppdm-cli protection-policies create \
  --type GENERIC_APPLICATION_ASSET \
  --name "Complete App Backup" \
  --schedule DAILY \
  --enable-anomaly-detection \
  --enable-indexing \
  --skip-unprotectable-state \
  --storage-container-id "container-123"
```

#### Generic Application Asset
```bash
# Basic Generic Application Asset policy
ppdm-cli protection-policies create --type GENERIC_APPLICATION_ASSET --name "App Backup Policy" --schedule DAILY --storage-container-id "container-123"

# With debug mode and description
ppdm-cli protection-policies create --type GENERIC_APPLICATION_ASSET --name "Production App Policy" --description "Critical application backup" --schedule DAILY --enable-indexing --debug

# Dry run to test configuration
ppdm-cli protection-policies create --type GENERIC_APPLICATION_ASSET --name "Test Policy" --schedule DAILY --storage-container-id "container-123" --dry-run
```

#### VMware Virtual Machine
```bash
# Basic VMware policy
ppdm-cli protection-policies create --type VMWARE_VIRTUAL_MACHINE --name "VM Backup Policy" --schedule DAILY --storage-container-id "container-123"

# With anomaly detection
ppdm-cli protection-policies create --type VMWARE_VIRTUAL_MACHINE --name "Security VM Policy" --schedule WEEKLY --enable-anomaly-detection --enable-indexing
```

#### Oracle Database
```bash
# Basic Oracle policy
ppdm-cli protection-policies create --type ORACLE_DATABASE --name "Oracle Backup Policy" --schedule DAILY --storage-container-id "container-123"

# With custom settings
ppdm-cli protection-policies create --type ORACLE_DATABASE --name "Production Oracle Policy" --schedule DAILY --parallelism 4 --sync-catalog SYNC
```

**🔍 Tab Completion**:
The `--type` flag supports tab completion for asset types:
```bash
ppdm-cli protection-policies create --type [TAB]
# Shows: VMWARE_VIRTUAL_MACHINE, ORACLE_DATABASE, GENERIC_APPLICATION_ASSET, KUBERNETES
```

**⚠️ API Schema Compliance Notes**:

#### Generic Application Asset Schema Requirements:
- **Connection Details**: Must use `ProtectionAddress` structure with `type` (FQDN/IPV4/IPV6) and `value` fields
- **Options Placement**: `options` must be at the same level as `config`, not inside `config`
- **Connection Types**: Valid types are OS, VCENTER, DBUSER, DB_WALLET, RMAN, RMAN_WALLET, DB, SAPHANA_DB_USER, SAPHANA_SYSTEMDB_USER, NAS

#### Kubernetes Schema Requirements:
- **Data Consistency**: Only `CRASH_CONSISTENT` is supported (Kubernetes limitation)
- **Simple Config**: Only `dataConsistency` and `forceFull` fields required
- **No Options**: Kubernetes policies don't have separate options object
- **No Connection Details**: No credential management needed for Kubernetes
- **Asset Type**: Must be exactly `KUBERNETES` (case-sensitive)

**Example Kubernetes Structure**:
```json
{
  "objectives": [{
    "config": {
      "dataConsistency": "CRASH_CONSISTENT",
      "forceFull": false
    }
  }]
}
```

**🐛 Debug Mode**:
Use `--debug` flag to see full API requests and responses:
```bash
ppdm-cli protection-policies create --type GENERIC_APPLICATION_ASSET --name "Debug Policy" --schedule DAILY --debug
```

**✅ Success Indicators**:
- HTTP 201 Created status
- Policy ID returned in response
- Policy appears in PPDM console with correct asset type

**📖 Legacy Examples**:
```bash
# Create from file (still supported)
ppdm-cli protection-policies create --from-file policy.json

# Create inline JSON (still supported)  
ppdm-cli protection-policies create --name "Test Policy" --json '{"type":"FILE_SYSTEM"}'
```

#### Update Protection Policy
```bash
ppdm-cli protection-policies update <id> [flags]
```

**Description**: Update an existing protection policy.

**Arguments**:
- `id` - Policy ID (required)

**Flags**:
- `--description` - Policy description
- `--from-file` - Update from JSON file
- `--json` - Update from inline JSON

#### Delete Protection Policy
```bash
ppdm-cli protection-policies delete <ids> [flags]
```

**Description**: Delete one or more protection policies.

**Arguments**:
- `ids` - Comma-separated policy IDs (required)

**Flags**:
- `--confirm` - Confirm deletion without prompt

### Activities Monitoring {#activities-monitoring}

#### List Activities
```bash
ppdm-cli activities list [flags]
```

**Description**: List activities with filtering and monitoring options.

**Examples**:
```bash
# List all activities
ppdm-cli activities list

# Filter by category
ppdm-cli activities list --filter "category eq 'PROTECT'"

# Filter by status
ppdm-cli activities list --filter "status eq 'RUNNING'"

# Monitor recent activities
ppdm-cli activities list --filter "createdAt ge '2026-03-16T00:00:00Z'" --orderby "createdAt desc"
```

#### Show Activity
```bash
ppdm-cli activities show <id> [flags]
```

**Description**: Display detailed information about a specific activity.

**Arguments**:
- `id` - Activity ID (required)

### Storage Systems {#storage-systems}

#### List Storage Systems
```bash
ppdm-cli storage-systems list [flags]
```

**Description**: List storage systems with filtering options.

**Flags**:
- `--supported-asset-type` - Filter by supported asset type
- `--type` - Filter by storage system type

**Examples**:
```bash
# List all storage systems
ppdm-cli storage-systems list

# Filter by type
ppdm-cli storage-systems list --type "DATA_DOMAIN_SYSTEM"

# Filter by supported asset type
ppdm-cli storage-systems list --supported-asset-type "FILE_SYSTEM"
```

#### Show Storage System
```bash
ppdm-cli storage-systems show <id> [flags]
```

**Description**: Display detailed information about a storage system.

**Arguments**:
- `id` - Storage system ID (required)

#### Update Storage System
```bash
ppdm-cli storage-systems update <id> [flags]
```

**Description**: Update storage system configuration.

**Arguments**:
- `id` - Storage system ID (required)

**Flags**:
- `--description` - Storage system description
- `--from-file` - Update from JSON file
- `--json` - Update from inline JSON

### Nodes Management {#nodes-management}

#### List Nodes
```bash
ppdm-cli nodes list [flags]
```

**Description**: List all nodes in the PPDM cluster.

**Examples**:
```bash
# List all nodes
ppdm-cli nodes list

# Filter by status
ppdm-cli nodes list --filter "status eq 'HEALTHY'"

# Show node details
ppdm-cli nodes show "node-id"
```

#### Show Node
```bash
ppdm-cli nodes show <id> [flags]
```

**Description**: Display detailed information about a specific node.

**Arguments**:
- `id` - Node ID (required)

### Alerts Management {#alerts-management}

#### List Alerts
```bash
ppdm-cli alerts list [flags]
```

**Description**: List alerts with filtering options.

**Examples**:
```bash
# List all alerts
ppdm-cli alerts list

# Filter by severity
ppdm-cli alerts list --filter "severity eq 'CRITICAL'"

# Filter by status
ppdm-cli alerts list --filter "status eq 'ACTIVE'"
```

#### Show Alert
```bash
ppdm-cli alerts show <id> [flags]
```

**Description**: Display detailed information about a specific alert.

**Arguments**:
- `id` - Alert ID (required)

## 🔍 Filter Expressions {#filter-expressions}

### Supported Operators
| Operator | Description | Example |
|----------|-------------|---------|
| `eq` | Equals | `name eq 'test'` |
| `ne` | Not equals | `status ne 'DELETED'` |
| `co` | Contains | `name co 'backup'` |
| `sw` | Starts with | `name sw 'prod'` |
| `ew` | Ends with | `name ew '-system'` |
| `gt` | Greater than | `createdAt gt '2026-03-16T00:00:00Z'` |
| `ge` | Greater than or equal | `createdAt ge '2026-03-16T00:00:00Z'` |
| `lt` | Less than | `createdAt lt '2026-03-16T00:00:00Z'` |
| `le` | Less than or equal | `createdAt le '2026-03-16T00:00:00Z'` |
| `and` | Logical AND | `name co 'test' and status eq 'ACTIVE'` |
| `or` | Logical OR | `status eq 'ACTIVE' or status eq 'WARNING'` |

### Common Filter Examples
```bash
# Filter by name
--filter "name eq 'Production-Filesystem'"

# Filter by type
--filter "type eq 'FILE_SYSTEM'"

# Filter by status
--filter "status eq 'PROTECTED'"

# Complex filter
--filter "name co 'prod' and status eq 'ACTIVE'"

# Date range filter
--filter "createdAt ge '2026-03-16T00:00:00Z' and createdAt le '2026-03-16T23:59:59Z'"

# Multiple conditions
--filter "(type eq 'FILE_SYSTEM' or type eq 'VMWARE_VIRTUAL_MACHINE') and status eq 'PROTECTED'"
```

## 📊 Output Formats {#output-formats}

### Table Format (Default)
```
ID                                    NAME              TYPE      STATUS
12345678-1234-1234-1234-123456789012  test-resource     FILE_SYSTEM  PROTECTED
87654321-4321-4321-4321-210987654321  prod-resource     FILE_SYSTEM  PROTECTED
```

### JSON Format
```json
{
  "content": [
    {
      "id": "12345678-1234-1234-1234-123456789012",
      "name": "test-resource",
      "type": "FILE_SYSTEM",
      "status": "PROTECTED",
      "createdAt": "2026-03-16T08:00:00Z",
      "updatedAt": "2026-03-16T08:00:00Z"
    }
  ],
  "page": {
    "totalElements": 1,
    "totalPages": 1,
    "size": 100,
    "number": 0
  }
}
```

### YAML Format
```yaml
content:
- id: 12345678-1234-1234-1234-123456789012
  name: test-resource
  type: FILE_SYSTEM
  status: PROTECTED
  createdAt: "2026-03-16T08:00:00Z"
  updatedAt: "2026-03-16T08:00:00Z"
page:
  totalElements: 1
  totalPages: 1
  size: 100
  number: 0
```

### CSV Format
```csv
id,name,type,status,createdAt,updatedAt
12345678-1234-1234-1234-123456789012,test-resource,FILE_SYSTEM,PROTECTED,2026-03-16T08:00:00Z,2026-03-16T00:00:00Z
```

### JSON-Raw Format
```json
{"content":[{"id":"12345678-1234-1234-1234-123456789012","name":"test-resource","type":"FILE_SYSTEM","status":"PROTECTED","createdAt":"2026-03-16T08:00:00Z","updatedAt":"2026-03-16T08:00:00Z"}],"page":{"totalElements":1,"totalPages":1,"size":100,"number":0}}
```

## 🚨 Error Handling {#error-handling}

### Common Error Codes
| HTTP Code | Meaning | Resolution |
|-----------|---------|------------|
| 400 | Bad Request | Check request parameters and syntax |
| 401 | Unauthorized | Verify authentication credentials |
| 403 | Forbidden | Check user permissions |
| 404 | Not Found | Verify resource ID exists |
| 409 | Conflict | Resource already exists or conflict |
| 422 | Unprocessable Entity | Invalid data format or validation error |
| 500 | Internal Server Error | Contact support |

### CLI Error Examples
```bash
# Authentication error
Error: failed to create client: authentication failed: invalid credentials

# Resource not found
Error: failed to get resource: resource not found: 12345678-1234-1234-1234-123456789012

# Connection error
Error: failed to create client: connection refused: dial tcp ppdm.example.com:8443
```

## � Multi-Schedule Functionality {#multi-schedule}

The `--multi-schedule` flag enables creation of policies with multiple backup operations in a single objective. This allows for different backup frequencies and levels within one policy.

### ⚠️ **IMPORTANT: Retention Period Warning**

**Setting a retention period lesser than the backup frequency may result in data loss in the schedule.**

#### **🚨 Critical Retention Guidelines:**

**Example Risk Scenario:**
- **Hourly INCREMENTAL backup** with **6-hour retention** → Data loss risk
- **Daily FULL backup** with **12-hour retention** → Data loss risk  
- **Weekly DIFFERENTIAL backup** with **3-day retention** → Data loss risk

#### **✅ Recommended Retention Practices:**

**For Multi-Schedule Policies:**
- **Hourly operations:** Minimum 24-hour retention (1 day)
- **Daily operations:** Minimum 7-day retention (1 week)
- **Weekly operations:** Minimum 4-week retention (1 month)
- **Monthly operations:** Minimum 12-month retention (1 year)

**Safe Retention Examples:**
```bash
# SAFE: Hourly backup with 24-hour retention
ppdm-cli protection-policies create \
  --type VMWARE_VIRTUAL_MACHINE \
  --name "VM Safe Multi" \
  --schedule DAILY \
  --multi-schedule \
  --retention 24 \
  --retention-units HOURS \
  --storage-container-id "container-123"

# SAFE: Daily backup with 30-day retention
ppdm-cli protection-policies create \
  --type MICROSOFT_EXCHANGE_DATABASE \
  --name "Exchange Safe Multi" \
  --schedule DAILY \
  --multi-schedule \
  --retention 30 \
  --retention-units DAYS \
  --storage-container-id "container-123"

# RISK: Hourly backup with only 6-hour retention - DATA LOSS RISK!
ppdm-cli protection-policies create \
  --type VMWARE_VIRTUAL_MACHINE \
  --name "VM Risky Multi" \
  --schedule DAILY \
  --multi-schedule \
  --retention 6 \
  --retention-units HOURS \
  --storage-container-id "container-123"  # ⚠️ DANGER: Data loss risk!
```

#### **🔍 How Retention Works with Multi-Schedule:**

**Unified Retention Policy:**
- All operations in a multi-schedule policy share the **same retention settings**
- The `--retention` and `--retention-units` flags apply to **ALL operations**
- Individual operations cannot have separate retention periods

**Risk Examples:**
```json
{
  "objectives": [{
    "operations": [
      {"backupLevel": "FULL", "schedule": {"type": "DAILY"}},      // Daily backup
      {"backupLevel": "INCREMENTAL", "schedule": {"type": "HOURLY"}}, // Hourly backup
      {"backupLevel": "DIFFERENTIAL", "schedule": {"type": "WEEKLY"}} // Weekly backup
    ],
    "retentions": [{
      "time": [{"unitValue": 6, "unitType": "HOUR"}] // ⚠️ RISK: Hourly backups deleted after 6 hours!
    }]
  }]
}
```

#### **🛡️ Safe Multi-Schedule Configurations:**

**Production-Ready Examples:**
```bash
# Enterprise Exchange Policy (SAFE)
ppdm-cli protection-policies create \
  --type MICROSOFT_EXCHANGE_DATABASE \
  --name "Enterprise Exchange" \
  --schedule DAILY \
  --multi-schedule \
  --exchange-consistency-check ALL \
  --retention 90 \
  --retention-units DAYS \
  --storage-container-id "prod-container"

# VM Policy with Adequate Retention (SAFE)
ppdm-cli protection-policies create \
  --type VMWARE_VIRTUAL_MACHINE \
  --name "Production VMs" \
  --schedule DAILY \
  --multi-schedule \
  --enable-anomaly-detection \
  --retention 30 \
  --retention-units DAYS \
  --storage-container-id "vm-container"
```

### 🎯 How Multi-Schedule Works
- **Single Objective:** Multiple operations are grouped under one BACKUP objective
- **Different Schedules:** Each operation can have its own schedule (DAILY, WEEKLY, MONTHLY, HOURLY)
- **Different Backup Levels:** Each operation can use different backup levels
- **Unified Retention:** All operations share the same retention policy

### 📋 Multi-Schedule Operations by Asset Type

#### Microsoft Exchange Database
- **Operation 1:** Daily FULL backup (DAILY schedule)
- **Operation 2:** Weekly FULL backup (WEEKLY schedule - SUNDAY)
- **Operation 3:** Monthly SYNTHETIC_FULL backup (MONTHLY schedule - 1st day)

#### VMware Virtual Machine
- **Operation 1:** Daily FULL backup (DAILY schedule)
- **Operation 2:** Hourly INCREMENTAL backup (HOURLY schedule)
- **Operation 3:** Weekly DIFFERENTIAL backup (WEEKLY schedule - SUNDAY)

#### Hyper-V Virtual Machine
- **Operation 1:** Daily FULL backup (DAILY schedule)
- **Operation 2:** Hourly INCREMENTAL backup (HOURLY schedule)
- **Operation 3:** Weekly DIFFERENTIAL backup (WEEKLY schedule - SUNDAY)

#### Nutanix Virtual Machine
- **Operation 1:** Daily FULL backup (DAILY schedule)
- **Operation 2:** Hourly INCREMENTAL backup (HOURLY schedule)
- **Operation 3:** Weekly DIFFERENTIAL backup (WEEKLY schedule - SUNDAY)

#### Native Edge Virtual Machine
- **Operation 1:** Daily FULL backup (DAILY schedule)
- **Operation 2:** Hourly INCREMENTAL backup (HOURLY schedule)
- **Operation 3:** Weekly DIFFERENTIAL backup (WEEKLY schedule - SUNDAY)

#### Microsoft SQL Database
- **Operation 1:** Daily FULL backup (DAILY schedule)
- **Operation 2:** Hourly LOG backup (HOURLY schedule)
- **Operation 3:** Weekly DIFFERENTIAL backup (WEEKLY schedule - SUNDAY)

#### NAS Share
- **Operation 1:** Daily FULL backup (DAILY schedule)
- **Operation 2:** Hourly INCREMENTAL backup (HOURLY schedule)
- **Operation 3:** Weekly DIFFERENTIAL backup (WEEKLY schedule - SUNDAY)

### 🔧 Multi-Schedule JSON Structure
```json
{
  "objectives": [{
    "type": "BACKUP",
    "operations": [
      {
        "backupLevel": "FULL",
        "schedule": {"recurrence": {"pattern": {"type": "DAILY"}}},
        "triggerType": "ON_SCHEDULE"
      },
      {
        "backupLevel": "FULL", 
        "schedule": {"recurrence": {"pattern": {"type": "WEEKLY"}}},
        "triggerType": "ON_SCHEDULE"
      },
      {
        "backupLevel": "SYNTHETIC_FULL",
        "schedule": {"recurrence": {"pattern": {"type": "MONTHLY"}}},
        "triggerType": "ON_SCHEDULE"
      }
    ]
  }]
}
```

### 🚀 Multi-Schedule Examples

#### Exchange Database Multi-Schedule
```bash
# Creates 3 operations with Exchange-specific settings
ppdm-cli protection-policies create \
  --type MICROSOFT_EXCHANGE_DATABASE \
  --name "Exchange Multi Schedule" \
  --schedule DAILY \
  --multi-schedule \
  --exchange-consistency-check ALL \
  --storage-container-id "container-123" \
  --description "Exchange with daily, weekly, and monthly backups"
```

#### VMware Multi-Schedule
```bash
# Creates 3 operations with VMware-specific options
ppdm-cli protection-policies create \
  --type VMWARE_VIRTUAL_MACHINE \
  --name "VM Multi Schedule" \
  --schedule DAILY \
  --multi-schedule \
  --enable-anomaly-detection \
  --storage-container-id "container-123" \
  --description "VM with multiple backup schedules"
```

#### Testing Multi-Schedule
```bash
# Use dry-run to preview the multi-schedule structure
ppdm-cli protection-policies create \
  --type MICROSOFT_EXCHANGE_DATABASE \
  --name "Test Multi Schedule" \
  --schedule DAILY \
  --multi-schedule \
  --dry-run \
  --debug
```

### 🎯 Multi-Schedule Benefits
- **Flexible Backup Strategy:** Different frequencies for different needs
- **Reduced Policy Management:** Multiple schedules in one policy
- **Storage Optimization:** Mix of full and incremental backups
- **Compliance Support:** Meet various retention requirements
- **Operational Efficiency:** Single policy for complex backup strategies

### 📊 Asset-Specific Options

#### 🔧 VMware Virtual Machine Options
- `--enable-anomaly-detection` - Enable anomaly detection
- `--enable-indexing` - Enable metadata indexing

#### 🔧 Microsoft Exchange Database Options
- `--exchange-consistency-check` - Consistency check level
  - `NONE` - No consistency check (default)
  - `ALL` - Check database and transaction log files
  - `LOGS_ONLY` - Check transaction log files only
  - `DATABASE_ONLY` - Check database files only

#### 🔧 Microsoft SQL Database Options
- `--sql-promotion-type` - SQL promotion type for LOG/DIFF backups
  - `ALL` - Promote ineligible databases to FULL backup (default)
  - `NONE` - Skip ineligible databases
  - `NONE_WITH_WARNINGS` - Skip ineligible databases with warnings
- `--sql-skip-simple-database` - Skip SIMPLE recovery model databases during LOG backups
- `--sql-skip-unprotectable-state` - Skip SQL databases in unprotectable state
- `--sql-auto-promote` - Auto-promote non-full backups to full backups
- `--sql-parallelism` - SQL parallel streams (default 0)

#### 🔧 NAS Share Options
- `--nas-ads-file-backup` - Enable ADS (Alternate Data Streams) File Backup
- `--nas-continue-on` - Continue backup on specific errors
  - `ACL_ACCESS_DENIED` - Continue when ACL access is denied
  - `DATA_ACCESS_DENIED` - Continue when data access is denied
  - `FILENAME_LENGTH_LIMIT_REACHED` - Continue when filename limit is reached
- `--nas-debug-enabled` - Enable DEBUG mode for NAS backup logs
- `--nas-indexing-enabled` - Enable metadata indexing for NAS backup files
- `--nas-skip-on` - Skip backup on specific errors
  - `FILENAME_LENGTH_LIMIT_REACHED` - Skip when filename limit is reached
- `--nas-exclude-filter-ids` - Comma-separated list of exclusion filter IDs

#### 🔧 Oracle Database Options
- `--sync-catalog` - Recovery catalog synchronization
  - `NONE` - No catalog sync (default)
  - `SYNC` - Synchronous catalog sync
  - `ASYNC` - Asynchronous catalog sync
- `--parallelism` - Parallel streams for backup (default 0)

#### 🔧 Generic Application Asset Options
- `--enable-anomaly-detection` - Enable anomaly detection
- `--enable-indexing` - Enable metadata indexing
- `--skip-unprotectable-state` - Skip unprotectable assets
- `--system-state-recovery-only` - System state recovery only

## 🧪 PPDM-Baremetal Testing {#ppdm-baremetal-testing}

### **🎯 MANDATORY TESTING METHODOLOGY**

**Always test new protection-policy create <type> commands with PPDM-Baremetal!** 

When creating a new protection policy create command with a new asset type, **ALWAYS test it with the PPDM-Baremetal server at `ppdm-baremetal.home.labbuildr.com`**. This ensures API compliance, validates JSON structure, and verifies functionality before production deployment.

### **🔧 Testing Workflow**

#### **✅ Step 1: Basic Asset Type Test**
```bash
# Test basic policy creation with dry-run and debug on PPDM-Baremetal
./ppdm-cli protection-policies create \
  --type [NEW_ASSET_TYPE] \
  --name "Test-[NEW_ASSET_TYPE]-Basic" \
  --schedule DAILY \
  --storage-container-id aa4930d2-01cf-423a-b1ff-c0f4aa499ae4 \
  --dry-run \
  --debug
```

#### **✅ Step 2: Asset-Specific Options Test**
```bash
# Test with all asset-specific flags on PPDM-Baremetal
./ppdm-cli protection-policies create \
  --type [NEW_ASSET_TYPE] \
  --name "Test-[NEW_ASSET_TYPE]-Advanced" \
  --schedule DAILY \
  --[ASSET_SPECIFIC_FLAGS] \
  --storage-container-id aa4930d2-01cf-423a-b1ff-c0f4aa499ae4 \
  --dry-run \
  --debug
```

#### **✅ Step 3: Multi-Schedule Test**
```bash
# Test multi-schedule functionality on PPDM-Baremetal
./ppdm-cli protection-policies create \
  --type [NEW_ASSET_TYPE] \
  --name "Test-[NEW_ASSET_TYPE]-Multi" \
  --schedule DAILY \
  --multi-schedule \
  --[ASSET_SPECIFIC_FLAGS] \
  --storage-container-id aa4930d2-01cf-423a-b1ff-c0f4aa499ae4 \
  --dry-run \
  --debug
```

#### **✅ Step 4: Production Configuration Test**
```bash
# Test production-ready configuration on PPDM-Baremetal
./ppdm-cli protection-policies create \
  --type [NEW_ASSET_TYPE] \
  --name "Test-[NEW_ASSET_TYPE]-Production" \
  --schedule DAILY \
  --retention 30 \
  --retention-units DAYS \
  --[ASSET_SPECIFIC_FLAGS] \
  --storage-container-id aa4930d2-01cf-423a-b1ff-c0f4aa499ae4 \
  --dry-run \
  --debug
```

### **🚀 Live Testing Examples with PPDM-Baremetal**

#### **✅ VMware Virtual Machine Test**
```bash
# Basic VMware test on PPDM-Baremetal
./ppdm-cli protection-policies create \
  --type VMWARE_VIRTUAL_MACHINE \
  --name "Test-VMware-Basic" \
  --schedule DAILY \
  --storage-container-id aa4930d2-01cf-423a-b1ff-c0f4aa499ae4 \
  --dry-run \
  --debug

# VMware with multi-schedule on PPDM-Baremetal
./ppdm-cli protection-policies create \
  --type VMWARE_VIRTUAL_MACHINE \
  --name "Test-VMware-Multi" \
  --schedule DAILY \
  --multi-schedule \
  --enable-anomaly-detection \
  --storage-container-id aa4930d2-01cf-423a-b1ff-c0f4aa499ae4 \
  --dry-run \
  --debug
```

#### **✅ Microsoft Exchange Database Test**
```bash
# Exchange with consistency check on PPDM-Baremetal
./ppdm-cli protection-policies create \
  --type MICROSOFT_EXCHANGE_DATABASE \
  --name "Test-Exchange-Advanced" \
  --schedule DAILY \
  --exchange-consistency-check ALL \
  --storage-container-id aa4930d2-01cf-423a-b1ff-c0f4aa499ae4 \
  --dry-run \
  --debug

# Exchange multi-schedule on PPDM-Baremetal
./ppdm-cli protection-policies create \
  --type MICROSOFT_EXCHANGE_DATABASE \
  --name "Test-Exchange-Multi" \
  --schedule DAILY \
  --multi-schedule \
  --exchange-consistency-check ALL \
  --storage-container-id aa4930d2-01cf-423a-b1ff-c0f4aa499ae4 \
  --dry-run \
  --debug
```

#### **✅ Microsoft SQL Database Test**
```bash
# SQL with promotion and parallelism on PPDM-Baremetal
./ppdm-cli protection-policies create \
  --type MICROSOFT_SQL_DATABASE \
  --name "Test-SQL-Advanced" \
  --schedule DAILY \
  --sql-promotion-type ALL \
  --sql-parallelism 4 \
  --storage-container-id aa4930d2-01cf-423a-b1ff-c0f4aa499ae4 \
  --dry-run \
  --debug

# SQL multi-schedule on PPDM-Baremetal
./ppdm-cli protection-policies create \
  --type MICROSOFT_SQL_DATABASE \
  --name "Test-SQL-Multi" \
  --schedule DAILY \
  --multi-schedule \
  --sql-promotion-type ALL \
  --sql-parallelism 8 \
  --storage-container-id aa4930d2-01cf-423a-b1ff-c0f4aa499ae4 \
  --dry-run \
  --debug
```

#### **✅ NAS Share Test**
```bash
# Basic NAS test on PPDM-Baremetal (current status - asset type recognized)
./ppdm-cli protection-policies create \
  --type NAS_SHARE \
  --name "Test-NAS-Basic" \
  --schedule DAILY \
  --storage-container-id aa4930d2-01cf-423a-b1ff-c0f4aa499ae4 \
  --dry-run \
  --debug

# NAS with advanced options on PPDM-Baremetal (when flags are fixed)
./ppdm-cli protection-policies create \
  --type NAS_SHARE \
  --name "Test-NAS-Advanced" \
  --schedule DAILY \
  --nas-ads-file-backup \
  --nas-indexing-enabled \
  --nas-continue-on ACL_ACCESS_DENIED,DATA_ACCESS_DENIED \
  --storage-container-id aa4930d2-01cf-423a-b1ff-c0f4aa499ae4 \
  --dry-run \
  --debug
```

### **📊 PPDM-Baremetal Test Validation Checklist**

#### **✅ PPDM-Baremetal Connection Validation:**
- [ ] **Server:** Connected to `ppdm-baremetal.home.labbuildr.com`
- [ ] **Authentication:** Successful admin authentication
- [ ] **API Version:** V3 API endpoints accessible
- [ ] **Storage Container:** `aa4930d2-01cf-423a-b1ff-c0f4aa499ae4` available

#### **✅ API Compliance Validation on PPDM-Baremetal:**
- [ ] **HTTP Method:** POST to `/api/v3/protection-policies`
- [ ] **Status Code:** 201 Created (dry-run shows 200)
- [ ] **JSON Structure:** Valid V3 API schema
- [ ] **Asset Type:** Correct discriminator value
- [ ] **Objectives:** Proper objective structure

#### **✅ Policy Structure Validation on PPDM-Baremetal:**
- [ ] **Asset Type:** Correct asset type identifier
- [ ] **Name:** Policy name properly set
- [ ] **Purpose:** Set to "CENTRALIZED"
- [ ] **Objectives Array:** Contains backup objectives
- [ ] **Operations:** Backup operations with schedules
- [ ] **Retentions:** Retention policies configured
- [ ] **Target:** Storage container properly set

#### **✅ Multi-Schedule Validation on PPDM-Baremetal:**
- [ ] **Operations Count:** Multiple operations created
- [ ] **Backup Levels:** Different levels per operation
- [ ] **Schedules:** Different schedule types
- [ ] **Trigger Types:** Set to "ON_SCHEDULE"
- [ ] **Action Window:** Proper window exceeded handling

#### **✅ Asset-Specific Validation on PPDM-Baremetal:**
- [ ] **Config Section:** Asset-specific configuration
- [ ] **Options Section:** Asset-specific options
- [ ] **Flags Applied:** All CLI flags reflected in JSON
- [ ] **Default Values:** Proper defaults when flags not set

### **🔍 PPDM-Baremetal Debug Analysis**

#### **✅ Key Debug Output:**
```bash
🔍 DEBUG: Starting manageProtectionPoliciesCreate
🔍 DEBUG: policyTypeV3 = 'NEW_ASSET_TYPE'
🔍 DEBUG: policyNameV3 = 'Test-NEW_ASSET_TYPE-Basic'
🔍 DEBUG: scheduleTypeV3 = 'DAILY'
🔍 DEBUG: Client created successfully
🔍 DEBUG: Authenticate - Request URL: https://ppdm-baremetal.home.labbuildr.com:8443/api/v2/login
🔍 DEBUG: Authentication successful
🔍 DEBUG: Using flag-based policy creation
🔍 DEBUG: Calling createPolicyFromFlags
🔍 DEBUG: assetType = 'NEW_ASSET_TYPE'
🔍 DEBUG: Building NEW_ASSET_TYPE policy
🔍 DEBUG: Policy created from flags successfully
```

#### **✅ PPDM-Baremetal API Call Inspection:**
```bash
🔍 DRY-RUN - API Call:
Method: POST
Endpoint: /api/v3/protection-policies
Body:
{
  "assetType": "NEW_ASSET_TYPE",
  "name": "Test-NEW_ASSET_TYPE-Basic",
  "objectives": [...]
}
```

### **⚠️ PPDM-Baremetal Troubleshooting Common Issues**

#### **🚨 Asset Type Not Recognized on PPDM-Baremetal:**
```bash
Error: unknown flag: --nas-ads-file-backup
```
**Solution:** Check if asset type is in completion list and flags are properly registered.

#### **🚨 Policy Falls Back to Default on PPDM-Baremetal:**
```bash
{
  "assetType": "NAS_SHARE",
  "name": "Test-NAS-Basic",
  "purpose": "CENTRALIZED"
}
```
**Solution:** Asset case not executed due to file corruption or missing implementation.

#### **🚨 Multi-Schedule Not Working on PPDM-Baremetal:**
```bash
🔍 DEBUG: Creating single-schedule operation
```
**Solution:** Check enableMultiScheduleV3 flag and multi-schedule logic.

#### **🚨 PPDM-Baremetal Connection Issues:**
```bash
Error: failed to create client: connection refused: dial tcp ppdm-baremetal.home.labbuildr.com:8443
```
**Solution:** Verify PPDM-Baremetal server is running and accessible.

### **🎯 PPDM-Baremetal Testing Best Practices**

#### **✅ Always Use PPDM-Baremetal for New Asset Types:**
- **Server:** `ppdm-baremetal.home.labbuildr.com`
- **Storage Container:** `aa4930d2-01cf-423a-b1ff-c0f4aa499ae4`
- **Authentication:** Admin credentials
- **API Version:** V3 endpoints

#### **✅ Always Use Dry-Run First on PPDM-Baremetal:**
- Prevents accidental policy creation
- Validates JSON structure
- Shows API call details
- Tests against real PPDM API

#### **✅ Always Use Debug Mode on PPDM-Baremetal:**
- Provides detailed execution flow
- Shows flag values
- Reveals implementation issues
- Displays PPDM-Baremetal connection details

#### **✅ Test Progressive Complexity on PPDM-Baremetal:**
1. Basic asset type recognition
2. Asset-specific options
3. Multi-schedule functionality
4. Production configurations

#### **✅ Validate Against PPDM-Baremetal API Schema:**
- Check discriminator values
- Verify required fields
- Ensure data type compliance
- Test with real PPDM server

### **🚀 PPDM-Baremetal Production Readiness Validation**

#### **✅ Final Production Test on PPDM-Baremetal:**
```bash
# Complete production test on PPDM-Baremetal
./ppdm-cli protection-policies create \
  --type [NEW_ASSET_TYPE] \
  --name "Production-Ready-[NEW_ASSET_TYPE]" \
  --schedule DAILY \
  --multi-schedule \
  --retention 30 \
  --retention-units DAYS \
  --[ALL_ASSET_SPECIFIC_FLAGS] \
  --storage-container-id aa4930d2-01cf-423a-b1ff-c0f4aa499ae4 \
  --dry-run \
  --debug
```

**✅ PPDM-Baremetal SUCCESS CRITERIA:**
- Asset type recognized and processed by PPDM-Baremetal
- All flags reflected in JSON output
- Multi-schedule creates multiple operations
- API schema compliance verified with PPDM-Baremetal
- No errors or warnings in debug output
- Successful connection to `ppdm-baremetal.home.labbuildr.com`

**🎉 READY FOR PRODUCTION ON PPDM-BAREMETAL!**

## 🔧 Advanced Usage {#advanced-usage}

### Multi-Host Operations
```bash
# Test different environments
ppdm-cli --host staging assets list
ppdm-cli --host production assets list

# Compare configurations
ppdm-cli --host staging nodes list --output json > staging-nodes.json
ppdm-cli --host production nodes list --output json > production-nodes.json
diff staging-nodes.json production-nodes.json
```

### Debug Mode
```bash
# Enable debug for API inspection
ppdm-cli --debug assets list

# Debug with specific host
ppdm-cli --host staging --debug activities show "activity-id"

# Debug with filter
ppdm-cli --debug assets list --filter "name co 'test'"
```

## 📚 Related Documentation

- [Shell Completion Guide](commands/COMPLETION_GUIDE.md) - Bash and Zsh tab completion setup
- [Deployment Guides](deployment) - Kubernetes and OpenShift deployment

---

**Version**: 20.1.0.0-12  
**Release Date**: March 16, 2026  
**API Versions**: PPDM v1.20.1.0.0, PPDM v2.20.1.0.0, PPDM v3.20.1.0.0
