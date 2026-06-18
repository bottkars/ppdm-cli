# PPDM CLI Shell Completion Guide

## Overview

This guide provides comprehensive instructions for setting up and using shell completion for PPDM CLI in Bash and Zsh environments.

## 🚀 Quick Setup

### Bash Completion
```bash
# Load completion in current session
source <(ppdm-cli completion bash)

# Add to ~/.bashrc for permanent setup
echo 'source <(ppdm-cli completion bash)' >> ~/.bashrc

# Reload shell
source ~/.bashrc
```

### Zsh Completion
```bash
# Load completion in current session
source <(ppdm-cli completion zsh)

# Add to ~/.zshrc for permanent setup
echo 'source <(ppdm-cli completion zsh)' >> ~/.zshrc

# Reload shell
source ~/.zshrc
```

## 📋 Installation Methods

### Method 1: Dynamic Loading (Recommended)

#### Bash
```bash
# Add to ~/.bashrc
cat >> ~/.bashrc << 'EOF'
# PPDM CLI completion
if command -v ppdm-cli &> /dev/null; then
    source <(ppdm-cli completion bash)
fi
EOF

# Reload shell
source ~/.bashrc
```

#### Zsh
```bash
# Add to ~/.zshrc
cat >> ~/.zshrc << 'EOF'
# PPDM CLI completion
if command -v ppdm-cli &> /dev/null; then
    source <(ppdm-cli completion zsh)
fi
EOF

# Reload shell
source ~/.zshrc
```

### Method 2: Static File Installation

#### Bash
```bash
# Generate completion file
ppdm-cli completion bash > ~/.local/share/bash-completion/completions/ppdm-cli

# Create directory if it doesn't exist
mkdir -p ~/.local/share/bash-completion/completions

# Alternative: System-wide installation
sudo ppdm-cli completion bash > /etc/bash_completion.d/ppdm-cli
```

#### Zsh
```bash
# Generate completion file
ppdm-cli completion zsh > ~/.zsh/completions/_ppdm-cli

# Create directory if it doesn't exist
mkdir -p ~/.zsh/completions

# Add to ~/.zshrc
echo 'fpath+=~/.zsh/completions' >> ~/.zshrc
echo 'autoload -U compinit && compinit' >> ~/.zshrc
```

## 🔧 Configuration

### Bash Configuration

#### Enable completion in ~/.bashrc
```bash
# Enable bash completion
if ! shopt -oq posix; then
  if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion.d/bash_completion ]; then
    . /etc/bash_completion.d/bash_completion
  fi
fi

# PPDM CLI completion
if command -v ppdm-cli &> /dev/null; then
    source <(ppdm-cli completion bash)
fi
```

#### Custom completion directory
```bash
# Add custom completion directory
export BASH_COMPLETION_USER_DIR=~/.local/share/bash-completion/completions

# Create directory
mkdir -p "$BASH_COMPLETION_USER_DIR"

# Generate completion
ppdm-cli completion bash > "$BASH_COMPLETION_USER_DIR/ppdm-cli"
```

### Zsh Configuration

#### Enable completion in ~/.zshrc
```bash
# Enable zsh completion
autoload -U compinit
compinit

# Add custom completion path
fpath+=~/.zsh/completions

# PPDM CLI completion
if command -v ppdm-cli &> /dev/null; then
    source <(ppdm-cli completion zsh)
fi
```

#### Completion options
```bash
# Add to ~/.zshrc for better completion experience
zstyle ':completion:*' menu select
zstyle ':completion:*' group-name ''
zstyle ':completion:*' list-colors ''
zstyle ':completion:*' matcher-list 'm:{a-zA-Z}={A-Za-z}'
zstyle ':completion:*' rehash true
```

## 🎯 Usage Examples

### Command Completion
```bash
# Complete main commands
ppdm-cli <TAB>
# assets       activities    alerts        nodes
# protection-policies  storage-systems

# Complete subcommands
ppdm-cli assets <TAB>
# list         show

# Complete flags
ppdm-cli assets list <TAB>
# --filter     --help        --output      --page
# --page-size  --orderby     --dry-run     --debug
```

### Flag Value Completion
```bash
# Output format completion
ppdm-cli assets list --output <TAB>
# table         json          yaml          csv           json-raw

# Filter completion (if implemented)
ppdm-cli assets list --filter "type <TAB>
# eq            ne            co            sw            ew
```

### Multi-Host Completion
```bash
# Host completion from config file
ppdm-cli --host <TAB>
# staging       production

# Environment variable completion
export PPDM_HOST=<TAB>
# ppdm-staging.example.com  ppdm-prod.example.com
```

