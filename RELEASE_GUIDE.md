# PPDM CLI Release Guide

This guide explains how to create and manage releases for PPDM CLI using the automated release system.

## 🚀 Quick Release Process

### 1. Tag the Release
```bash
# Create and push a version tag
git tag v20.1.0.0
git push origin v20.1.0.0
```

### 2. Automatic Release
The GitHub Actions workflow will automatically:
- Build binaries for all platforms
- Create GitHub release with assets
- Push to ORAS registry
- Sync documentation to public folder

### 3. Manual Release (Optional)
```bash
# Run release script manually
./scripts/release.sh 20.1.0.0

# Dry run to test
./scripts/release.sh 20.1.0.0 --dry-run
```

## 📋 Release Process Overview

### Automated Workflow (Recommended)
1. **Trigger**: Git tag push (`v*`)
2. **Build**: Multi-platform binaries
3. **Test**: Run test suite
4. **Release**: Create GitHub release
5. **Distribute**: Push to ORAS registry
6. **Document**: Sync public documentation

### Manual Script
```bash
# Full release
./scripts/release.sh VERSION

# With options
./scripts/release.sh VERSION \
  --skip-tests \
  --skip-oras \
  --skip-docs
```

## 🔧 Configuration

### Required Secrets
Set these in GitHub repository settings:

```yaml
# GitHub Secrets
QUAY_USERNAME: your-quay-username
QUAY_PASSWORD: your-quay-password
GITHUB_TOKEN: (automatically provided)
```

### Environment Variables
```bash
# Local development
export QUAY_USERNAME="your-username"
export QUAY_PASSWORD="your-password"
```

## 📦 Distribution Channels

### 1. GitHub Releases
- **URL**: https://github.com/bottkars/ppdm-cli/releases
- **Assets**: Platform-specific binaries
- **Checksums**: SHA256 and SHA512 files
- **Notes**: Auto-generated release notes

### 2. ORAS Registry
- **Registry**: quay.io/delldps/ppdm-cli
- **Tags**: Version-specific + latest
- **Platforms**: All supported architectures

### 3. Public Documentation
- **Repository**: https://github.com/bottkars/ppdm-cli
- **Location**: `/docs` folder
- **Content**: Filtered public documentation

## 🎯 Platform Support

| Platform | Architecture | Binary Name | ORAS Tag |
|----------|-------------|-------------|----------|
| Linux | AMD64 | `ppdm-cli-linux-amd64` | `linux-amd64` |
| Linux | ARM64 | `ppdm-cli-linux-arm64` | `linux-arm64` |
| macOS | AMD64 | `ppdm-cli-darwin-amd64` | `darwin-amd64` |
| macOS | ARM64 | `ppdm-cli-darwin-arm64` | `darwin-arm64` |
| Windows | AMD64 | `ppdm-cli-windows-amd64.exe` | `windows-amd64` |
| FreeBSD | AMD64 | `ppdm-cli-freebsd-amd64` | `freebsd-amd64` |

## 📋 Release Checklist

### Before Release
- [ ] All tests passing
- [ ] Documentation updated
- [ ] Version number updated
- [ ] CHANGELOG.md updated
- [ ] Release notes prepared

### During Release
- [ ] Tag created and pushed
- [ ] GitHub Actions running
- [ ] Binaries building successfully
- [ ] Release created on GitHub
- [ ] ORAS registry updated
- [ ] Documentation synced

### After Release
- [ ] Test binary downloads
- [ ] Verify ORAS registry access
- [ ] Check documentation links
- [ ] Announce release
- [ ] Update project status

## 🧪 Testing Releases

### Dry Run Testing
```bash
# Test release without actual changes
./scripts/release.sh 20.1.0.0 --dry-run

# Test specific components
./scripts/release.sh 20.1.0.0 --dry-run --skip-tests
./scripts/release.sh 20.1.0.0 --dry-run --skip-oras
```

