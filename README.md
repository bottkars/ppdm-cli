# PPDM CLI - PowerProtect Data Manager Command Line Interface

🚀 **Professional CLI for Dell PowerProtect Data Manager**

[![Release](https://img.shields.io/github/release/bottkars/ppdm-cli.svg)](https://github.com/bottkars/ppdm-cli/releases)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
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

- [Complete Command Reference](docs/COMMAND_REFERENCE.md)
- [Installation Guide](docs/README.md#installation)
- [Configuration Guide](docs/README.md#configuration)
- [Examples](docs/README.md#examples)

## 🔧 Features

- ✅ **Complete PPDM API Coverage** - All 80+ API endpoints
- ✅ **Advanced Filtering** - OData queries with server-side processing
- ✅ **Multiple Output Formats** - Table, JSON, YAML, CSV
- ✅ **Web Monitoring** - Real-time activity dashboard
- ✅ **Multi-Platform** - Linux, macOS, Windows, FreeBSD
- ✅ **Production Ready** - Enterprise-grade logging and error handling

## 🚀 Platform Support

| Platform | Architecture | Download |
|----------|-------------|----------|
| Linux | AMD64, ARM64 | `ppdm-cli-linux-*` |
| macOS | AMD64, ARM64 | `ppdm-cli-darwin-*` |
| Windows | AMD64 | `ppdm-cli-windows-amd64.exe` |
| FreeBSD | AMD64 | `ppdm-cli-freebsd-amd64` |

## 🔗 Links

- [GitHub Releases](https://github.com/bottkars/ppdm-cli/releases)
- [ORAS Registry](https://quay.io/repository/delldps/ppdm-cli)
- [Documentation](docs/)
- [Issues & Support](https://github.com/bottkars/ppdm-cli/issues)

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

---

**Dell PowerProtect Data Manager CLI** - Professional command-line interface for enterprise data protection.
