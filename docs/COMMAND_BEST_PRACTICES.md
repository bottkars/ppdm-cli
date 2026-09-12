# PPDM CLI Command Best Practices Guide

## Overview

This guide establishes comprehensive best practices for PPDM CLI command development, ensuring consistency, usability, and maintainability across all commands.

## Command Structure Standards

### Command Naming Conventions

- **Use kebab-case for command names**: `ppdm-cli assets show` (not `assets_show`)
- **Use descriptive, action-oriented names**: `list`, `show`, `create`, `delete`, `update`
- **Group related commands**: `backup server-dr create` (not `backup-create-server-dr`)
- **Use consistent verb-noun patterns**: `assets list`, `assets show`, `assets search`

### Command Organization

- **Follow hierarchical structure**: Main command → subcommand → action
- **Logical grouping**: Related commands under same parent
- **Consistent depth**: Avoid excessive nesting (max 3 levels recommended)

## Flag Naming Conventions

### General Rules

- **Use kebab-case for flag names**: `--asset-id` (not `--assetId` or `--asset_id`)
- **Use clear, descriptive names**: `--output` (not `--o` alone)
- **Provide short options for common flags**: `-o, --output`
- **Use consistent abbreviations**: `-h, --help`, `-v, --version`

### Flag Categories

#### Common Flags (Global)
- `--debug` - Enable debug/trace mode
- `--dry-run` - Show API calls without executing
- `--help` / `-h` - Help information
- `--ppdm-server` - Select specific PPDM server
- `--quiet` - Suppress log messages
- `--show-api-calls` - Show redacted API requests

#### Output Flags
- `--output` / `-o` - Output format (table, json, yaml, csv, json-raw, yaml-raw)
- `--log-level` - Log level (error, warn, info, debug)

#### Filtering Flags
- `--filter` / `-f` - Filter expression
- `--orderby` - Order by field
- `--page` - Page number
- `--page-size` / `-s` - Page size

#### Resource Identification Flags
- `--id` - Resource ID (for show/get operations)
- `--name` - Resource name
- `--asset-id` - Asset ID
- `--policy-id` - Policy ID

## Positional Argument Guidelines

### When to Use Positional Arguments

**Use positional arguments for:**
- **Show/get commands**: `ppdm-cli assets show <asset-id>`
- **Delete operations**: `ppdm-cli assets delete <asset-id>`
- **Primary resource identifiers**: The main resource being operated on

**Don't use positional arguments for:**
- **Optional parameters**: Keep as flags
- **Configuration options**: Use flags for clarity
- **Multiple required parameters**: Use flags for readability

### Implementation Pattern

```go
cmd := &cobra.Command{
    Use:   "show [resource-id]",
    Short: "Show resource details",
    RunE: func(cmd *cobra.Command, args []string) error {
        var resourceID string
        if len(args) > 0 {
            resourceID = args[0]
        } else {
            resourceID, _ = cmd.Flags().GetString("id")
        }
        if resourceID == "" {
            return fmt.Errorf("resource ID is required (provide as argument or --id flag)")
        }
        // ... rest of implementation
    },
}

cmd.Flags().StringVar(&resourceID, "id", "", "Resource ID (optional if provided as argument)")
```

## Help Text Standards

### Command Help Structure

```go
cmd := &cobra.Command{
    Use:   "show [resource-id]",
    Short: "Show resource details (API: getOperation)",
    Long: `Show resource details using the getOperation API operation.

This command implements the PPDM API operation:
- **Operation ID**: getOperation
- **API Version**: v2
- **Endpoint**: GET /api/v2/resources/{id}
- **API Reference**: ppdm-public-v2-20.1.0.0.yaml

The getOperation operation retrieves detailed information about a specific resource
including its configuration, status, and associated data.

Examples:
  ppdm-cli resources show <resource-id>
  ppdm-cli resources show --id <resource-id>
  ppdm-cli resources show <resource-id> --output json`,
}
```

### Help Text Requirements

1. **Short description**: Concise, action-oriented (max 50 chars)
2. **Long description**: Include API operation details
3. **API reference**: Operation ID, version, endpoint, reference file
4. **Examples**: Show both positional and flag usage
5. **Output formats**: Mention available output formats

## Error Handling Patterns

### User-Friendly Error Messages

```go
// Good: Clear, actionable error
if resourceID == "" {
    return fmt.Errorf("resource ID is required (provide as argument or --id flag)")
}

// Bad: Cryptic error
if resourceID == "" {
    return errors.New("invalid input")
}
```

