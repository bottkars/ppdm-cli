# Curl Command Reference

## Overview

The `curl` command provides direct API access to any PowerProtect Data Manager endpoint. It enables users to make raw HTTP requests to PPDM APIs with full control over parameters, headers, and request bodies.

## Syntax

```bash
ppdm-cli curl --endpoint <endpoint> --method <method> --apiver <version> [flags]
```

## Required Flags

- `--endpoint string` - API endpoint path (e.g., "activities", "protection-policies")
- `--method string` - HTTP method (GET, POST, PUT, DELETE, PATCH)
- `--apiver string` - API version (e.g., "api/v2", "api/v3")

## Optional Flags

- `--body string` - Request body (JSON string or file path)
- `--body-file string` - Request body from file
- `--filter string` - Filter expression for GET requests
- `--page int` - Page number (default 1)
- `--size int` - Page size (default 20)
- `--orderby string` - Order by field (e.g., "name asc")
- `--headers string` - Additional headers (key:value pairs)
- `--output string` - Output format (table, json, yaml, json-raw, yaml-raw)
- `--timeout duration` - Request timeout (default 30s)
- `-h, --help` - Help for curl command

## Examples

### GET Requests

#### Basic GET Request
```bash
# List activities
ppdm-cli curl --endpoint activities --method GET --apiver api/v2

# List protection policies
ppdm-cli curl --endpoint protection-policies --method GET --apiver api/v2

# List infrastructure objects
ppdm-cli curl --endpoint infrastructure-objects --method GET --apiver api/v3
```

#### GET with Filtering
```bash
# Filter activities by status
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 \
  --filter 'status eq "RUNNING"'

# Filter by multiple conditions
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 \
  --filter 'status eq "COMPLETED" and type eq "BACKUP"'

# Filter by date range
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 \
  --filter 'startTime ge "2024-01-01T00:00:00Z" and startTime le "2024-01-31T23:59:59Z"'
```

#### GET with Pagination
```bash
# Custom page size
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 \
  --page 1 --size 50

# Order results
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 \
  --orderby "startTime desc"

# Combined pagination and filtering
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 \
  --filter 'status eq "RUNNING"' --page 1 --size 20 --orderby "startTime asc"
```

#### GET with Specific Resource
```bash
# Get specific activity
ppdm-cli curl --endpoint activities/12345678-1234-1234-1234-123456789012 --method GET --apiver api/v2

# Get specific protection policy
ppdm-cli curl --endpoint protection-policies/policy-id --method GET --apiver api/v2

# Get specific infrastructure object
ppdm-cli curl --endpoint infrastructure-objects/object-id --method GET --apiver api/v3
```

### POST Requests

#### Create Resource with JSON Body
```bash
# Create protection policy
ppdm-cli curl --endpoint protection-policies --method POST --apiver api/v2 \
  --body '{
    "name": "Test Policy",
    "description": "Test protection policy",
    "assetType": "FILE_SYSTEM",
    "retentionDays": 30
  }'

# Create inventory source
ppdm-cli curl --endpoint inventory-sources --method POST --apiver api/v2 \
  --body '{
    "name": "vCenter Server",
    "type": "VMWARE_VCENTER",
    "addresses": ["vcenter.example.com"],
    "credentialId": "credential-id"
  }'

# Create backup
ppdm-cli curl --endpoint activities --method POST --apiver api/v2 \
  --body '{
    "type": "BACKUP",
    "assetId": "asset-id",
    "policyId": "policy-id",
    "name": "Test Backup"
  }'
```

#### POST with Body from File
```bash
# Create resource from JSON file
ppdm-cli curl --endpoint protection-policies --method POST --apiver api/v2 \
  --body-file policy.json

# Create resource from YAML file (converted to JSON)
ppdm-cli curl --endpoint inventory-sources --method POST --apiver api/v2 \
  --body-file inventory-source.yaml
```

### PUT Requests

