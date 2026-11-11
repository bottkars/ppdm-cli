# Homebrew Deployment Guide

## Overview

This guide covers installing and managing PPDM CLI using Homebrew on macOS (Intel and Apple Silicon).

## Prerequisites

### Required
- **macOS**: 10.15 Catalina or later
- **Homebrew**: Package manager for macOS

### Install Homebrew
If you don't have Homebrew installed:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Verify installation:
```bash
brew --version
```

## Installation

### Step 1: Tap the Repository
```bash
brew tap bottkars/tap
```

This adds the personal tap repository to your Homebrew installation.

### Step 2: Install PPDM CLI
```bash
brew install --cask ppdm-cli
```

Homebrew will:
- Download the appropriate binary for your architecture (Intel or Apple Silicon)
- Verify the SHA-256 checksum
- Install to `/usr/local/bin/ppdm-cli` (or your Homebrew prefix)

### Step 3: Verify Installation
```bash
which ppdm-cli
ppdm-cli --version
ppdm-cli --help
```

Expected output:
```
/usr/local/bin/ppdm-cli
PPDM CLI Version: 20.1.0.0-13
```

## Platform Support

### macOS Intel (AMD64)
- Automatically detected on Intel-based Macs
- Downloads: ppdm-cli-darwin-amd64
- Installs: Intel-optimized binary

### macOS Apple Silicon (ARM64)
- Automatically detected on Apple Silicon Macs (M1/M2/M3)
- Downloads: ppdm-cli-darwin-arm64
- Installs: ARM64-optimized binary

Homebrew automatically selects the correct architecture for your system.

## Configuration

After installation, configure PPDM CLI:

```bash
# Set PPDM server
export PPDM_HOST=your-ppdm-server.example.com

# Set credentials
export PPDM_USERNAME=admin
export PPDM_PASSWORD=your-password

# Set port (optional, defaults to 8443)
export PPDM_PORT=8443

# Skip TLS verification (for testing only)
export PPDM_INSECURE_SKIP_VERIFY=true
```

Or use interactive configuration:
```bash
ppdm-cli credentials configure
```

## Usage

### Basic Commands
```bash
# Check system health
ppdm-cli health entities

# List assets
ppdm-cli assets list

# List activities
ppdm-cli activities list

# List alerts
ppdm-cli alerts list
```

### With Environment Variables
```bash
PPDM_HOST=ppdm.example.com \
PPDM_USERNAME=admin \
PPDM_PASSWORD=password \
ppdm-cli assets list
```

## Updates

### Check for Updates
```bash
brew outdated --cask
```

### Update to Latest Version
```bash
brew upgrade --cask ppdm-cli
```

This will:
- Download the latest version
- Verify checksums
- Replace the existing binary
- Preserve your configuration

### Force Reinstall
```bash
brew reinstall --cask ppdm-cli
```

## Uninstallation

### Uninstall PPDM CLI
```bash
brew uninstall --cask ppdm-cli
```

This will:
- Remove the binary
- Remove the cask from Homebrew
- **Note**: Your configuration in ~/.ppdm will be preserved

### Remove Tap Repository
```bash
brew untap bottkars/tap
```

### Clean Up Configuration
To remove all PPDM CLI configuration:
```bash
# Remove configuration directory
rm -rf ~/.ppdm

# Remove any environment variables from your shell profile
# Edit ~/.zshrc, ~/.bash_profile, or ~/.bashrc
# Remove lines containing PPDM_*
```

## Troubleshooting

### Issue: Command not found after installation
**Symptoms**: `ppdm-cli: command not found`

**Solutions**:
1. Ensure Homebrew is in your PATH:
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
```

2. Restart your terminal or run:
```bash
eval "$(/opt/homebrew/bin/brew shellenv)"
```

### Issue: Checksum verification failed
**Symptoms**: Installation fails with checksum error

**Solutions**:
1. Force reinstall:
```bash
brew reinstall --cask ppdm-cli
```

2. If issue persists, report via GitHub issues

### Issue: Wrong architecture installed
**Symptoms**: Binary doesn't run or has performance issues

**Solutions**:
1. Verify your architecture:
```bash
uname -m
# x86_64 = Intel
# arm64 = Apple Silicon
```

2. Reinstall:
```bash
brew reinstall --cask ppdm-cli
```

### Issue: Can't tap repository
**Symptoms**: `brew tap bottkars/tap` fails

**Solutions**:
1. Check internet connection
2. Verify repository exists: https://github.com/bottkars/homebrew-tap
3. Try manual tap:
```bash
brew tap https://github.com/bottkars/homebrew-tap
```

### Issue: Configuration not persisting
**Symptoms**: Credentials need to be re-entered

**Solutions**:
1. Use configuration file instead of environment variables
2. Run:
```bash
ppdm-cli credentials configure
```

## Advanced Usage

### Install Specific Version
```bash
# Find available versions
brew info ppdm-cli

# Install specific version (if available as separate cask)
brew install --cask ppdm-cli@version
```

### Use with Multiple PPDM Servers
PPDM CLI supports multiple server configurations:

```bash
# Configure multiple hosts
ppdm-cli credentials add --name production --host ppdm-prod.example.com
ppdm-cli credentials add --name staging --host ppdm-staging.example.com

# Switch between hosts
ppdm-cli credentials use production
ppdm-cli assets list

# Use specific host for one command
ppdm-cli --host ppdm-staging.example.com assets list
```

### Integration with Shell Profiles

Add to your shell profile (~/.zshrc or ~/.bash_profile):
```bash
# PPDM CLI Configuration
export PPDM_HOST=your-ppdm-server.example.com
export PPDM_USERNAME=admin
# Note: Don't hardcode passwords in shell profiles
```

Then source the profile:
```bash
source ~/.zshrc
```

## Comparison with Other Installation Methods

| Method | Command | Platform | Updates |
|--------|---------|----------|---------|
| **Homebrew** | `brew install --cask ppdm-cli` | macOS only | Automatic |
| Manual Download | wget + chmod + mv | All platforms | Manual |
| Container | `podman run quay.io/delldps/ppdm-cli` | Linux only | Pull new image |
| ORAS | `oras pull quay.io/...` | All platforms | Manual |

## When to Use Homebrew

### Recommended
- You're on macOS
- You prefer automatic updates
- You want clean uninstall
- You're familiar with package managers
- You want standard installation location

### Not Recommended
- You're on Linux (use manual download or container)
- You're on Windows (use manual download)
- You need air-gapped installation (use manual download)
- You need maximum control over binary location

## Support

### Documentation
- Full documentation: https://github.com/bottkars/ppdm-cli
- Command reference: https://github.com/bottkars/ppdm-cli/blob/main/Documentation/COMMAND_REFERENCE.md

### Issues
Report issues at: https://github.com/bottkars/ppdm-cli/issues

### Homebrew Issues
For Homebrew-specific issues:
- Homebrew documentation: https://docs.brew.sh
- Homebrew issues: https://github.com/Homebrew/brew/issues

## Version Information

This guide covers PPDM CLI version 20.1.0.0-13 and later.

For version-specific information:
```bash
brew info ppdm-cli
ppdm-cli --version
```

## See Also

- [PPDM CLI README](https://github.com/bottkars/ppdm-cli)
- [Deployment Guide](DEPLOYMENT_README.md)
- [Quick Start Guide](../getting-started/CONFIG_QUICK_START.md)
- [Container Deployment](DOCKER_DEPLOYMENT.md)
