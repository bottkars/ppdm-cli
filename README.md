# PPDM CLI Documentation

Welcome to the comprehensive documentation for PPDM CLI - the professional command-line interface for Dell PowerProtect Data Manager.

## 📚 Documentation Sections

### [Command Reference](COMMAND_REFERENCE.md)
Complete reference for all PPDM CLI commands with examples, flags, and API operation mappings.

### [Installation Guide](#installation)
Step-by-step instructions for installing PPDM CLI on various platforms.

### [Configuration Guide](#configuration)
How to configure PPDM CLI for different environments and use cases.

### [Examples](#examples)
Practical examples and use cases for common PPDM operations.

## 🚀 Quick Start

### Installation

#### Binary Download
```bash
# Linux AMD64
wget https://github.com/bottkars/ppdm-cli/releases/latest/download/ppdm-cli-linux-amd64
chmod +x ppdm-cli-linux-amd64
sudo mv ppdm-cli-linux-amd64 /usr/local/bin/ppdm-cli

# Verify installation
ppdm-cli version
```

#### ORAS Registry
```bash
# Install ORAS
curl -LO https://github.com/oras-project/oras/releases/download/v0.16.0/oras_0.16.0_linux_amd64.tar.gz
tar -xzf oras_0.16.0_linux_amd64.tar.gz

# Download PPDM CLI
oras pull quay.io/delldps/ppdm-cli:latest-linux-amd64
```

#### Docker
```bash
docker run --rm -it quay.io/delldps/ppdm-cli:latest version
```

### Configuration

#### Environment Variables
```bash
export PPDM_SERVER=<PPDM_SERVER_IP>
export PPDM_USERNAME=<USERNAME>
export PPDM_PASSWORD=<PASSWORD>
export PPDM_PORT=8443
```

#### Config File
```bash
# Create configuration
ppdm-cli config add production --ppdm-server <PPDM_SERVER_IP> --username <USERNAME> --password <PASSWORD>

# Set as default
ppdm-cli config default production

# List configurations
ppdm-cli config list
```

## 🔧 Features

- ✅ **Complete PPDM API Coverage** - All 80+ API endpoints
- ✅ **Advanced Filtering** - OData queries with server-side processing
- ✅ **Multiple Output Formats** - Table, JSON, YAML, CSV
- ✅ **Web Monitoring** - Real-time activity dashboard
- ✅ **Multi-Platform** - Linux, macOS, Windows, FreeBSD
- ✅ **Production Ready** - Enterprise-grade logging and error handling

## 📋 Platform Support

| Platform | Architecture | Download |
|----------|-------------|----------|
| Linux | AMD64, ARM64 | `ppdm-cli-linux-*` |
| macOS | AMD64, ARM64 | `ppdm-cli-darwin-*` |
| Windows | AMD64 | `ppdm-cli-windows-amd64.exe` |
| FreeBSD | AMD64 | `ppdm-cli-freebsd-amd64` |

## 🔗 Links

- [GitHub Releases](https://github.com/bottkars/ppdm-cli/releases)
- [ORAS Registry](https://quay.io/repository/delldps/ppdm-cli)
- [Issues & Support](https://github.com/bottkars/ppdm-cli/issues)

## 📖 Command Categories

### [Backup & Restore](commands/BACKUP_RESTORE.md)
Backup and restore operations for assets and copies.

### [Activities](commands/ACTIVITIES.md)
Monitor and manage PPDM activities.

### [Assets](commands/ASSETS.md)
Asset management and discovery operations.

### [Protection Policies](commands/PROTECTION_POLICIES.md)
Create and manage protection policies.

### [Inventory Sources](commands/INVENTORY_SOURCES.md)
Manage inventory sources for asset discovery.

### [Storage](commands/STORAGE.md)
Storage system management.

### [Alerts](commands/ALERTS.md)
Alert management and monitoring.

### [Web Monitor](commands/WEB_MONITOR.md)
Real-time web-based monitoring interface.

---

For detailed command reference, see the [Command Reference](COMMAND_REFERENCE.md) document.