### File Completion
```bash
# File completion for --from-file flag
ppdm-cli protection-policies create --from-file <TAB>
# policy.json    backup-policy.json  test-policy.json

# Directory completion
ppdm-cli --config-file <TAB>
# .ppdm/         config/        /etc/ppdm/
```

## 🔍 Advanced Features

### Context-Aware Completion

#### Asset Type Completion
```bash
# Complete asset types in filters
ppdm-cli assets list --filter "type <TAB>
# FILE_SYSTEM           VMWARE_VIRTUAL_MACHINE
# MICROSOFT_SQL_SERVER  ORACLE_DATABASE
# KUBERNETES_CLUSTER    NAS_SHARE
```

#### Activity Status Completion
```bash
# Complete activity statuses
ppdm-cli activities list --filter "status <TAB>
# RUNNING       COMPLETED     FAILED        CANCELLED
# PENDING       PAUSED
```

#### Alert Severity Completion
```bash
# Complete alert severities
ppdm-cli alerts list --filter "severity <TAB>
# CRITICAL      WARNING       INFO          DEBUG
```

### Dynamic Value Completion

#### Resource ID Completion
```bash
# Complete asset IDs
ppdm-cli assets show <TAB>
# 12345678-1234-1234-1234-123456789012
# 87654321-4321-4321-4321-210987654321

# Complete policy IDs
ppdm-cli protection-policies show <TAB>
# policy-123456  policy-789012  policy-345678
```

#### Host Configuration Completion
```bash
# Complete configured hosts
ppdm-cli --host <TAB>
# staging       production    development   test

# Complete environment variables
export PPDM_HOST=<TAB>
# ppdm-staging.example.com
# ppdm-prod.example.com
# ppdm-dev.example.com
```

## 🛠️ Troubleshooting

### Common Issues

#### Completion Not Working
```bash
# Check if ppdm-cli is in PATH
which ppdm-cli

# Test completion generation
ppdm-cli completion bash | head -5

# Check shell completion is enabled
echo $BASH_VERSION
echo $ZSH_VERSION
```

#### Zsh Completion Issues
```bash
# Check completion system
autoload -U compinit && compinit

# Rebuild completion cache
rm -f ~/.zcompdump*
compinit

# Check fpath
echo $fpath | tr ' ' '\n' | grep completion
```

#### Bash Completion Issues
```bash
# Check bash completion
complete -p | grep ppdm-cli

# Reload completion
source ~/.bashrc

# Check completion directory
ls /etc/bash_completion.d/ | grep ppdm
```

### Debug Mode

#### Bash Debug
```bash
# Enable bash completion debug
set -v
ppdm-cli <TAB>
set +v

# Check completion function
declare -f _ppdm-cli
```

#### Zsh Debug
```bash
# Enable zsh completion debug
zstyle ':completion:*' debug true
ppdm-cli <TAB>

# Check completion function
which _ppdm-cli
```

## 🔄 Updating Completion

### Dynamic Updates
```bash
# Reload completion (Bash)
source <(ppdm-cli completion bash)

# Reload completion (Zsh)
source <(ppdm-cli completion zsh)
```

### Static File Updates
```bash
# Update bash completion file
ppdm-cli completion bash > ~/.local/share/bash-completion/completions/ppdm-cli

# Update zsh completion file
ppdm-cli completion zsh > ~/.zsh/completions/_ppdm-cli

# Reload shell
source ~/.bashrc  # or ~/.zshrc
```

## 📚 Integration with Other Tools

### Oh My Zsh
```bash
# Add to ~/.zshrc (after oh-my-zsh setup)
if command -v ppdm-cli &> /dev/null; then
    source <(ppdm-cli completion zsh)
fi
```

### Prezto
```bash
# Add to ~/.zpreztorc
zstyle ':prezto:module:completion' auto 'true'

# Load completion
if command -v ppdm-cli &> /dev/null; then
    source <(ppdm-cli completion zsh)
fi
```

### Fish Shell (if supported)
```bash
# Fish completion (if implemented)
ppdm-cli completion fish > ~/.config/fish/completions/ppdm-cli.fish
```

## 🎯 Best Practices

### Performance Optimization
```bash
# Use lazy loading for better performance
_ppdm_cli_completion() {
    if [[ $1 == "ppdm-cli" ]]; then
        source <(ppdm-cli completion bash)
        _ppdm_cli_completion "$@"
    fi
}
complete -F _ppdm_cli_completion ppdm-cli
```

### Shell-Specific Optimizations

#### Bash
```bash
# Enable programmable completion
shopt -s progcomp

# Use complete -C for custom completion
complete -C _ppdm_cli_custom ppdm-cli
```

