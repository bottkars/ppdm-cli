# PPDM CLI Configuration Management Guide

## Overview

The PPDM CLI features enterprise-grade configuration management with security, multi-host support, and automatic migration. This guide covers all aspects of configuration management from basic setup to advanced security features.

## Table of Contents

- [Quick Start](#quick-start)
- [Configuration Commands](#configuration-commands)
- [Multi-Host Management](#multi-host-management)
- [Security Setup](#security-setup)
- [Automatic Migration](#automatic-migration)
- [Environment Variables](#environment-variables)
- [Container Deployment](#container-deployment)
- [Security Features](#security-features)
- [Troubleshooting](#troubleshooting)

## Quick Start

### Basic Configuration

```bash
# Add your first PPDM server
ppdm-cli config add production \
  --ppdm-server ppdm.mycorp.com \
  --port 8443 \
  --username admin \
  --password YourPassword123! \
  --insecure-skip-verify false \
  --timeout 30

# List configured hosts
ppdm-cli config list

# Use the CLI - it will automatically prompt for security setup if needed
ppdm-cli assets list
```

### Container Quick Start

```bash
# Using environment variables (no config file needed)
docker run -e PPDM_SERVER=ppdm.mycorp.com \
           -e PPDM_USERNAME=admin \
           -e PPDM_PASSWORD=YourPassword123! \
           -e PPDM_PORT=8443 \
           quay.io/delldps/ppdm-cli:latest assets list
```

## Configuration Commands

### Command Structure

```bash
ppdm-cli config <command> [options]
```

### Available Commands

| Command | Description | Example |
|---------|-------------|---------|
| `list` | List all configured hosts | `ppdm-cli config list` |
| `add <name>` | Add new PPDM server | `ppdm-cli config add prod --ppdm-server ppdm.com` |
| `remove <name>` | Remove host configuration | `ppdm-cli config remove prod` |
| `change <name>` | Update existing host | `ppdm-cli config change prod --port 9443` |
| `default <name>` | Set default host | `ppdm-cli config default prod` |
| `show <name>` | Show host details | `ppdm-cli config show prod` |

### Command Options

| Option | Description | Example |
|--------|-------------|---------|
| `--ppdm-server` | PPDM server address (required) | `--ppdm-server ppdm.mycorp.com` |
| `--port` | Server port (default: 8443) | `--port 9443` |
| `--username` | Username (default: admin) | `--username myuser` |
| `--password` | Password | `--password MyPass123!` |
| `--insecure-skip-verify` | Skip TLS verification (default: false) | `--insecure-skip-verify true` |
| `--timeout` | Connection timeout in seconds (default: 30) | `--timeout 60` |

## Multi-Host Management

### Adding Multiple Servers

```bash
# Production environment
ppdm-cli config add production \
  --ppdm-server ppdm-prod.mycorp.com \
  --port 8443 \
  --username admin \
  --password ProdPassword123! \
  --timeout 30

# Staging environment  
ppdm-cli config add staging \
  --ppdm-server ppdm-staging.mycorp.com \
  --port 8443 \
  --username admin \
  --password StagingPassword123! \
  --insecure-skip-verify true \
  --timeout 30

# Development environment
ppdm-cli config add development \
  --ppdm-server ppdm-dev.mycorp.com \
  --port 8443 \
  --username devadmin \
  --password DevPassword123! \
  --timeout 45
```

### Managing Hosts

```bash
# List all hosts
ppdm-cli config list
# Output:
# production (DEFAULT) - ppdm-prod.mycorp.com:8443 (admin)
# staging - ppdm-staging.mycorp.com:8443 (admin)
# development - ppdm-dev.mycorp.com:8443 (devadmin)

# Switch default host
ppdm-cli config default staging

# Update host configuration
ppdm-cli config change production --port 9443 --timeout 60

# Show host details
ppdm-cli config show production
# Output:
# Host: production
# Server: ppdm-prod.mycorp.com:9443
# Username: admin
# Insecure: false
# Timeout: 60s
# Created: 2026-03-19 20:36:22
# Updated: 2026-03-19 21:45:03

# Remove host
ppdm-cli config remove development
```

## Security Setup

### Automatic Security Detection

The CLI automatically detects when passwords are stored in plaintext and prompts for security setup:

```bash
ppdm-cli config list
# Output:
# 🔒 Security Required: Detected plaintext passwords in configuration
# Please set a master password to encrypt your configuration:
# Enter master password: ********
# Confirm master password: ********
# ✅ Security setup completed! All passwords are now encrypted.
# 
# production (DEFAULT) - ppdm-prod.mycorp.com:8443 (admin)
# staging - ppdm-staging.mycorp.com:8443 (admin)
```

### Master Password Requirements

- **Minimum Length**: 12 characters
- **Complexity**: Must contain at least 3 of:
  - Uppercase letters (A-Z)
  - Lowercase letters (a-z)
  - Digits (0-9)
  - Special characters (!@#$%^&*)
- **Common Passwords**: Weak/common passwords are rejected

### Session Management

```bash
# First command after security setup - requires master password
ppdm-cli config list
# Enter master password: ********

# Subsequent commands within 15 minutes - no password required
ppdm-cli assets list
ppdm-cli status

# After 15 minutes - password required again
ppdm-cli activities list
# Enter master password: ********
```

## Automatic Migration

### Migration Scenarios

The configuration system automatically migrates from legacy formats:

#### 1. Old Config Format
```json
{
  "default": "staging",
  "hosts": {
    "production": {
      "host": "ppdm-baremetal.home.labbuildr.com",  // Old field
      "port": 8443,
      "username": "admin",
      "password": "Ithaca2022!",                   // Plaintext
      "insecure": true,
      "timeout": 30
    }
  }
}
```

#### 2. Enhanced Format (After Migration)
```json
{
  "version": "2.0",
  "default": "production",
  "hosts": {
    "production": {
      "ppdm-server": "ppdm-baremetal.home.labbuildr.com",  // New field
      "port": 8443,
      "username": "admin",
      "password": "jvzH7ekKlbp+301zypQal6IRLVg5QLYa5bhZYrKP96vnbX01B+95",  // Encrypted
      "insecure": true,
      "timeout": 30,
      "created_at": "2026-03-19T20:36:22.124095+01:00",
      "updated_at": "2026-03-19T20:36:22.124095+01:00"
    }
  },
  "encryption": {
    "method": "aes256-gcm",
    "key_derivation": "pbkdf2",
    "salt": "jgDMBDiBE7nZuraSmxpEmIE5WmhuiPCb17s71VOf+Jc=",
    "iterations": 100000,
    "master_password": true,
    "session_timeout": 15
  },
  "metadata": {
    "created_at": "2026-03-19T20:36:22.124005+01:00",
    "updated_at": "2026-03-19T20:45:03.06123+01:00",
    "version": "2.0",
    "last_migrated": "2026-03-19T20:36:22+01:00",
    "migration_from": "old"
  }
}
```

### Migration Process

1. **Detection**: Automatically detects legacy format on load
2. **Conversion**: Migrates field names and encrypts passwords
3. **Backup**: Creates backup of original configuration
4. **Save**: Writes new enhanced format
5. **Tracking**: Records migration metadata

## Environment Variables

### Supported Variables

| Variable | Description | Default | Example |
|----------|-------------|---------|---------|
| `PPDM_SERVER` | PPDM server address | localhost | `ppdm.mycorp.com` |
| `PPDM_USERNAME` | Username | admin | `myuser` |
| `PPDM_PASSWORD` | Password | empty | `mypass123` |
| `PPDM_PORT` | Port | 8443 | `9443` |
| `PPDM_INSECURE_SKIP_VERIFY` | Skip TLS verification | false | `true` |
| `PPDM_CONFIG_FILE` | Custom config file path | `~/.ppdm/config.json` | `/app/config.json` |
| `PPDM_DEFAULT_HOST` | Default host name | first host | `production` |

### Usage Examples

```bash
# Override file configuration
export PPDM_SERVER=ppdm-container.mycorp.com
export PPDM_USERNAME=containeruser
export PPDM_PASSWORD=containerpass123
export PPDM_PORT=8443
export PPDM_INSECURE_SKIP_VERIFY=false

ppdm-cli assets list

# Custom config file location
export PPDM_CONFIG_FILE=/custom/path/config.json
ppdm-cli status

# Set default host via environment
export PPDM_DEFAULT_HOST=staging
ppdm-cli config list
```

### Precedence Order

1. **Environment Variables** (highest priority)
2. **Configuration File**
3. **Default Values** (lowest priority)

## Container Deployment

### Docker

```bash
# Basic container usage
docker run -e PPDM_SERVER=ppdm.mycorp.com \
           -e PPDM_USERNAME=admin \
           -e PPDM_PASSWORD=mypassword \
           -e PPDM_PORT=8443 \
           quay.io/delldps/ppdm-cli:latest assets list

# With persistent configuration
docker run -v ppdm-config:/home/ppdm/.ppdm \
           -e PPDM_SERVER=ppdm.mycorp.com \
           -e PPDM_USERNAME=admin \
           -e PPDM_PASSWORD=mypassword \
           quay.io/delldps/ppdm-cli:latest
```

### Docker Compose

```yaml
version: '3.8'
services:
  ppdm-cli:
    image: quay.io/delldps/ppdm-cli:latest
    environment:
      PPDM_SERVER: ppdm.mycorp.com
      PPDM_USERNAME: admin
      PPDM_PASSWORD: mypassword
      PPDM_PORT: 8443
      PPDM_INSECURE_SKIP_VERIFY: "false"
    volumes:
      - ppdm-config:/home/ppdm/.ppdm
    command: assets list
volumes:
  ppdm-config:
```

### Kubernetes

```yaml
apiVersion: v1
kind: ConfigMap
data:
  PPDM_HOST: "ppdm.mycorp.com"
  PPDM_USERNAME: "admin"
  PPDM_PASSWORD: "mypassword"
  PPDM_PORT: "8443"
---
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      containers:
      - name: ppdm-cli
        image: quay.io/delldps/ppdm-cli:latest
        envFrom:
        - configMapRef:
            name: ppdm-config
        volumeMounts:
        - name: config
          mountPath: /home/ppdm/.ppdm
      volumes:
      - name: config
        persistentVolumeClaim:
          claimName: ppdm-config
```

## Security Features

### Encryption Details

| Feature | Implementation |
|---------|----------------|
| **Algorithm** | AES-256-GCM (Galois/Counter Mode) |
| **Key Derivation** | PBKDF2 with 100,000 iterations |
| **Salt Length** | 32 bytes (random) |
| **Key Length** | 32 bytes (256 bits) |
| **Authentication** | GCM authentication tag |

### File Security

```bash
# Configuration file permissions
ls -la ~/.ppdm/config.json
# -rw------- 1 user group 1234 Mar 19 20:36 config.json

# Backup file permissions
ls -la ~/.ppdm/config.json.backup
# -rw------- 1 user group 1234 Mar 19 20:35 config.json.backup
```

### Session Management

- **Timeout**: 15 minutes of inactivity
- **Refresh**: Extended on each successful operation
- **Memory**: Master password not stored in memory after timeout
- **Security**: Session invalidated on master password change

## Troubleshooting

### Common Issues

#### Migration Issues

```bash
# Problem: Migration fails
# Solution: Check file permissions and disk space
ls -la ~/.ppdm/
df -h ~/.ppdm/

# Manual migration backup restore
cp ~/.ppdm/config.json.backup ~/.ppdm/config.json
```

#### Security Issues

```bash
# Problem: Master password not accepted
# Solution: Reset security (will lose encrypted passwords)
rm ~/.ppdm/config.json
ppdm-cli config add production --ppdm-server ppdm.com --username admin --password newpass
```

#### Environment Variable Issues

```bash
# Problem: Environment variables not working
# Solution: Check variable names and values
env | grep PPDM_

# Debug environment variable loading
export PPDM_DEBUG=true
ppdm-cli status
```

#### Permission Issues

```bash
# Problem: Config file permissions error
# Solution: Fix permissions
chmod 600 ~/.ppdm/config.json
chown $USER:$USER ~/.ppdm/config.json
```

### Debug Mode

```bash
# Enable debug logging
export PPDM_DEBUG=true
ppdm-cli config list

# Check configuration file location
ppdm-cli config list --debug
```

### Backup and Recovery

```bash
# List backup files
ls -la ~/.ppdm/config.json.backup*

# Restore from backup
cp ~/.ppdm/config.json.backup.20260319-203622 ~/.ppdm/config.json

# Export configuration (decrypted)
ppdm-cli config show production --export > production-config.json
```

## Advanced Configuration

### Configuration File Structure

The enhanced configuration file uses the following structure:

```json
{
  "version": "2.0",
  "default": "production",
  "hosts": {
    "host-name": {
      "ppdm-server": "server.address.com",
      "port": 8443,
      "username": "admin",
      "password": "encrypted-password",
      "insecure": false,
      "timeout": 30,
      "created_at": "2026-03-19T20:36:22.124095+01:00",
      "updated_at": "2026-03-19T20:36:22.124095+01:00"
    }
  },
  "encryption": {
    "method": "aes256-gcm",
    "key_derivation": "pbkdf2",
    "salt": "base64-encoded-salt",
    "iterations": 100000,
    "master_password": true,
    "session_timeout": 15
  },
  "metadata": {
    "created_at": "2026-03-19T20:36:22.124005+01:00",
    "updated_at": "2026-03-19T20:45:03.06123+01:00",
    "version": "2.0",
    "last_migrated": "2026-03-19T20:36:22+01:00",
    "migration_from": "old"
  }
}
```

### Validation Rules

- **Host Names**: 3-63 characters, alphanumeric + hyphens, no leading/trailing hyphens
- **Server Addresses**: Valid IPv4 or FQDN
- **Ports**: 1-65535
- **Timeouts**: 1-3600 seconds
- **Usernames**: 1-64 characters, no spaces
- **Passwords**: 1-128 characters

For more information, see the main [README.md](../README.md) file.
