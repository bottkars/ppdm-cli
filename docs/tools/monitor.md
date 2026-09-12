# Monitor Tool

## Overview

The `monitor` command provides real-time monitoring of PPDM activities with configurable refresh intervals. It offers multiple modes including continuous monitoring, interactive console mode, and dashboard mode for different use cases.

## Features

### Real-Time Monitoring
- **Configurable refresh interval**: Set how often to refresh activity data (default: 10 seconds)
- **Activity count control**: Display 5-50 activities at a time
- **Infinite or limited cycles**: Monitor indefinitely or for a specific number of cycles
- **Dashboard mode**: Redraw output on same lines for dashboard-style display

### Filtering Capabilities
- **Category filtering**: Filter by activity category (BACKUP, RESTORE, DISCOVERY, etc.)
- **Type filtering**: Filter by activity type (TASK, JOB, JOB_GROUP)
- **Search**: Search for activities containing specific text
- **Running-only**: Show only currently running activities

### Interactive Mode
- **Keyboard controls**: Interactive console mode with keyboard shortcuts
- **Real-time updates**: Immediate response to activity changes
- **User-friendly interface**: Easy-to-use interactive controls

## Usage

### Basic Usage

```bash
# Monitor with default settings (10s interval, infinite, 10 activities)
ppdm-cli monitor

# Monitor with 5 second interval, 20 cycles, 20 activities
ppdm-cli monitor --interval 5 --count 20 --activities 20

# Monitor in interactive console mode
ppdm-cli monitor --interactive

# Monitor only running activities
ppdm-cli monitor --running-only
```

### Advanced Filtering

```bash
# Monitor specific activity categories
ppdm-cli monitor --category BACKUP
ppdm-cli monitor --category RESTORE
ppdm-cli monitor --category DISCOVERY

# Monitor specific activity types
ppdm-cli monitor --type TASK
ppdm-cli monitor --type JOB
ppdm-cli monitor --type JOB_GROUP

# Monitor with search
ppdm-cli monitor --search "database"

# Combine filters
ppdm-cli monitor --category BACKUP --type JOB --search "production"
```

### Dashboard Mode

```bash
# Monitor in dashboard mode (no scrolling)
ppdm-cli monitor --no-scroll

# Dashboard mode with custom settings
ppdm-cli monitor --no-scroll --interval 5 --activities 20
```

## Flags

| Flag | Short | Description | Default |
|------|-------|-------------|---------|
| `--interval` | `-i` | Refresh interval in seconds | 10 |
| `--count` | `-c` | Number of refresh cycles (0 = infinite) | 0 (infinite) |
| `--activities` | | Number of activities to display (5-50) | 10 |
| `--category` | | Filter by activity category | All categories |
| `--type` | | Filter by activity type (TASK, JOB, JOB_GROUP) | All types |
| `--search` | | Search for activities containing specific text | No search |
| `--running-only` | | Show only running activities | Show all |
| `--interactive` | `-I` | Interactive console mode with keyboard controls | Non-interactive |
| `--no-scroll` | | Redraw output on same lines (dashboard mode) | Scrolling mode |

## Activity Categories

The monitor supports filtering by these PPDM activity categories:
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

## Activity Types

- **TASK**: Individual task activities
- **JOB**: Job activities
- **JOB_GROUP**: Job group activities

## Examples

### Monitor All Activities

```bash
ppdm-cli monitor
```

This will:
- Refresh every 10 seconds
- Show 10 activities at a time
- Run indefinitely (until interrupted)
- Show all activity types and categories

### Monitor Running Backups

```bash
ppdm-cli monitor --category BACKUP --running-only
```

This will:
- Show only backup activities
- Show only currently running backups
- Refresh every 10 seconds
- Run indefinitely

### Monitor with Custom Settings

```bash
ppdm-cli monitor --interval 5 --count 20 --activities 20
```

This will:
- Refresh every 5 seconds
- Show 20 activities at a time
- Run for 20 cycles (100 seconds total)
- Show all activity types and categories

### Dashboard Mode

```bash
ppdm-cli monitor --no-scroll --interval 5 --activities 15
```

This will:
- Redraw output on same lines (dashboard style)
- Refresh every 5 seconds
- Show 15 activities
- Run indefinitely

### Interactive Mode

```bash
ppdm-cli monitor --interactive
```