#### Zsh
```bash
# Use compdef for better integration
compdef _ppdm_cli ppdm-cli

# Enable menu selection
zstyle ':completion:*' menu select
```

### Configuration Management
```bash
# Create completion configuration file
cat > ~/.config/ppdm-cli/completion.conf << 'EOF'
# PPDM CLI completion configuration
COMPLETION_TIMEOUT=5
COMPLETION_CACHE=true
COMPLETION_DEBUG=false
EOF

# Source configuration in shell
if [[ -f ~/.config/ppdm-cli/completion.conf ]]; then
    source ~/.config/ppdm-cli/completion.conf
fi
```

## 📖 Examples and Workflows

### Daily Workflow
```bash
# 1. List assets with completion
ppdm-cli assets list --filter "type <TAB>
# Select FILE_SYSTEM
ppdm-cli assets list --filter "type eq 'FILE_SYSTEM'"

# 2. Show specific asset
ppdm-cli assets show <TAB>
# Select from available IDs
ppdm-cli assets show "12345678-1234-1234-1234-123456789012"

# 3. Create policy with file completion
ppdm-cli protection-policies create --from-file <TAB>
# Select policy file
ppdm-cli protection-policies create --from-file policy.json
```

### Multi-Host Workflow
```bash
# 1. Switch between hosts
ppdm-cli --host <TAB>
# Select staging
ppdm-cli --host staging assets list

# 2. Compare environments
ppdm-cli --host production assets list --output json > prod-assets.json
ppdm-cli --host staging assets list --output json > staging-assets.json

# 3. Use environment variables
export PPDM_HOST=<TAB>
# Select from configured hosts
```

### Automation Script with Completion
```bash
#!/bin/bash
# Script that uses PPDM CLI with completion support

# Enable completion in script
source <(ppdm-cli completion bash)

# Interactive asset selection
echo "Select an asset:"
select asset in $(ppdm-cli assets list --output json | jq -r '.content[].name'); do
    echo "Selected: $asset"
    ppdm-cli assets show "$(ppdm-cli assets list --filter "name eq '$asset'" --output json-raw | jq -r '.content[0].id')"
    break
done
```

## 🎯 Policy Creation Best Practices

### Unified Command Structure
The PPDM CLI now supports a unified policy creation command that works across all asset types:

```bash
ppdm-cli protection-policies create --type <asset_type> [flags]
```

### Asset Type Completion
```bash
# Tab completion for asset types
ppdm-cli protection-policies create --type [TAB]
# Options: VMWARE_VIRTUAL_MACHINE, ORACLE_DATABASE, GENERIC_APPLICATION_ASSET, KUBERNETES
```

### Policy Creation Workflow
```bash
#!/bin/bash
# Interactive policy creation with completion

echo "Creating new protection policy..."

# Step 1: Select asset type (with completion)
echo "Available asset types: VMWARE_VIRTUAL_MACHINE, ORACLE_DATABASE, GENERIC_APPLICATION_ASSET, KUBERNETES"
read -p "Asset type: " asset_type

# Step 2: Policy name (with validation)
read -p "Policy name: " policy_name
if [[ -z "$policy_name" ]]; then
    echo "Error: Policy name is required"
    exit 1
fi

# Step 3: Schedule type (with completion)
echo "Available schedules: DAILY, WEEKLY, MONTHLY, HOURLY, MINUTELY"
read -p "Schedule type: " schedule_type

# Step 4: Storage container (with completion)
echo "Available storage containers:"
ppdm-cli storage-containers list --output table
read -p "Storage container ID: " storage_id

# Step 5: Create policy with debug mode
echo "Creating policy..."
ppdm-cli protection-policies create \
    --type "$asset_type" \
    --name "$policy_name" \
    --schedule "$schedule_type" \
    --storage-container-id "$storage_id" \
    --debug

echo "Policy creation completed!"
```

### Debug Mode Usage
Always use debug mode when testing new policy configurations:

```bash
# Test with debug mode
ppdm-cli protection-policies create --type GENERIC_APPLICATION_ASSET --name "Test Policy" --schedule DAILY --debug

# Dry run to validate without creating
ppdm-cli protection-policies create --type GENERIC_APPLICATION_ASSET --name "Test Policy" --schedule DAILY --dry-run
```

### API Schema Compliance Learnings

#### Generic Application Asset Requirements:
1. **Connection Details Structure**:
   ```json
   "addresses": [{"type": "FQDN", "value": "localhost"}]
   ```
   NOT: `{"hostname": "localhost"}`

