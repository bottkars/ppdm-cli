# PPDM CLI Tools Documentation

This directory contains documentation for PPDM CLI tools - specialized commands that provide interactive interfaces, monitoring capabilities, and advanced features.

## Browser Tools

Interactive terminal-based browsers for navigating and selecting PPDM resources.

### [Activities Browser](activities-browser.md)
Interactive browser for PPDM job groups with hierarchical navigation and filtering capabilities.

**Features:**
- Hierarchical navigation (job groups → details → tasks)
- Category, status, and type filtering
- Search functionality
- Real-time data loading from PPDM API

**Usage:**
```bash
ppdm-cli activities-browser
ppdm-cli activities-browser --category BACKUP --status RUNNING
```

### [Backup Browser](backup-browser.md)
Interactive TUI browser for selecting assets and starting backups.

**Features:**
- Mouse and keyboard navigation
- Asset type filtering
- Backup type selection (FULL, INCREMENTAL, DIFFERENTIAL)
- Real-time asset loading

**Usage:**
```bash
ppdm-cli backup-browser
ppdm-cli backup-browser --force-tui
```

### [Copies Browser](copies-browser.md)
Interactive TUI browser for browsing and sorting backup copies.

**Features:**
- Advanced sorting (name, type, size, date, anomalies)
- Filter by copy type and anomaly status
- TUI and CLI modes
- Real-time copy loading

**Usage:**
```bash
ppdm-cli copies-browser
ppdm-cli copies-browser --force-tui
```

## Monitoring Tools

Real-time monitoring and metrics collection for PPDM system health and performance.

### [Monitor](monitor.md)
Real-time monitoring of PPDM activities with configurable refresh intervals.

**Features:**
- Configurable refresh interval
- Activity count control
- Category and type filtering
- Dashboard mode
- Interactive console mode

**Usage:**
```bash
ppdm-cli monitor
ppdm-cli monitor --category BACKUP --running-only
ppdm-cli monitor --interval 5 --count 20
```

### [Monitoring Metrics](monitoring-metrics.md)
Comprehensive system metrics and health status monitoring.

**Features:**
- Activity metrics (V3)
- Alert metrics
- Resource utilization metrics
- SLA compliance metrics
- Storage system metrics
- System health metrics

**Usage:**
```bash
ppdm-cli monitoring-metrics activity-metrics
ppdm-cli monitoring-metrics alert-metrics
ppdm-cli monitoring-metrics system-health-metrics
```

### [Health](health.md)
PPDM system health monitoring with health check triggering and result viewing.

**Features:**
- Health check types (V2)
- Health check triggering
- Health check results
- Health entities (V3)
- Health events (V3)

**Usage:**
```bash
ppdm-cli health list-types
ppdm-cli health trigger
ppdm-cli health entities
ppdm-cli health results
```

## AI Tools

AI-powered interfaces for natural language command generation and intelligent assistance.

### [AI Interface](ai.md)
AI-powered command interface using OpenAI and Ollama LLMs.

**Features:**
- Natural language to CLI command translation
- Safety validation for destructive operations
- Multi-provider support (OpenAI, Ollama)
- Command explanation
- Dry-run mode

**Usage:**
```bash
ppdm-cli ai "show me all policies with 0 assets"
ppdm-cli ai "what does this command do: protection-policies list"
ppdm-cli ai --provider openai "list storage systems"
```

## Related Documentation

- [Command Reference](../COMMAND_REFERENCE.md) - Complete command documentation
- [Command Best Practices](../COMMAND_BEST_PRACTICES.md) - Development guidelines
- [Activities Command](../commands/ACTIVITIES.md) - Activities command documentation
- [Backup Command](../commands/BACKUP.md) - Backup command documentation
- [Web Monitor](../commands/WEB_MONITOR.md) - Web-based activity monitoring

## Tool Comparison

| Tool | Type | Interface | Use Case |
|------|------|-----------|----------|
| **activities-browser** | Browser | TUI | Interactive job group navigation |
| **backup-browser** | Browser | TUI | Interactive asset selection for backups |
| **copies-browser** | Browser | TUI/CLI | Interactive backup copy exploration |
| **monitor** | Monitoring | CLI | Real-time activity monitoring |
| **monitoring-metrics** | Monitoring | CLI | System metrics and health status |
| **health** | Monitoring | CLI | System health checks and monitoring |
| **ai** | AI | CLI | Natural language command generation |
| **web-monitor** | Monitoring | Web | Web-based activity monitoring |

## Choosing the Right Tool

### For Interactive Navigation
- Use **activities-browser** for job group exploration
- Use **backup-browser** for asset selection and backup initiation
- Use **copies-browser** for backup copy analysis

### For Real-Time Monitoring
- Use **monitor** for activity monitoring with filtering
- Use **monitoring-metrics** for comprehensive system metrics
- Use **health** for system health checks
- Use **web-monitor** for web-based dashboard

### For Command Generation
- Use **ai** for natural language to CLI command translation
- Use **ai** for command explanation and learning

### For Automation
- Use **monitor** with CLI output for scripting
- Use **monitoring-metrics** for metrics collection
- Use **ai** with --show-command for command generation

## Quick Reference

### Browser Tools
```bash
ppdm-cli activities-browser
ppdm-cli backup-browser
ppdm-cli copies-browser
```

### Monitoring Tools
```bash
ppdm-cli monitor
ppdm-cli monitoring-metrics activity-metrics
ppdm-cli health trigger
```

### AI Tools
```bash
ppdm-cli ai "show me all assets"
ppdm-cli ai --provider openai "list policies"
```

---

*For complete command documentation, see the [main command reference](../COMMAND_REFERENCE.md).*
