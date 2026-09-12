# Copies Browser Tool

## Overview

The `copies-browser` command provides an interactive terminal-based browser for browsing and sorting backup copies. It offers a professional TUI (Text User Interface) with advanced sorting, filtering, and real-time data loading from the PPDM API.

## Features

### Navigation Controls
- **Arrow keys**: Navigate up/down in list
- **Tab key**: Switch between views (list/details/sort)
- **Left arrow**: Go back to previous view
- **Number keys 1-6**: Change sort criteria
- **s/S**: Toggle sort order (ascending/descending)
- **f/F**: Change filter
- **Escape/q/Q**: Quit or go back
- **Enter key**: View copy details

### Interface Features
- **Sort by multiple criteria**: Name, type, size, date, anomalies
- **Filter by copy type**: Filter by copy type and anomaly status
- **Real-time copy loading**: Copies loaded directly from PPDM API
- **Professional TUI interface**: Status indicators and clean design
- **Flexible mode**: TUI or CLI mode based on terminal detection

## Usage

### Basic Usage

```bash
# Start the copies browser (auto-detects terminal)
ppdm-cli copies-browser

# Force TUI mode in non-interactive terminals
ppdm-cli copies-browser --force-tui

# Force CLI mode in interactive terminals
ppdm-cli copies-browser --force-cli
```

## Views

### 1. List View
Shows backup copies with:
- Copy name
- Copy type
- Size
- Creation date
- Anomaly status
- Other relevant information

### 2. Details View
Shows detailed information about selected copy:
- Complete copy metadata
- Storage location
- Retention information
- Anomaly details
- Associated asset information

### 3. Sort View
Shows available sort criteria:
- Name
- Type
- Size
- Date
- Anomalies
- Custom criteria

## Sort Criteria

The browser supports sorting by these criteria (number keys 1-6):

| Key | Criteria | Description |
|-----|----------|-------------|
| **1** | Name | Sort by copy name alphabetically |
| **2** | Type | Sort by copy type (ACTIVE, EXPIRED, etc.) |
| **3** | Size | Sort by copy size (ascending/descending) |
| **4** | Date | Sort by creation date |
| **5** | Anomalies | Sort by anomaly status |
| **6** | Custom | Custom sort criteria |

### Toggle Sort Order
- Press `s` or `S` to toggle between ascending and descending order

## Filter Options

The browser supports filtering by:
- **Copy type**: ACTIVE, EXPIRED, CORRUPTED, etc.
- **Anomaly status**: With/without anomalies
- **Asset type**: Filter by asset type
- **Time range**: Filter by creation date

### Change Filter
- Press `f` or `F` to open filter options
- Select filter criteria
- Apply filter to view

## Flags

| Flag | Description | Default |
|------|-------------|---------|
| `--force-tui` | Force TUI mode even in non-interactive terminals | Auto-detect |
| `--force-cli` | Force CLI mode even in interactive terminals | Auto-detect |

## Examples

### Start Copies Browser

```bash
ppdm-cli copies-browser
```

This will:
1. Show the list view with all backup copies
2. Allow you to sort by different criteria
3. Allow you to filter copies
4. Allow you to view copy details
5. Navigate between views with Tab key

### Force TUI Mode

```bash
ppdm-cli copies-browser --force-tui
```

Use this if the auto-detection fails to recognize your terminal as interactive.

### Force CLI Mode

```bash
ppdm-cli copies-browser --force-cli
```

Use this if you prefer CLI output even in an interactive terminal.

## Workflow

### Step-by-Step Copy Exploration

1. **View All Copies**
   - List view shows all backup copies
   - Use arrow keys to navigate
   - Tab to switch views

2. **Sort Copies**
   - Press 1-6 to select sort criteria
   - Press s/S to toggle sort order
   - View sorted list

3. **Filter Copies**
   - Press f/F to open filter options
   - Select filter criteria
   - Apply filter

4. **View Details**
   - Navigate to desired copy
   - Press Enter to view details
   - Left arrow to go back

5. **Exit**
   - Press q/Q or Escape to quit

## Use Cases

### 1. Find Largest Copies

```bash
ppdm-cli copies-browser
# Press 3 to sort by size
# Press s to sort descending
```

Find the largest backup copies to analyze storage usage.

### 2. Find Expired Copies

```bash
ppdm-cli copies-browser
# Press f to open filter
# Filter by copy type EXPIRED
```

Identify expired copies that may need cleanup.

### 3. Find Copies with Anomalies

```bash
ppdm-cli copies-browser
# Press 5 to sort by anomalies
# Or filter by anomaly status
```

Identify copies with anomalies that may need investigation.

### 4. Analyze Recent Copies

```bash
ppdm-cli copies-browser
# Press 4 to sort by date
# Press s to sort descending (newest first)
```

View the most recent backup copies.

### 5. Investigate Specific Copy

```bash
ppdm-cli copies-browser
# Navigate to copy
# Press Enter to view details
```

Get detailed information about a specific backup copy.

## Tips and Best Practices

### 1. Use Appropriate Sort Criteria
- **Size**: For storage analysis
- **Date**: For recent activity review
- **Anomalies**: For troubleshooting
- **Type**: For status review

### 2. Combine Sort and Filter
```bash
# Sort by size, filter by ACTIVE type
ppdm-cli copies-browser
# Press 3 (size), then s (descending)
# Press f, filter by ACTIVE
```

### 3. Use CLI Mode for Scripting
```bash
# Force CLI mode for pipe operations
ppdm-cli copies-browser --force-cli | grep "ACTIVE"
```

### 4. Monitor Copy Status
```bash
# Regularly check for anomalies
ppdm-cli copies-browser
# Sort by anomalies (5)
# Review copies with anomalies
```

### 5. Clean Up Old Copies
```bash
# Find expired copies
ppdm-cli copies-browser
# Filter by EXPIRED
# Sort by date
# Identify candidates for cleanup
```

## Troubleshooting

### Browser Not Starting

```bash
# Force TUI mode
ppdm-cli copies-browser --force-tui

# Force CLI mode
ppdm-cli copies-browser --force-cli

# Check terminal compatibility
echo $TERM

# Check PPDM connection
ppdm-cli status
```

### No Copies Shown

```bash
# Check if copies exist
ppdm-cli copies list

# Use debug mode
ppdm-cli --debug copies-browser
```

### Sort Not Working

- Ensure you're in list view
- Press number keys 1-6 to select sort criteria
- Press s/S to toggle sort order
- Check if filter is applied (may limit results)

### Filter Not Working

- Press f/F to open filter options
- Ensure filter criteria are valid
- Clear filter to see all copies
- Use debug mode to check API calls

## Related Commands

- **copies** - Analyze backup copies with command-line interface
- **backup-restore** - Manage backup and restore operations
- **assets** - Manage protected assets
- **activities** - Monitor backup activities

## API Information

- **Operation ID**: getCopies
- **API Version**: v2
- **Endpoint**: GET /api/v2/copies
- **API Reference**: ppdm-public-v2-20.3.0.0.yaml

## Comparison: Copies Browser vs Command Line

### Copies Browser
- ✅ Interactive exploration
- ✅ Visual sorting and filtering
- ✅ Easy for occasional users
- ✅ Quick ad-hoc analysis
- ❌ Slower for experienced users
- ❌ Not scriptable

### Command Line
- ✅ Fast for experienced users
- ✅ Scriptable and automatable
- ✅ Precise control with filters
- ✅ Output to files/pipes
- ❌ Requires knowing filter syntax
- ❌ Less intuitive for new users

---

*For more information about other tools, see the [tools documentation index](README.md).*