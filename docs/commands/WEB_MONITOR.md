# PPDM Web Monitor

A modern web-based interface for monitoring Dell PowerProtect Data Manager activities in real-time.

## Features

### 🎯 Core Functionality
- **Real-time Monitoring**: Live activity updates via WebSocket
- **Adjustable Settings**: Configure refresh interval, activity count, filters
- **Category Filtering**: Filter by any of the 22 PPDM activity categories
- **Type Filtering**: Filter by TASK, JOB, or JOB_GROUP activity types
- **Search Functionality**: Search activities by description or asset name
- **Status Filtering**: Show only running activities or all activities
- **Responsive Design**: Works on desktop, tablet, and mobile devices

### 🎨 UI Design
- **PPDM-Inspired Theme**: Dark theme matching PPDM's professional aesthetic
- **Modern Interface**: Clean, intuitive design with smooth animations
- **Real-time Updates**: Live connection status and activity feed
- **Interactive Controls**: Easy-to-use settings panel with instant updates
- **Status Dashboard**: Overview of activity statistics and system status

### 🚀 Technical Features
- **WebSocket Communication**: Efficient real-time data streaming
- **RESTful API**: Configuration management via HTTP API
- **Gin Web Framework**: High-performance HTTP server
- **Responsive Layout**: CSS Grid and Flexbox for adaptive design
- **Error Handling**: Robust connection management with auto-reconnect

## Usage

### Starting the Web Monitor

```bash
# Start with default settings (localhost:8080)
ppdm-cli web-monitor

# Start on custom port and host
ppdm-cli web-monitor --port 9090 --host 0.0.0.0

# Start with default filter settings
ppdm-cli web-monitor --interval 10 --activities 20 --category "BACKUP"

# Start with running activities only
ppdm-cli web-monitor --running-only --interval 5
```

### Command Line Options

| Flag | Description | Default |
|------|-------------|---------|
| `--host` | Host to bind the web server | `localhost` |
| `--port` | Port for the web server | `8080` |
| `--interval` | Default refresh interval (seconds) | `10` |
| `--activities` | Default number of activities to display | `20` |
| `--search` | Default search term | `""` |
| `--type` | Default activity type filter | `""` |
| `--category` | Default activity category filter | `""` |
| `--running-only` | Show only running activities by default | `false` |

### Web Interface Controls

#### Monitor Settings Panel
- **Refresh Interval**: 5-300 seconds
- **Number of Activities**: 5-100 activities
- **Search Term**: Filter activities by text
- **Activity Type**: All, TASK, JOB, JOB_GROUP
- **Activity Category**: All 22 PPDM categories
- **Running Only**: Show only running activities

#### Activity Categories Available
```
ARCHIVE, BACKUP, CLOUD_PROTECT, CLOUD_TIER, CONFIG, DELETE,
DISASTER_RECOVERY, DISCOVERY, EXPORT, MANAGE, MIGRATE,
PUSH_UPDATE, REPLICATE, RESTORE, SYSTEM, VALIDATE, INDEX,
GARBAGE_COLLECTION, DATA_MOVEMENT, ANOMALY_DETECTION,
SECURITY, UPDATE, QUICK_RECOVERY
```

#### Status Dashboard
- **Total Activities**: Current activity count
- **Running**: Currently running activities
- **Success**: Completed successfully
- **Failed**: Failed activities
- **v2 API**: API v2 status
- **v3 API**: API v3 status

## Architecture

### Components

#### 1. Web Server (`pkg/web/monitor.go`)
- **Gin Router**: HTTP request handling
- **WebSocket Hub**: Connection management
- **Activity Monitor**: Data fetching and broadcasting
- **Configuration API**: Settings management

#### 2. Frontend (`web/templates/index.html`)
- **Responsive Design**: Mobile-friendly layout
- **WebSocket Client**: Real-time data reception
- **Dynamic UI**: Interactive controls and updates
- **PPDM Styling**: Professional dark theme