### Manual Testing
```bash
# Download and test binary
wget https://github.com/bottkars/ppdm-cli/releases/latest/download/ppdm-cli-linux-amd64
chmod +x ppdm-cli-linux-amd64
./ppdm-cli-linux-amd64 version

# Test ORAS download
oras pull quay.io/delldps/ppdm-cli:latest-linux-amd64
./ppdm-cli version
```

## 📚 Documentation Management

### Sync Documentation
```bash
# Sync docs to public folder
./scripts/sync-docs.sh

# Dry run to see changes
./scripts/sync-docs.sh --dry-run

# Validate existing docs
./scripts/sync-docs.sh --validate-only
```

### Documentation Structure
```
public/
├── README.md              # Main public README
├── LICENSE                 # MIT license
├── docs/
│   ├── README.md          # Documentation index
│   ├── COMMAND_REFERENCE.md
│   └── commands/
│       ├── ACTIVITIES.md
│       ├── ASSETS.md
│       └── ...
```

## 🔄 Release Workflow Examples

### Standard Release
```bash
# 1. Update version in code
# 2. Update CHANGELOG.md
# 3. Commit changes
git add .
git commit -m "Release v20.1.0.0"
git push

# 4. Tag and push
git tag v20.1.0.0
git push origin v20.1.0.0

# 5. Monitor GitHub Actions
# 6. Verify release
```

### Hotfix Release
```bash
# 1. Create hotfix branch
git checkout -b hotfix/v20.1.0.1

# 2. Fix issue and test
# 3. Update version
git commit -m "Hotfix v20.1.0.1"
git push origin hotfix/v20.1.0.1

# 4. Tag and release
git tag v20.1.0.1
git push origin v20.1.0.1

# 5. Merge back to main
git checkout main
git merge hotfix/v20.1.0.1
git push origin main
```

### Beta Release
```bash
# 1. Create beta version
./scripts/release.sh 20.1.1.0-beta

# 2. Manual release with prerelease flag
gh release create v20.1.1.0-beta \
  --title "PPDM CLI v20.1.1.0-beta" \
  --prerelease \
  dist/*
```

## 🔍 Troubleshooting

### Common Issues

#### Build Failures
```bash
# Check build logs in GitHub Actions
# Verify Go version compatibility
# Check for dependency issues
```

#### ORAS Push Failures
```bash
# Check credentials
echo $QUAY_USERNAME $QUAY_PASSWORD

# Test ORAS login
echo $QUAY_PASSWORD | oras login quay.io -u $QUAY_USERNAME --password-stdin

# Check registry access
oras repo ls quay.io/delldps/ppdm-cli
```

#### Documentation Sync Issues
```bash
# Validate documentation structure
./scripts/sync-docs.sh --validate-only

# Check file permissions
ls -la public/docs/

# Manual sync test
./scripts/sync-docs.sh --dry-run
```

### Debug Mode
```bash
# Enable debug logging
export DEBUG=true

# Run release with verbose output
./scripts/release.sh 20.1.0.0 --dry-run
```

## 📊 Release Statistics

### Metrics to Track
- [ ] Download counts per platform
- [ ] ORAS registry pulls
- [ ] GitHub stars/forks
- [ ] Issue resolution time
- [ ] Release frequency

### Monitoring
```bash
# Check GitHub release stats
gh release view --json assets,downloads

# Check ORAS registry
oras repo tags quay.io/delldps/ppdm-cli

# Monitor repository activity
gh api repos/bottkars/ppdm-cli/releases
```

## 🎯 Best Practices

### Version Management
- Use semantic versioning (MAJOR.MINOR.PATCH)
- Tag releases consistently (`vX.Y.Z`)
- Update CHANGELOG.md for each release
- Use beta/alpha for pre-releases

### Release Quality
- Always run full test suite
- Test binaries on multiple platforms
- Verify documentation accuracy
- Check all distribution channels

### Security
- Keep credentials secure
- Use GitHub secrets for sensitive data
- Validate checksums for binaries
- Review release notes for sensitive information

---

For more information, see the main [README.md](README.md) or check the [GitHub repository](https://github.com/bottkars/ppdm-cli).