### Error Message Standards

- **Be specific**: Tell user exactly what's wrong
- **Be actionable**: Tell user how to fix it
- **Be consistent**: Use similar wording across commands
- **Include context**: Mention the command and parameter

## Output Format Standards

### Supported Formats

All commands should support:
- **table** - Human-readable table format (default)
- **json** - Formatted JSON output
- **yaml** - YAML format
- **csv** - CSV format for data export
- **json-raw** - Raw API response (for automation)
- **yaml-raw** - Raw API response in YAML

### Output Format Implementation

```go
cmd.Flags().StringVarP(&output, "output", "o", "table", "Output format (table, json, yaml, csv, json-raw, yaml-raw)")
```

## Filter Expression Guidelines

### OData Syntax Support

All list commands should support OData filter expressions:
- **Equality**: `name eq "value"`
- **Contains**: `name co "substring"`
- **Comparison**: `size gt 1024`, `startTime ge "2024-01-01T00:00:00Z"`
- **Logical operators**: `and`, `or`, `not`
- **Complex filters**: `status eq "RUNNING" and type eq "BACKUP"`

### Filter Examples in Help

```bash
# Basic filtering
ppdm-cli resources list --filter 'status eq "ACTIVE"'

# Complex filtering
ppdm-cli resources list --filter 'status eq "RUNNING" and type eq "BACKUP"'

# Time-based filtering
ppdm-cli resources list --filter 'startTime ge "2024-01-01T00:00:00Z"'
```

## Tab Completion Requirements

### Dynamic Tab Completion

Implement dynamic tab completion for:
- **Resource IDs**: Fetch actual IDs from PPDM server
- **Output formats**: Available output options
- **Filter values**: Common filter values
- **Policy names**: Fetch actual policy names

### Tab Completion Implementation

```go
cmd.RegisterFlagCompletionFunc("id", func(cmd *cobra.Command, args []string, toComplete string) ([]string, cobra.ShellCompDirective) {
    ppdmClient, err := CreateMultiHostClient(cmd)
    if err != nil {
        return nil, cobra.ShellCompDirectiveNoFileComp
    }
    
    // Fetch actual resource IDs from PPDM server
    resources, err := ppdmClient.GetResources()
    if err != nil {
        return nil, cobra.ShellCompDirectiveNoFileComp
    }
    
    var ids []string
    for _, resource := range resources {
        ids = append(ids, resource.ID)
    }
    return ids, cobra.ShellCompDirectiveNoFileComp
})
```

## Multi-Host Support Patterns

### PPDM Server Flag

All commands should support the `--ppdm-server` flag for multi-host environments:
- **Global flag**: Available on all commands
- **Configuration-based**: Uses configured servers
- **Override capability**: Can override default server

### Multi-Host Implementation

```go
ppdmClient, err := CreateMultiHostClient(cmd)
if err != nil {
    return fmt.Errorf("failed to create client: %w", err)
}
```

## API Documentation Requirements

### Required API Information

Each command must document:
- **Operation ID**: The API operation being called
- **API Version**: v2 or v3
- **Endpoint**: The API endpoint path
- **API Reference**: The OpenAPI specification file

### API Documentation Format

```go
Long: `Show resource details using the getOperation API operation.

This command implements the PPDM API operation:
- **Operation ID**: getOperation
- **API Version**: v2
- **Endpoint**: GET /api/v2/resources/{id}
- **API Reference**: ppdm-public-v2-20.1.0.0.yaml`
```

## Testing Requirements

### Unit Tests

- **Command structure tests**: Verify command creation and flags
- **Flag validation tests**: Test flag parsing and validation
- **Error handling tests**: Test error conditions and messages

### Integration Tests

- **API integration tests**: Test against real PPDM server
- **Multi-host tests**: Test with different PPDM servers
- **End-to-end tests**: Test complete command workflows

### Test Naming Conventions

```go
func TestNewCommandName(t *testing.T) {
    // Test command creation
}

func TestCommandNameValidation(t *testing.T) {
    // Test validation logic
}

func TestCommandNameIntegration(t *testing.T) {
    // Test integration with PPDM API
}
```

## Common Patterns and Anti-Patterns

### Good Patterns

✅ **Consistent flag naming**: `--asset-id`, `--policy-id`  
✅ **Clear error messages**: "resource ID is required (provide as argument or --id flag)"  
✅ **Comprehensive help text**: Includes API details and examples  
✅ **Positional arguments for show commands**: `ppdm-cli assets show <asset-id>`  
✅ **Dynamic tab completion**: Fetches actual values from PPDM server  
✅ **Multi-host support**: Works with `--ppdm-server` flag  

