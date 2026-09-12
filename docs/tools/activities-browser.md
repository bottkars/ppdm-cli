# Activities Browser Tool

## Overview

The `activities-browser` command provides an interactive terminal-based browser for navigating PPDM job groups. It offers a hierarchical navigation system with filtering capabilities to explore activities in a user-friendly interface.

## Features

### Hierarchical Navigation
- **Level 1**: List ONLY job groups (filterable by category, type, status, search) - excludes all jobs and tasks
- **Level 2**: Select a job group to show detailed information
- **Level 3**: Show individual tasks/sub-tasks of the selected job group

**IMPORTANT**: Main window shows ONLY JOB_GROUP type activities with children. Individual jobs and tasks are NOT shown in the main list.

### Navigation Controls
- **Cursor keys**: Navigate through the list
- **Enter**: Drill down into selected item
- **Escape**: Go back to previous level
- **q**: Quit the browser
- **f**: Filter activities

### Activity Categories

The browser supports filtering by these PPDM activity categories:
- ARCHIVE
- BACKUP
- CLOUD_PROTECT
- CLOUD_TIER
- CONFIG
- DELETE
- DISASTER_RECOVERY
- DISCOVERY
- EXPORT
- MANAGE
- MIGRATE
- PUSH_UPDATE
- REPLICATE
- RESTORE
- SYSTEM
- VALIDATE
- INDEX
- GARBAGE_COLLECTION
- DATA_MOVEMENT
- ANOMALY_DETECTION
- SECURITY
- UPDATE
- QUICK_RECOVERY

## Usage

### Basic Usage

```bash
# Start the browser with all job groups
ppdm-cli activities-browser

# Start with category filter
ppdm-cli activities-browser --category BACKUP

# Start with status filter
ppdm-cli activities-browser --status RUNNING

# Start with result filter
ppdm-cli activities-browser --result SUCCESS

# Start with search term
ppdm-cli activities-browser --search "database"

# Start with type filter
ppdm-cli activities-browser --type JOB_GROUP
```

### Advanced Filtering

```bash
# Filter by multiple criteria
ppdm-cli activities-browser --category BACKUP --status RUNNING

# Search within specific category
ppdm-cli activities-browser --category REPLICATE --search "production"

# Show only failed activities
ppdm-cli activities-browser --result FAILED

# Show only successful backups
ppdm-cli activities-browser --category BACKUP --result SUCCESS
```

## Flags

| Flag | Short | Description | Default |
|------|-------|-------------|---------|
| `--category` | `-c` | Filter by activity category | All categories |
| `--status` | `-s` | Filter by status (SUCCESS, FAILED, RUNNING, etc.) | All statuses |
| `--result` | `-r` | Filter by result status (SUCCESS, FAILED) | All results |
| `--search` | `-S` | Search in activity names | No search |
| `--type` | `-t` | Filter by activity type (JOB_GROUP, JOB, TASK) | JOB_GROUP |

## Examples

### Browse All Job Groups

```bash
ppdm-cli activities-browser
```

This will display all job groups in the main window. You can:
- Navigate with cursor keys
- Press Enter to see details of a job group
- Press Escape to go back
- Press 'f' to apply filters
- Press 'q' to quit

### Browse Backup Activities

```bash
ppdm-cli activities-browser --category BACKUP
```

This will show only backup-related job groups.

### Browse Running Activities

```bash
ppdm-cli activities-browser --status RUNNING
```

This will show only currently running job groups.

### Search for Specific Activities

```bash
ppdm-cli activities-browser --search "database"
```

This will show job groups containing "database" in their names.

### Browse Failed Replications

```bash
ppdm-cli activities-browser --category REPLICATE --result FAILED
```

This will show only failed replication job groups.

## Use Cases

### 1. Monitor Running Backups

```bash
ppdm-cli activities-browser --category BACKUP --status RUNNING
```

Navigate through running backup job groups to see their current status and progress.

### 2. Investigate Failed Activities

```bash
ppdm-cli activities-browser --result FAILED
```

Browse through failed job groups to understand what went wrong and drill down into individual tasks.

### 3. Search for Specific Assets

```bash
ppdm-cli activities-browser --search "production-server"
```

Find job groups related to a specific asset or server.

### 4. Monitor Replication Status

```bash
ppdm-cli activities-browser --category REPLICATE --status RUNNING
```

Monitor replication job groups to ensure data is being replicated correctly.

## Tips and Best Practices

### 1. Use Category Filters for Focus
```bash
# Focus on specific activity type
ppdm-cli activities-browser --category BACKUP
ppdm-cli activities-browser --category REPLICATE
ppdm-cli activities-browser --category RESTORE
```

### 2. Combine Filters for Precision
```bash
# Show only running backups
ppdm-cli activities-browser --category BACKUP --status RUNNING

# Show only failed replications
ppdm-cli activities-browser --category REPLICATE --result FAILED
```

### 3. Use Search for Quick Access
```bash
# Find activities by name
ppdm-cli activities-browser --search "database"
ppdm-cli activities-browser --search "production"
```

### 4. Navigate Hierarchically
- Start at Level 1 to see all job groups
- Press Enter to drill down into a job group
- Press Escape to go back
- Use 'q' to quit when done

### 5. Use Filter Mode (f key)
- Press 'f' to enter filter mode
- Apply multiple filters dynamically
- Clear filters to see all activities

## Troubleshooting

### No Activities Shown

```bash
# Check if filters are too restrictive
ppdm-cli activities-browser

# Try without filters
ppdm-cli activities-browser --category ""
```

### Browser Not Responding

```bash
# Check PPDM connection
ppdm-cli status

# Try with debug mode
ppdm-cli --debug activities-browser
```

### Navigation Issues

- Use cursor keys (up/down) to navigate
- Press Enter to select an item
- Press Escape to go back
- Press 'q' to quit at any time

## Related Commands

- **activities** - List and manage activities with filtering
- **activities list** - List all activities with advanced filtering
- **activities show** - Show specific activity details
- **monitor** - Real-time monitoring of activities
- **web-monitor** - Web-based activity monitoring

## API Information

- **Operation ID**: getAllActivities
- **API Version**: v3
- **Endpoint**: GET /api/v3/activities
- **API Reference**: ppdm-public-v3-20.3.0.0.yaml

---

*For more information about other tools, see the [tools documentation index](README.md).*