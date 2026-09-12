# Tools Wrapper Documentation

## Overview

The PPDM CLI includes several interactive tools and wrappers that provide enhanced user experience for common operations. These tools include browser interfaces, monitoring dashboards, and AI-powered command generation.

## Available Tools

### Browser Tools

Interactive terminal-based browsers for navigating and selecting PPDM resources.

#### activities-browser
Interactive browser for PPDM job groups with hierarchical navigation and filtering capabilities.

**Usage:**
```bash
ppdm-cli activities-browser
ppdm-cli activities-browser --category BACKUP --status RUNNING
```

**Features:**
- Hierarchical navigation (job groups → details → tasks)
- Category, status, and type filtering
- Search functionality
- Real-time data loading from PPDM API

**Documentation:** [activities-browser.md](tools/activities-browser.md)

#### backup-browser
Interactive TUI browser for selecting assets and starting backups.

**Usage:**
```bash
ppdm-cli backup-browser
ppdm-cli backup-browser --force-tui
```

**Features:**
- Mouse and keyboard navigation
- Asset type filtering
- Backup type selection (FULL, INCREMENTAL, DIFFERENTIAL)
- Real-time asset loading

**Documentation:** [backup-browser.md](tools/backup-browser.md)

#### copies-browser
Interactive TUI browser for browsing and sorting backup copies.

**Usage:**
```bash
ppdm-cli copies-browser
ppdm-cli copies-browser --force-tui
```

**Features:**
- Advanced sorting (name, type, size, date, anomalies)
- Filter by copy type and anomaly status
- TUI and CLI modes
- Real-time copy loading

**Documentation:** [copies-browser.md](tools/copies-browser.md)

### Monitoring Tools

Real-time monitoring and metrics collection for PPDM system health and performance.

#### monitor
Real-time monitoring of PPDM activities with configurable refresh intervals.

**Usage:**
```bash
ppdm-cli monitor
ppdm-cli monitor --category BACKUP --running-only
ppdm-cli monitor --interval 5 --count 20
```

**Features:**
- Configurable refresh interval
- Activity count control
- Category and type filtering
- Dashboard mode
- Interactive console mode

**Documentation:** [monitor.md](tools/monitor.md)

#### monitoring-metrics
Comprehensive system metrics and health status monitoring.

**Usage:**
```bash
ppdm-cli monitoring-metrics activity-metrics
ppdm-cli monitoring-metrics alert-metrics
ppdm-cli monitoring-metrics system-health-metrics
```

**Features:**
- Activity metrics (V3)
- Alert metrics
- Resource utilization metrics
- SLA compliance metrics
- Storage system metrics
- System health metrics

**Documentation:** [monitoring-metrics.md](tools/monitoring-metrics.md)

#### health
PPDM system health monitoring with health check triggering and result viewing.

**Usage:**
```bash
ppdm-cli health list-types
ppdm-cli health trigger
ppdm-cli health entities
ppdm-cli health results
```

**Features:**
- Health check types (V2)
- Health check triggering
- Health check results
- Health entities (V3)
- Health events (V3)

**Documentation:** [health.md](tools/health.md)

#### web-monitor
Web-based activity monitoring interface.

**Usage:**
```bash
ppdm-cli web-monitor
ppdm-cli web-monitor --port 8080
```

**Features:**
- Web-based interface
- Real-time activity dashboard
- Remote access
- Visual monitoring

**Documentation:** [WEB_MONITOR.md](commands/WEB_MONITOR.md)

### AI Tools

AI-powered interfaces for natural language command generation and intelligent assistance.

#### ai
AI-powered command interface using OpenAI and Ollama LLMs.

**Usage:**
```bash
ppdm-cli ai "show me all policies with 0 assets"
ppdm-cli ai --provider openai "list storage systems"
```

**Features:**
- Natural language to CLI command translation
- Safety validation for destructive operations
- Multi-provider support (OpenAI, Ollama)
- Command explanation
- Dry-run mode

**Documentation:** [ai.md](tools/ai.md)

## Tool Comparison

