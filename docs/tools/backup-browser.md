# Backup Browser Tool

## Overview

The `backup-browser` command provides an interactive terminal-based browser for selecting assets and starting backups. It offers a professional TUI (Text User Interface) with mouse support, keyboard navigation, and real-time asset loading from the PPDM API.

## Features

### Navigation Controls
- **Arrow keys**: Navigate up/down in lists
- **Tab key**: Switch between views (types/assets/confirm)
- **Left arrow**: Go back to types view
- **Number keys 1/2/3**: Change backup type (INCREMENTAL/FULL/DIFFERENTIAL)
- **Escape/q/Q**: Quit or go back to previous view
- **Enter key**: Confirm selection and start backup

### Interface Features
- **Mouse click navigation**: Built-in tview mouse support
- **Keyboard navigation**: Arrow keys for keyboard-only operation
- **Asset filtering by type**: Filter assets by their type
- **Backup type selection**: Choose between INCREMENTAL, FULL, or DIFFERENTIAL
- **Real-time asset loading**: Assets loaded directly from PPDM API
- **Professional TUI interface**: Status indicators and clean design

## Usage

### Basic Usage

```bash
# Start the backup browser
ppdm-cli backup-browser

# Force TUI mode in non-interactive terminals
ppdm-cli backup-browser --force-tui
```

## Views

### 1. Types View
Shows available asset types:
- VMWARE_VIRTUAL_MACHINE
- MICROSOFT_SQL_SERVER
- MICROSOFT_EXCHANGE_DATABASE
- NAS_SHARE
- KUBERNETES
- And more...

### 2. Assets View
Shows assets of the selected type with:
- Asset name
- Asset ID
- Protection status
- Other relevant information

### 3. Confirm View
Shows backup confirmation with:
- Selected assets
- Backup type
- Policy information
- Confirmation button

## Backup Types

The browser supports three backup types:

| Type | Description | Use Case |
|------|-------------|----------|
| **FULL** | Complete backup of all data | Full system recovery, initial backup |
| **INCREMENTAL** | Backup changes since last backup | Frequent backups, faster processing |
| **DIFFERENTIAL** | Backup changes since last full backup | Balance between speed and recovery time |

### Changing Backup Type
- Press `1` for INCREMENTAL
- Press `2` for FULL
- Press `3` for DIFFERENTIAL

## Flags

| Flag | Description | Default |
|------|-------------|---------|
| `--force-tui` | Force TUI mode even in non-interactive terminals | Auto-detect |

## Examples

### Start Backup Browser

```bash
ppdm-cli backup-browser
```

This will:
1. Show the types view with available asset types
2. Allow you to select an asset type
3. Show assets of that type
4. Allow you to select assets
5. Show confirmation with backup type selection
6. Start the backup when confirmed

### Force TUI Mode

```bash
ppdm-cli backup-browser --force-tui
```

Use this if the auto-detection fails to recognize your terminal as interactive.

## Workflow

### Step-by-Step Backup Process

1. **Select Asset Type**
   - Use arrow keys to navigate
   - Press Enter to select asset type
   - Tab to switch views

2. **Select Assets**
   - Browse available assets
   - Use arrow keys to navigate
   - Press Enter to select/deselect assets
   - Left arrow to go back to types view

3. **Configure Backup**
   - Press 1/2/3 to select backup type
   - Review selected assets
   - Check policy information

4. **Confirm and Start**
   - Press Enter to confirm
   - Backup will be initiated
   - Monitor backup progress with activities command

## Use Cases

### 1. Quick Ad-Hoc Backup

```bash
ppdm-cli backup-browser
```

Select assets and start a backup quickly without remembering asset IDs or policy details.

### 2. Test Backup Before Schedule

```bash
ppdm-cli backup-browser
```

Start a test backup to verify everything works before scheduled backup runs.

### 3. Emergency Backup

```bash
ppdm-cli backup-browser
```

Quickly start an emergency backup for critical assets.

### 4. Backup Type Testing

```bash
ppdm-cli backup-browser
```

Test different backup types (FULL, INCREMENTAL, DIFFERENTIAL) to find optimal settings.

## Tips and Best Practices

### 1. Use Appropriate Backup Type
- **FULL**: For initial backups or complete recovery needs
- **INCREMENTAL**: For frequent, fast backups
- **DIFFERENTIAL**: For balance between speed and recovery time

### 2. Check Protection Status
- Only protected assets can be backed up
- Unprotected assets will be skipped
- Use `ppdm-cli assets list --protection-status UNPROTECTED` to check

### 3. Monitor Backup Progress
```bash
# After starting backup with browser
ppdm-cli activities list --filter 'type eq "BACKUP" and status eq "RUNNING"'
```

### 4. Use Keyboard Navigation
- Arrow keys are faster than mouse for experienced users
- Tab key quickly switches between views
- Number keys (1/2/3) quickly change backup type

### 5. Verify Backup Completion
```bash
# Check backup status
ppdm-cli activities show --id <activity-id>

# Check backup copies
ppdm-cli copies list --asset-id <asset-id>
```

## Troubleshooting

### Browser Not Starting

```bash
# Force TUI mode
ppdm-cli backup-browser --force-tui

# Check terminal compatibility
echo $TERM

# Check PPDM connection
ppdm-cli status
```

### No Assets Shown

```bash
# Check if assets exist
ppdm-cli assets list

# Check protection status
ppdm-cli assets list --protection-status PROTECTED

# Use debug mode
ppdm-cli --debug backup-browser
```

### Backup Fails to Start

```bash
# Check asset protection status
ppdm-cli assets show --id <asset-id>

# Check policy exists
ppdm-cli protection-policies list

# Use debug mode to see errors
ppdm-cli --debug backup-browser
```

### Navigation Issues

- Ensure terminal supports cursor keys
- Try `--force-tui` flag
- Check terminal type: `echo $TERM`
- Use mouse if keyboard navigation fails

## Related Commands

- **backup create** - Create manual backup with command-line flags
- **assets** - List and manage assets
- **protection-policies** - Manage backup policies
- **activities** - Monitor backup activities
- **copies** - Analyze backup copies

## API Information

- **Operation ID**: CreateProtection
- **API Version**: v3
- **Endpoint**: POST /api/v3/protections
- **API Reference**: ppdm-public-v3-20.3.0.0.yaml

## Comparison: Backup Browser vs Command Line

### Backup Browser
- ✅ Interactive selection
- ✅ Visual interface
- ✅ No need to remember asset IDs
- ✅ Easy for occasional users
- ❌ Slower for experienced users
- ❌ Not scriptable

### Command Line
- ✅ Fast for experienced users
- ✅ Scriptable
- ✅ Precise control
- ❌ Requires knowing asset IDs
- ❌ Less intuitive for new users

---

*For more information about other tools, see the [tools documentation index](README.md).*