### Anti-Patterns

❌ **Inconsistent naming**: `--assetId` vs `--asset-id`  
❌ **Cryptic errors**: "invalid input" vs "resource ID is required"  
❌ **Missing API documentation**: No operation ID or endpoint  
❌ **No examples**: Help text without usage examples  
❌ **Hard-coded values**: Tab completion with static lists  
❌ **Single-host only**: Commands that don't support multi-host  

## Documentation Standards

### Command Reference Format

Follow the activities command reference format:
- **Overview section**: Command purpose and capabilities
- **Available Commands**: List of subcommands with descriptions
- **API Endpoints & Operation IDs**: Table format with command, endpoint, method, operation ID, API version
- **Examples**: Comprehensive usage examples
- **Output Formats**: Available output formats with examples
- **Filter Expressions**: OData syntax guide and examples

### Documentation Structure

```markdown
# Command Name Command Reference

## Overview
Brief description of command group

## Available Commands
- `subcommand` - Description (API: operationId)

## API Endpoints & Operation IDs
| Command | API Endpoint | Method | Operation ID | API Version |
|----------|-------------|--------|--------------|-------------|
| `command subcommand` | `/api/v2/endpoint` | GET | operationId | v2 |

## Examples
# Basic usage
ppdm-cli command subcommand

# Advanced usage
ppdm-cli command subcommand --filter 'status eq "RUNNING"'

## Output Formats
# Table format (default)
ppdm-cli command subcommand

# JSON format
ppdm-cli command subcommand --output json
```

## Validation Checklist

Use this checklist when creating or modifying commands:

### Command Structure
- [ ] Command uses kebab-case naming
- [ ] Command follows hierarchical structure
- [ ] Subcommands are logically grouped
- [ ] Command depth is reasonable (max 3 levels)

### Flag Implementation
- [ ] Flags use kebab-case naming
- [ ] Common flags have short options (-h, -o, etc.)
- [ ] Flag names are descriptive and clear
- [ ] Required flags are properly marked
- [ ] Flag descriptions are helpful

### Positional Arguments
- [ ] Show commands support positional arguments
- [ ] Positional arguments are optional (flag alternative available)
- [ ] Validation logic handles both positional and flag usage
- [ ] Error messages are clear for missing arguments

### Help Text
- [ ] Short description is concise (max 50 chars)
- [ ] Long description includes API operation details
- [ ] API reference includes operation ID, version, endpoint
- [ ] Examples show both positional and flag usage
- [ ] Help text mentions available output formats

### Error Handling
- [ ] Error messages are specific and actionable
- [ ] Error messages follow consistent wording
- [ ] Error messages include context (command, parameter)
- [ ] Error handling doesn't expose sensitive information

### Output Formats
- [ ] Command supports all 6 output formats
- [ ] Default output is table format
- [ ] JSON-raw provides raw API response
- [ ] Output format flag is `-o, --output`

### Filter Support
- [ ] List commands support OData filter expressions
- [ ] Help text includes filter examples
- [ ] Filter syntax is documented
- [ ] Complex filters are supported (and, or, not)

### Tab Completion
- [ ] Resource IDs have dynamic tab completion
- [ ] Output formats have static completion
- [ ] Policy names have dynamic completion
- [ ] Tab completion handles errors gracefully

### Multi-Host Support
- [ ] Command supports `--ppdm-server` flag
- [ ] Command works with multi-host configuration
- [ ] Help text mentions multi-host capability
- [ ] Examples show multi-host usage

### API Documentation
- [ ] Operation ID is documented
- [ ] API version is documented
- [ ] API endpoint is documented
- [ ] API reference file is mentioned

### Testing
- [ ] Unit tests for command structure
- [ ] Unit tests for flag validation
- [ ] Integration tests for API calls
- [ ] Tests follow naming conventions

## Future Considerations

### Planned Enhancements

- **Automated validation**: Script to validate commands against best practices
- **Documentation generation**: Auto-generate command reference from code
- **Testing framework**: Standardized testing patterns and utilities
- **Linting rules**: Custom linting rules for command compliance

### Continuous Improvement

- **Regular audits**: Periodic review of commands for compliance
- **User feedback**: Incorporate user feedback into best practices
- **API evolution**: Update best practices as PPDM API evolves
- **Community standards**: Align with broader CLI best practices

---

*This guide should be updated regularly as the PPDM CLI evolves and new best practices emerge.*