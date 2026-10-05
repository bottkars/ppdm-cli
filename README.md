# PPDM CLI - PowerProtect Data Manager Command Line Interface

🚀 **Professional CLI for Dell PowerProtect Data Manager**

[![Go Version](https://img.shields.io/badge/Go-1.26.4-00ADD8?style=flat&logo=go)](https://golang.org)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Release](https://img.shields.io/github/v/release/bottkars/ppdm-cli)](https://github.com/bottkars/ppdm-cli/releases)
[![Build Status](https://github.com/bottkars/ppdm-cli/workflows/CI/badge.svg)](https://github.com/bottkars/ppdm-cli/actions)
[![ORAS](https://img.shields.io/badge/oras-registry-orange.svg)](https://quay.io/repository/delldps/ppdm-cli)

## 🎯 Quick Start

### Download Binary

```bash
# Linux AMD64
wget https://github.com/bottkars/ppdm-cli/releases/latest/download/ppdm-cli-linux-amd64
chmod +x ppdm-cli-linux-amd64
sudo mv ppdm-cli-linux-amd64 /usr/local/bin/ppdm-cli

# Verify installation
ppdm-cli version
```

### ORAS Registry

```bash
# Install ORAS
curl -LO https://github.com/oras-project/oras/releases/download/v0.16.0/oras_0.16.0_linux_amd64.tar.gz
tar -xzf oras_0.16.0_linux_amd64.tar.gz

# Download PPDM CLI
oras pull quay.io/delldps/ppdm-cli:latest-linux-amd64
```

### Docker

```bash
docker run --rm -it quay.io/delldps/ppdm-cli:latest version
```

## 📚 Documentation

### Core Documentation
- [Documentation Hub](docs/) - Complete documentation index
- [Complete Command Reference](docs/COMMAND_REFERENCE.md) - All 57 commands documented
- [Command Best Practices](docs/COMMAND_BEST_PRACTICES.md) - Development guidelines and standards
- [Command Audit Summary](docs/COMMAND_AUDIT_SUMMARY.md) - Best practices compliance audit

### Command Documentation
- [Activities](docs/commands/ACTIVITIES.md) - Activity monitoring and management
- [Assets](docs/commands/ASSETS.md) - Asset management and discovery
- [Backup](docs/commands/BACKUP.md) - Backup operations and server DR
- [Backup Restore](docs/commands/BACKUP_RESTORE.md) - Backup and restore operations
- [Copies](docs/commands/COPIES.md) - Backup copies analysis
- [Infrastructure Objects](docs/commands/INFRASTRUCTURE_OBJECTS.md) - Infrastructure management
- [Protection Policies](docs/commands/PROTECTION_POLICIES.md) - Protection policy management
- [Web Monitor](docs/commands/WEB_MONITOR.md) - Real-time web monitoring interface
- [Curl](docs/commands/CURL.md) - Direct API access
- [Shell Completion](docs/commands/COMPLETION_GUIDE.md) - Shell completion setup
- [Tools Wrapper](docs/commands/TOOLS_WRAPPER.md) - Interactive tools and monitoring

### Browser Tools
- [Activities Browser](docs/tools/activities-browser.md) - Interactive job group browser
- [Backup Browser](docs/tools/backup-browser.md) - Interactive asset selection browser
- [Copies Browser](docs/tools/copies-browser.md) - Interactive backup copies browser
- [Tools Overview](docs/tools/README.md) - All tools documentation

### Monitoring Tools
- [Monitor](docs/tools/monitor.md) - Real-time activity monitoring
- [Monitoring Metrics](docs/tools/monitoring-metrics.md) - System metrics and health status
- [Health](docs/tools/health.md) - System health checks and monitoring
- [Web Monitor](docs/commands/WEB_MONITOR.md) - Web-based activity monitoring

### AI Tools
- [AI Interface](docs/tools/ai.md) - AI-powered command interface with natural language support

### Getting Started
- [Configuration Quick Start](docs/getting-started/CONFIG_QUICK_START.md) - Quick configuration guide
- [Configuration Management](docs/getting-started/CONFIGURATION_MANAGEMENT.md) - Advanced configuration

### Deployment Guides
- [Docker Deployment](docs/deployment/DOCKER_DEPLOYMENT.md) - Docker container deployment
- [Kubernetes Deployment](docs/deployment/KUBERNETES_DEPLOYMENT.md) - Kubernetes deployment
- [OpenShift Deployment](docs/deployment/OPENSHIFT_4.20_SECURITY_FIXES.md) - OpenShift deployment

## 🔧 Features

- ✅ **Complete PPDM API Coverage** - All 80+ API endpoints
- ✅ **Advanced Filtering** - OData queries with server-side processing
- ✅ **Multiple Output Formats** - Table, JSON, YAML, CSV
- ✅ **Web Monitoring** - Real-time activity dashboard
- ✅ **Multi-Platform** - Linux, macOS, Windows, FreeBSD
- ✅ **Production Ready** - Enterprise-grade logging and error handling

## 🚀 Platform Support

| Platform | Architecture | Status |
|----------|-------------|--------|
| ![Linux](https://img.shields.io/badge/Linux-FCC624?style=flat&logo=linux) | AMD64, ARM64 | ✅ Supported |
| ![macOS](https://img.shields.io/badge/macOS-000000?style=flat&logo=apple) | AMD64, ARM64 | ✅ Supported |
| ![Windows](https://img.shields.io/badge/Windows-0078D6?style=flat&logo=windows) | AMD64 | ✅ Supported |
| ![FreeBSD](https://img.shields.io/badge/FreeBSD-AB2BDA?style=flat&logo=freebsd) | AMD64 | ✅ Supported |

## 🔗 Links

- [GitHub Releases](https://github.com/bottkars/ppdm-cli/releases)
- [ORAS Registry](https://quay.io/repository/delldps/ppdm-cli)
- [Documentation](docs/)
- [Issues & Support](https://github.com/bottkars/ppdm-cli/issues)

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

---

**Dell PowerProtect Data Manager CLI** - Professional command-line interface for enterprise data protection.