#### 3. API Integration
- **PPDM Client**: Authentication and API calls
- **Activity Filtering**: OData query building
- **Real-time Updates**: WebSocket data streaming
- **Error Handling**: Robust error management

### Data Flow

```
PPDM API → PPDM Client → Activity Monitor → WebSocket Hub → Frontend
     ↑                                                        ↓
   Configuration API ← Settings Panel ← User Interface ← WebSocket Client
```

## API Documentation

### WebSocket Endpoints

#### `GET /ws`
WebSocket endpoint for real-time activity updates.

**Message Types:**
- `activities`: Activity data with system status
- `config`: Configuration updates

**Activity Data Structure:**
```json
{
  "type": "activities",
  "timestamp": "2026-04-01T10:00:00Z",
  "data": [...],
  "config": {...},
  "systemStatus": {
    "v2_api": "available",
    "v3_api": "available"
  }
}
```

### REST API Endpoints

#### `GET /api/config`
Returns current monitor configuration.

#### `POST /api/config`
Updates monitor configuration.

**Request Body:**
```json
{
  "interval": 10,
  "activityCount": 20,
  "searchTerm": "",
  "activityType": "",
  "category": "BACKUP",
  "runningOnly": false
}
```

## Development

### Building

```bash
# Build with web monitor support
go build -o ppdm-cli cmd/ppdm-cli/main.go

# Or use the Homebrew Go installation
/home/linuxbrew/.linuxbrew/bin/go build -o ppdm-cli cmd/ppdm-cli/main.go
```

### Running

```bash
# Start the web monitor
./ppdm-cli web-monitor

# Open in browser
open http://localhost:8080
```

### Dependencies

The web monitor requires these additional dependencies:
- `github.com/gin-gonic/gin` - Web framework
- `github.com/gorilla/websocket` - WebSocket support

## Security Considerations

### Network Security
- **Local Binding**: Default binds to localhost only
- **CORS Settings**: Configurable for development
- **Authentication**: Uses existing PPDM client authentication

### Data Security
- **No Credentials Storage**: Web UI doesn't store PPDM credentials
- **Session Management**: WebSocket connections are stateless
- **Input Validation**: All user inputs are validated

## Troubleshooting

### Common Issues

#### WebSocket Connection Failed
```bash
# Check if port is available
netstat -an | grep 8080

# Try different port
ppdm-cli web-monitor --port 8081
```

#### Activities Not Loading
```bash
# Check PPDM connection
ppdm-cli activities list --limit 5

# Verify authentication
ppdm-cli status
```

#### Performance Issues
```bash
# Reduce activity count
ppdm-cli web-monitor --activities 10

# Increase refresh interval
ppdm-cli web-monitor --interval 30
```

### Debug Mode

Enable debug logging for troubleshooting:

```bash
ppdm-cli --debug web-monitor
```

## Future Enhancements

### Planned Features
- **Historical Data**: Activity history and trends
- **Alert System**: Configurable activity alerts
- **Export Functionality**: Export activity data
- **Multi-Server Support**: Monitor multiple PPDM instances
- **User Authentication**: Web-based user management
- **Custom Dashboards**: Configurable dashboard layouts

### Performance Optimizations
- **Data Caching**: Reduce API calls
- **Lazy Loading**: Load activities on demand
- **Compression**: WebSocket message compression
- **Connection Pooling**: Optimize resource usage

## Contributing

### Code Structure
```
pkg/web/
├── monitor.go          # Main web monitor implementation
└── README.md           # This documentation

web/
├── templates/
│   └── index.html      # Main HTML template
├── static/
│   ├── css/           # Stylesheets (future)
│   ├── js/            # JavaScript files (future)
│   └── images/        # Images and icons (future)
└── README.md          # Web UI documentation
```

### Development Guidelines
- Follow existing code style and patterns
- Add comprehensive error handling
- Include unit tests for new features
- Update documentation for API changes
- Test responsive design on multiple devices

## License

This feature is part of the PPDM CLI project and follows the same license terms.