This will:
- Start interactive console mode
- Provide keyboard controls
- Allow real-time interaction
- Show all activities

### Search-Specific Activities

```bash
ppdm-cli monitor --search "database"
```

This will:
- Show only activities containing "database"
- Refresh every 10 seconds
- Run indefinitely

## Use Cases

### 1. Monitor Backup Operations

```bash
ppdm-cli monitor --category BACKUP --running-only
```

Monitor currently running backup operations in real-time.

### 2. Monitor Restore Operations

```bash
ppdm-cli monitor --category RESTORE --running-only
```

Monitor currently running restore operations.

### 3. Monitor Specific Assets

```bash
ppdm-cli monitor --search "production-server"
```

Monitor activities related to a specific asset or server.

### 4. Dashboard Display

```bash
ppdm-cli monitor --no-scroll --interval 5
```

Create a dashboard-style display that updates every 5 seconds.

### 5. Short-Term Monitoring

```bash
ppdm-cli monitor --interval 2 --count 30
```

Monitor for 60 seconds (30 cycles × 2 seconds) with frequent updates.

### 6. Monitor Disaster Recovery

```bash
ppdm-cli monitor --category DISASTER_RECOVERY --running-only
```

Monitor disaster recovery operations in real-time.

## Tips and Best Practices

### 1. Choose Appropriate Refresh Interval
- **Fast (2-5s)**: For critical operations or short-term monitoring
- **Normal (10s)**: For general monitoring (default)
- **Slow (30-60s)**: For long-term monitoring or dashboard display

### 2. Use Category Filters for Focus
```bash
# Focus on specific operation type
ppdm-cli monitor --category BACKUP
ppdm-cli monitor --category REPLICATE
ppdm-cli monitor --category RESTORE
```

### 3. Use Running-Only for Active Monitoring
```bash
# Show only active operations
ppdm-cli monitor --running-only
```

### 4. Use Dashboard Mode for Display
```bash
# Dashboard-style display
ppdm-cli monitor --no-scroll
```

### 5. Combine Filters for Precision
```bash
# Multiple filters
ppdm-cli monitor --category BACKUP --type JOB --running-only
```

### 6. Use Search for Asset-Specific Monitoring
```bash
# Monitor specific assets
ppdm-cli monitor --search "database"
ppdm-cli monitor --search "production"
```

## Troubleshooting

### Monitor Not Updating

```bash
# Check refresh interval
ppdm-cli monitor --interval 5

# Check PPDM connection
ppdm-cli status

# Use debug mode
ppdm-cli --debug monitor
```

### No Activities Shown

```bash
# Check if filters are too restrictive
ppdm-cli monitor

# Try without filters
ppdm-cli monitor --category ""

# Check if activities exist
ppdm-cli activities list
```

### Performance Issues

```bash
# Reduce activity count
ppdm-cli monitor --activities 5

# Increase refresh interval
ppdm-cli monitor --interval 30

# Use running-only filter
ppdm-cli monitor --running-only
```

### Dashboard Mode Issues

```bash
# Ensure terminal supports cursor positioning
echo $TERM

# Try without dashboard mode
ppdm-cli monitor

# Force dashboard mode
ppdm-cli monitor --no-scroll
```

## Related Commands

- **activities** - List and manage activities
- **activities list** - List all activities with filtering
- **activities show** - Show specific activity details
- **activities-browser** - Interactive browser for job groups
- **web-monitor** - Web-based activity monitoring

## API Information

- **Operation ID**: getAllActivities
- **API Version**: v3
- **Endpoint**: GET /api/v3/activities
- **API Reference**: ppdm-public-v3-20.2.0.0.yaml

## Comparison: Monitor vs Other Tools

### Monitor (CLI)
- ✅ Real-time updates
- ✅ Configurable refresh
- ✅ Filtering capabilities
- ✅ Scriptable
- ❌ Terminal-based only
- ❌ No visual interface

### Activities Browser
- ✅ Interactive navigation
- ✅ Hierarchical view
- ✅ Visual interface
- ❌ Not real-time
- ❌ Manual refresh

### Web Monitor
- ✅ Web-based interface
- ✅ Real-time updates
- ✅ Visual dashboard
- ✅ Remote access
- ❌ Requires web server
- ❌ More complex setup

---

*For more information about other tools, see the [tools documentation index](README.md).*