#### Update Resource
```bash
# Update protection policy
ppdm-cli curl --endpoint protection-policies/policy-id --method PUT --apiver api/v2 \
  --body '{
    "name": "Updated Policy Name",
    "description": "Updated description"
  }'

# Update inventory source
ppdm-cli curl --endpoint inventory-sources/source-id --method PUT --apiver api/v2 \
  --body '{
    "name": "Updated Source Name",
    "addresses": ["new-address.example.com"]
  }'

# Update host
ppdm-cli curl --endpoint hosts/host-id --method PUT --apiver api/v2 \
  --body '{
    "name": "Updated Host Name"
  }'
```

### PATCH Requests

#### Partial Update
```bash
# Partial update of protection policy
ppdm-cli curl --endpoint protection-policies/policy-id --method PATCH --apiver api/v2 \
  --body '{
    "description": "Updated description only"
  }'

# Enable/disable policy
ppdm-cli curl --endpoint protection-policies/policy-id --method PATCH --apiver api/v2 \
  --body '{
    "isActive": false
  }'
```

### DELETE Requests

#### Delete Resource
```bash
# Delete protection policy
ppdm-cli curl --endpoint protection-policies/policy-id --method DELETE --apiver api/v2

# Delete inventory source
ppdm-cli curl --endpoint inventory-sources/source-id --method DELETE --apiver api/v2

# Cancel activity
ppdm-cli curl --endpoint activities/activity-id --method DELETE --apiver api/v2
```

## Advanced Usage

### Custom Headers
```bash
# Add custom headers
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 \
  --headers "X-Custom-Header:value,Another-Header:value2"

# Content-Type header (automatically set for JSON body)
ppdm-cli curl --endpoint protection-policies --method POST --apiver api/v2 \
  --body '{"name":"Test"}' \
  --headers "Content-Type:application/json"
```

### Output Formats
```bash
# Table format (default for GET)
ppdm-cli curl --endpoint activities --method GET --apiver api/v2

# JSON format
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --output json

# JSON-Raw (raw API response)
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --output json-raw

# YAML format
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --output yaml

# YAML-Raw (raw API response)
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --output yaml-raw
```

### Timeout and Performance
```bash
# Custom timeout
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --timeout 60s

# Large page size for performance
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --page 1 --size 100
```

## Use Cases

### API Exploration
```bash
# Explore available endpoints
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --filter 'top 1'

# Check API version compatibility
ppdm-cli curl --endpoint activities --method GET --apiver api/v3

# Test endpoint existence
ppdm-cli curl --endpoint inventory-sources --method GET --apiver api/v3
```

### Bulk Operations
```bash
# Get all activities (large page size)
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --page 1 --size 1000

# Export all policies
ppdm-cli curl --endpoint protection-policies --method GET --apiver api/v2 --size 500 --output json-raw > policies.json

# Export all infrastructure objects
ppdm-cli curl --endpoint infrastructure-objects --method GET --apiver api/v3 --size 500 --output json-raw > infra_objects.json
```

### Testing and Debugging
```bash
# Test API connectivity
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --filter 'top 1'

# Debug with global debug flag
ppdm-cli --debug curl --endpoint activities --method GET --apiver api/v2

# Test invalid endpoint
ppdm-cli curl --endpoint invalid-endpoint --method GET --apiver api/v2
```

### Custom Scripts
```bash
#!/bin/bash
# API health check script

echo "=== Testing API Connectivity ==="
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --filter 'top 1'

echo "=== Checking System Status ==="
ppdm-cli curl --endpoint status --method GET --apiver api/v2

echo "=== Listing Recent Activities ==="
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 \
  --filter 'startTime ge "$(date -d '1 hour ago' -Iseconds)"' --orderby "startTime desc"
```

### Data Migration
```bash
# Export data from one system
ppdm-cli curl --endpoint protection-policies --method GET --apiver api/v2 --output json-raw > source_policies.json

# Import data to another system
ppdm-cli curl --endpoint protection-policies --method POST --apiver api/v2 --body-file source_policies.json
```

## API Endpoints Reference

### Common Endpoints