| Tool | Type | Interface | Use Case |
|------|------|-----------|----------|
| **activities-browser** | Browser | TUI | Interactive job group navigation |
| **backup-browser** | Browser | TUI | Interactive asset selection for backups |
| **copies-browser** | Browser | TUI/CLI | Interactive backup copy exploration |
| **monitor** | Monitoring | CLI | Real-time activity monitoring |
| **monitoring-metrics** | Monitoring | CLI | System metrics and health status |
| **health** | Monitoring | CLI | System health checks and monitoring |
| **web-monitor** | Monitoring | Web | Web-based activity monitoring |
| **ai** | AI | CLI | Natural language command generation |

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
ppdm-cli web-monitor
```

### AI Tools
```bash
ppdm-cli ai "show me all assets"
ppdm-cli ai --provider openai "list policies"
```

## Tool-Specific Configuration

### AI Tool Configuration

#### OpenAI Setup
```bash
# Set API key
export OPENAI_API_KEY="your-api-key"

# Or use flag
ppdm-cli ai "list assets" --api-key "your-api-key"
```

#### Ollama Setup
```bash
# Install Ollama
# Visit https://ollama.ai/ for installation instructions

# Pull a model
ollama pull mistral
ollama pull codellama:7b

# Start Ollama server
ollama serve
```

### Web Monitor Configuration

```bash
# Start web monitor on custom port
ppdm-cli web-monitor --port 8080

# Start with specific refresh interval
ppdm-cli web-monitor --refresh-interval 5
```

## Tips and Best Practices

### 1. Use Interactive Tools for Exploration
- **activities-browser** for exploring job groups
- **backup-browser** for selecting assets
- **copies-browser** for analyzing backup copies

### 2. Use Monitoring Tools for Operations
- **monitor** for real-time activity tracking
- **monitoring-metrics** for system health
- **health** for health checks

### 3. Use AI Tool for Learning
- **ai** for learning command syntax
- **ai** for generating complex queries
- **ai** for command explanation

### 4. Combine Tools for Complete Workflow
```bash
# Example: Complete backup workflow
# 1. Select assets with backup-browser
ppdm-cli backup-browser

# 2. Monitor with monitor
ppdm-cli monitor --category BACKUP

# 3. Analyze with copies-browser
ppdm-cli copies-browser

# 4. Check health
ppdm-cli health trigger
```

## Troubleshooting

### Browser Tools

#### TUI Not Starting
```bash
# Force TUI mode
ppdm-cli backup-browser --force-tui

# Check terminal compatibility
echo $TERM

# Use CLI mode instead
ppdm-cli copies-browser --force-cli
```

#### Data Not Loading
```bash
# Check PPDM connection
ppdm-cli config test

# Use debug mode
ppdm-cli --debug activities-browser
```

### Monitoring Tools

#### Monitor Not Updating
```bash
# Check refresh interval
ppdm-cli monitor --interval 5

# Check PPDM connection
ppdm-cli status

# Use debug mode
ppdm-cli --debug monitor
```

#### Metrics Not Available
```bash
# Check if metrics exist
ppdm-cli monitoring-metrics activity-metrics

# Try without filters
ppdm-cli monitoring-metrics activity-metrics

# Use debug mode
ppdm-cli --debug monitoring-metrics activity-metrics
```

### AI Tool

#### API Key Issues
```bash
# Set API key
export OPENAI_API_KEY="your-api-key"

# Verify key is set
echo $OPENAI_API_KEY

# Use flag instead
ppdm-cli ai "list assets" --api-key "your-api-key"
```

#### Ollama Connection Issues
```bash
# Check if Ollama is running
curl http://localhost:11434/api/tags

# Start Ollama server
ollama serve

# Check Ollama URL
ppdm-cli ai "list assets" --provider ollama --ollama-url "http://localhost:11434"
```

## Related Documentation

- [Command Reference](../COMMAND_REFERENCE.md) - Complete command documentation
- [Activities Command](commands/ACTIVITIES.md) - Activities command documentation
- [Backup Command](commands/BACKUP.md) - Backup command documentation
- [Assets Command](commands/ASSETS.md) - Assets command documentation
- [Protection Policies Command](commands/PROTECTION_POLICIES.md) - Protection policies documentation

---

*For more information about other commands, see the [main command reference](../COMMAND_REFERENCE.md).*