2. **Options Placement**:
   ```json
   {
     "config": {...},
     "options": {"debugEnabled": false}
   }
   ```
   NOT: `{"config": {"options": {...}}}`

3. **Valid Connection Types**: OS, VCENTER, DBUSER, DB_WALLET, RMAN, RMAN_WALLET, DB, SAPHANA_DB_USER, SAPHANA_SYSTEMDB_USER, NAS

#### Kubernetes Requirements:
1. **Data Consistency**: Only `CRASH_CONSISTENT` is supported (Kubernetes limitation)
2. **Simple Config**: Only `dataConsistency` and `forceFull` fields required
3. **No Options**: Kubernetes policies don't have separate options object
4. **No Connection Details**: No credential management needed for Kubernetes
5. **Asset Type**: Must be exactly `KUBERNETES` (case-sensitive)

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

### Common Troubleshooting Patterns

#### API Validation Errors
```bash
# Enable debug mode to see exact API request
ppdm-cli protection-policies create --type GENERIC_APPLICATION_ASSET --name "Debug Policy" --debug

# Look for these patterns in error messages:
# - "Object instance has properties which are not allowed by the schema"
# - "Instance value ('TYPE') not found in enum"
# - "missing ',' before newline in composite literal"
```

#### Success Indicators
```bash
# Look for these success indicators:
# - HTTP 201 Created status
# - Policy ID returned in response
# - "✅ Protection Policy Created:" message
```

### Policy Creation Templates

#### Generic Application Asset Template
```bash
# Basic template
ppdm-cli protection-policies create \
    --type GENERIC_APPLICATION_ASSET \
    --name "App-$(date +%Y%m%d)" \
    --schedule DAILY \
    --storage-container-id "your-container-id" \
    --enable-indexing

# Production template
ppdm-cli protection-policies create \
    --type GENERIC_APPLICATION_ASSET \
    --name "Production-App-Backup" \
    --description "Critical application backup policy" \
    --schedule DAILY \
    --retention 30 \
    --retention-units DAY \
    --storage-container-id "prod-container" \
    --enable-indexing
```

#### VMware Template
```bash
# Production VMware template
ppdm-cli protection-policies create \
    --type VMWARE_VIRTUAL_MACHINE \
    --name "VM-Production-Backup" \
    --schedule DAILY \
    --retention 14 \
    --storage-container-id "vm-container" \
    --enable-indexing \
    --enable-anomaly-detection
```

#### Kubernetes Template
```bash
# Basic Kubernetes template
ppdm-cli protection-policies create \
    --type KUBERNETES \
    --name "K8s-$(date +%Y%m%d)" \
    --schedule DAILY \
    --storage-container-id "your-container-id"

# Production Kubernetes template
ppdm-cli protection-policies create \
    --type KUBERNETES \
    --name "Production-K8s-Backup" \
    --description "Production Kubernetes cluster backup policy" \
    --schedule DAILY \
    --retention 30 \
    --retention-units DAY \
    --storage-container-id "k8s-container"

# High-frequency Kubernetes template
ppdm-cli protection-policies create \
    --type KUBERNETES \
    --name "K8s-High-Frequency" \
    --schedule HOURLY \
    --retention 24 \
    --retention-units HOUR \
    --storage-container-id "k8s-container" \
    --description "High-frequency Kubernetes backup"
```

#### Oracle Database Template
```bash
# Production Oracle template
ppdm-cli protection-policies create \
    --type ORACLE_DATABASE \
    --name "Oracle-Production-Backup" \
    --schedule DAILY \
    --parallelism 4 \
    --sync-catalog SYNC \
    --retention 30 \
    --storage-container-id "oracle-container"
```

## 🔧 Customization

### Custom Completion Functions
```bash
# Custom completion for asset names
_ppdm_cli_asset_names() {
    local cur=${COMP_WORDS[COMP_CWORD]}
    local assets=$(ppdm-cli assets list --output json-raw 2>/dev/null | jq -r '.content[].name' 2>/dev/null)
    COMPREPLY=($(compgen -W "$assets" -- "$cur"))
}

# Register custom completion
complete -F _ppdm_cli_asset_names ppdm-cli-show-asset
```

### Alias Completion
```bash
# Create aliases with completion
alias ppdm=ppdm-cli
complete -F _ppdm_cli ppdm

alias assets='ppdm-cli assets'
complete -F _ppdm_cli_assets assets

alias policies='ppdm-cli protection-policies'
complete -F _ppdm_cli_protection-policies policies
```

---

**Version**: 20.1.0.0-14  
**Last Updated**: March 16, 2026  
**Supported Shells**: Bash 4.0+, Zsh 5.0+  
**New Features**: Unified policy creation with Kubernetes support