| Endpoint | Version | Methods | Description |
|----------|---------|---------|-------------|
| `activities` | api/v2 | GET, POST, DELETE | Activity management |
| `protection-policies` | api/v2 | GET, POST, PUT, DELETE | Protection policies |
| `inventory-sources` | api/v2 | GET, POST, PUT, DELETE | Inventory sources |
| `hosts` | api/v2 | GET, POST, PUT, DELETE | Host management |
| `assets` | api/v2 | GET, POST, PUT, DELETE | Asset management |
| `infrastructure-objects` | api/v3 | GET | Infrastructure objects |
| `storage-systems` | api/v2 | GET, POST, PUT, DELETE | Storage systems |
| `credentials` | api/v2 | GET, POST, PUT, DELETE | Credentials |
| `users` | api/v2 | GET, POST, PUT, DELETE | User management |

### Endpoint Patterns

#### List Resources
```bash
ppdm-cli curl --endpoint <resource-type> --method GET --apiver api/v2
```

#### Get Specific Resource
```bash
ppdm-cli curl --endpoint <resource-type>/<resource-id> --method GET --apiver api/v2
```

#### Create Resource
```bash
ppdm-cli curl --endpoint <resource-type> --method POST --apiver api/v2 --body '{"field":"value"}'
```

#### Update Resource
```bash
ppdm-cli curl --endpoint <resource-type>/<resource-id> --method PUT --apiver api/v2 --body '{"field":"new-value"}'
```

#### Delete Resource
```bash
ppdm-cli curl --endpoint <resource-type>/<resource-id> --method DELETE --apiver api/v2
```

## Tips and Best Practices

### 1. Use JSON-Raw for Automation
```bash
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --output json-raw 2>/dev/null | jq '.content[].id'
```

### 2. Filter at API Level
```bash
# Good: Filter at API level
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --filter 'status eq "RUNNING"'

# Avoid: Get all data then filter
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 | grep RUNNING
```

### 3. Use Appropriate Page Sizes
```bash
# For large datasets
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --page 1 --size 100

# For quick checks
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --filter 'top 5'
```

### 4. Validate JSON Before Sending
```bash
# Validate JSON file
jq '.' policy.json

# Use validated file
ppdm-cli curl --endpoint protection-policies --method POST --apiver api/v2 --body-file policy.json
```

### 5. Use Dry Run for Testing
```bash
# Test endpoint without authentication (if possible)
ppdm-cli --dry-run curl --endpoint activities --method GET --apiver api/v2
```

## Troubleshooting

### Common Issues

#### 1. Endpoint Not Found
```bash
# Check API version
ppdm-cli curl --endpoint activities --method GET --apiver api/v2
ppdm-cli curl --endpoint activities --method GET --apiver api/v3

# Check endpoint path
ppdm-cli curl --endpoint invalid-endpoint --method GET --apiver api/v2
```

#### 2. Authentication Issues
```bash
# Test connection with simple endpoint
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --filter 'top 1'

# Use debug mode
ppdm-cli --debug curl --endpoint activities --method GET --apiver api/v2
```

#### 3. Invalid JSON Body
```bash
# Validate JSON syntax
echo '{"name":"test"}' | jq .

# Use file instead of inline JSON
ppdm-cli curl --endpoint protection-policies --method POST --apiver api/v2 --body-file valid.json
```

#### 4. Filter Syntax Errors
```bash
# Test simple filter first
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --filter 'status eq "RUNNING"'

# Check complex filter syntax
ppdm-cli curl --endpoint activities --method GET --apiver api/v2 --filter 'status eq "COMPLETED" and type eq "BACKUP"'
```

### Debug Mode

Use debug mode for detailed information:
```bash
ppdm-cli --debug curl --endpoint activities --method GET --apiver api/v2
```

This will show:
- HTTP request details
- Headers being sent
- Response headers
- Timing information
- Error details

## Error Handling

### Common HTTP Status Codes

| Status | Meaning | Action |
|--------|---------|--------|
| 200 | Success | Request completed successfully |
| 201 | Created | Resource created successfully |
| 400 | Bad Request | Invalid request body or parameters |
| 401 | Unauthorized | Authentication failed |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Endpoint or resource not found |
| 409 | Conflict | Resource conflict |
| 500 | Server Error | Internal server error |

### Error Response Format
```json
{
  "code": 400,
  "reason": "Invalid request",
  "details": ["Specific error details"],
  "remediation": "How to fix the error",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

---

*For more information about other commands, see the [main command reference](../COMMAND_REFERENCE.md).*
