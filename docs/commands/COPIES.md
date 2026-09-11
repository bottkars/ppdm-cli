# Copies Command Documentation

## Overview

The `copies` command group provides comprehensive management of PPDM backup copies with advanced filtering, analysis, and transaction management capabilities.

## Available Commands

### Core Copy Commands

#### `copies list`
List backup copies with enhanced filtering and analysis capabilities.

```bash
# List all copies
ppdm-cli copies list

# List with state filtering
ppdm-cli copies list --state INUSE

# List with date range
ppdm-cli copies list --start-date "2024-01-01" --end-date "2024-12-31"

# List with asset type filtering
ppdm-cli copies list --asset-type VMWARE_VIRTUAL_MACHINE

# List with output format
ppdm-cli copies list --output json
```

#### `copies show`
Display detailed information about a specific copy.

```bash
# Show copy details
ppdm-cli copies show --id "copy-id"

# Show with JSON output
ppdm-cli copies show --id "copy-id" --output json
```

#### `copies delete`
Delete backup copies with confirmation and safety checks.

```bash
# Delete a copy
ppdm-cli copies delete --id "copy-id"

# Force delete without confirmation
ppdm-cli copies delete --id "copy-id" --force
```

### Analysis Commands

#### `copies analysis`
Perform comprehensive analysis of copy data including size distribution, retention, and performance metrics.

```bash
# Analyze all copies
ppdm-cli copies analysis

# Analyze specific asset type
ppdm-cli copies analysis --asset-type VMWARE_VIRTUAL_MACHINE
```

#### `copies anomaly`
Detect and analyze anomalies in copy data such as unusual size patterns, timing issues, or consistency problems.

```bash
# Detect anomalies
ppdm-cli copies anomaly

# Anomaly with detailed output
ppdm-cli copies anomaly --output json
```

#### `copies integrity`
Check data integrity of copies and identify potential corruption or consistency issues.

```bash
# Check integrity
ppdm-cli copies integrity

# Integrity check with detailed output
ppdm-cli copies integrity --output json
```

### Copy Transaction Commands

#### `copies transactions list`
List all transactions for a specific copy. Transactions track operations on copies such as create, restore, export, archive, and other operations that can keep copies in INUSE state.

```bash
# List transactions for a copy
ppdm-cli copies transactions list --copy-id "copy-id"

# List with JSON output
ppdm-cli copies transactions list --copy-id "copy-id" --output json

# List with raw JSON for automation
ppdm-cli copies transactions list --copy-id "copy-id" --output json-raw
```

**Transaction States:**
- Active transactions have no end state and ended time shows "N/A"
- Completed transactions show end state (SUCCEEDED, FAILED, EXPIRED)
- Transactions can keep copies in INUSE state until properly ended

#### `copies transactions show`
Show detailed information about a specific transaction.

```bash
# Show transaction details
ppdm-cli copies transactions show --id "transaction-id"

# Show with JSON output
ppdm-cli copies transactions show --id "transaction-id" --output json
```

**Note:** Direct transaction lookup is limited by API design. Use `copies transactions list --copy-id <id>` to find transactions for a specific copy.

#### `copies transactions end`
End a copy transaction to free the copy from INUSE state. This is useful when copies are stuck in INUSE state due to failed or abandoned operations.

```bash
# End transaction with default SUCCEEDED state
ppdm-cli copies transactions end --id "transaction-id"

# End transaction with specific state
ppdm-cli copies transactions end --id "transaction-id" --state SUCCEEDED
ppdm-cli copies transactions end --id "transaction-id" --state FAILED
ppdm-cli copies transactions end --id "transaction-id" --state EXPIRED

# End with JSON output
ppdm-cli copies transactions end --id "transaction-id" --output json
```

**Transaction End States:**
- `SUCCEEDED` - Transaction completed successfully
- `FAILED` - Transaction failed
- `EXPIRED` - Transaction expired

**Use Case Example:**
```bash
# 1. Find stuck INUSE copies
ppdm-cli copies list --state INUSE

# 2. List transactions for the stuck copy
ppdm-cli copies transactions list --copy-id "stuck-copy-id"

# 3. End active transactions
ppdm-cli copies transactions end --id "active-transaction-id" --state SUCCEEDED

# 4. Verify copy state changed to IDLE
ppdm-cli copies show --id "stuck-copy-id"
```

### Aggregate Commands

#### `copies aggregate`
Perform aggregation operations on copy data for analysis and reporting.

```bash
# Aggregate by asset type
ppdm-cli copies aggregate --field assetType

# Aggregate by state
ppdm-cli copies aggregate --field state
```

## Copy States

Understanding copy states is crucial for effective copy management:

| State | Description | Transaction Impact |
|-------|-------------|---------------------|
| `IDLE` | Copy is available for operations | No active transactions |
| `INUSE` | Copy is being used by an operation | Active transaction present |
| `DELETING` | Copy is being deleted | Deletion transaction active |
| `RESTORING` | Copy is being used for restore | Restore transaction active |
| `ARCHIVING` | Copy is being archived | Archive transaction active |

## Transaction Types

Common transaction types that can affect copy state:

| Transaction Type | Description | Typical State Impact |
|------------------|-------------|---------------------|
| `CREATE` | Copy creation operation | INUSE during creation |
| `RESTORE` | Copy restore operation | INUSE during restore |
| `EXPORT` | Copy export operation | INUSE during export |
| `ARCHIVE` | Copy archive operation | INUSE during archive |
| `MARK_FOR_ARCHIVING` | Copy marked for archiving | INUSE until ended |
| `REPLICATE` | Copy replication operation | INUSE during replication |
| `TIER` | Copy cloud tiering operation | INUSE during tiering |
| `RECALL` | Copy recall from cloud | INUSE during recall |
| `DELETE` | Copy deletion operation | INUSE during deletion |
| `BROWSE` | Copy browse operation | INUSE during browse |

## Troubleshooting

### Copy Stuck in INUSE State

When a copy remains in INUSE state unexpectedly:

1. **Check active transactions:**
   ```bash
   ppdm-cli copies transactions list --copy-id "stuck-copy-id"
   ```

2. **Identify the stuck transaction:**
   - Look for transactions with no end state
   - Check transaction type and start time
   - Verify if the operation is still relevant

3. **End the transaction:**
   ```bash
   ppdm-cli copies transactions end --id "transaction-id" --state SUCCEEDED
   ```

4. **Verify state change:**
   ```bash
   ppdm-cli copies show --id "stuck-copy-id"
   ```

### Transaction End State Selection

Choose the appropriate end state based on the situation:

- **SUCCEEDED**: Use when the operation completed successfully but the transaction wasn't properly ended
- **FAILED**: Use when the operation failed and you want to mark it as failed
- **EXPIRED**: Use when the operation timed out or expired

### Common Scenarios

#### Scenario 1: Failed Archive Operation
```bash
# Copy stuck in INUSE after failed archive
ppdm-cli copies transactions list --copy-id "copy-id"
# Shows: MARK_FOR_ARCHIVING transaction with no end state

# End the failed transaction
ppdm-cli copies transactions end --id "transaction-id" --state FAILED

# Verify copy is now IDLE
ppdm-cli copies show --id "copy-id"
```

#### Scenario 2: Abandoned Restore Operation
```bash
# Copy stuck in INUSE after abandoned restore
ppdm-cli copies transactions list --copy-id "copy-id"
# Shows: RESTORE transaction with no end state

# End the abandoned transaction
ppdm-cli copies transactions end --id "transaction-id" --state FAILED

# Verify copy is now IDLE
ppdm-cli copies show --id "copy-id"
```

#### Scenario 3: Successful Operation with Stuck Transaction
```bash
# Copy stuck in INUSE after successful operation
ppdm-cli copies transactions list --copy-id "copy-id"
# Shows: Any transaction type with no end state

# End the transaction as succeeded
ppdm-cli copies transactions end --id "transaction-id" --state SUCCEEDED

# Verify copy is now IDLE
ppdm-cli copies show --id "copy-id"
```

## Best Practices

### 1. Monitor Copy States Regularly
```bash
# Check for stuck copies
ppdm-cli copies list --state INUSE

# Check for copies with long-running transactions
ppdm-cli copies transactions list --copy-id "copy-id" | grep "N/A"
```

### 2. Use Transaction Management Proactively
```bash
# Before performing operations, check for existing transactions
ppdm-cli copies transactions list --copy-id "copy-id"

# After operations, verify transaction completion
ppdm-cli copies transactions list --copy-id "copy-id"
```

### 3. Document Transaction End Reasons
When ending transactions manually, document the reason for future reference:
- Why was the transaction stuck?
- What operation was being performed?
- What was the resolution?

### 4. Use Appropriate End States
Choose the correct end state based on the actual situation:
- Don't mark failed operations as SUCCEEDED
- Don't mark successful operations as FAILED
- Use EXPIRED for timeout situations

### 5. Verify State Changes
Always verify that the copy state changes as expected after ending transactions:
```bash
ppdm-cli copies show --id "copy-id"
```

## Integration Examples

### Bash Script for Stuck Copy Detection
```bash
#!/bin/bash
# Find and fix stuck copies

# Get all INUSE copies
stuck_copies=$(ppdm-cli copies list --state INUSE --output json-raw 2>/dev/null | jq -r '.[].id')

for copy_id in $stuck_copies; do
    echo "Checking copy: $copy_id"
    
    # List transactions
    transactions=$(ppdm-cli copies transactions list --copy-id "$copy_id" --output json-raw 2>/dev/null)
    
    # Find active transactions (no end state)
    active_tx=$(echo "$transactions" | jq -r '.[] | select(.endState == null) | .id')
    
    for tx_id in $active_tx; do
        echo "Ending transaction: $tx_id"
        ppdm-cli copies transactions end --id "$tx_id" --state SUCCEEDED
    done
    
    # Verify state change
    state=$(ppdm-cli copies show --id "$copy_id" --output json-raw 2>/dev/null | jq -r '.state')
    echo "Copy state: $state"
done
```

### Python Integration for Transaction Management
```python
import subprocess
import json

def get_stuck_copies():
    """Get all copies in INUSE state"""
    result = subprocess.run([
        'ppdm-cli', 'copies', 'list', 
        '--state', 'INUSE',
        '--output', 'json-raw'
    ], capture_output=True, text=True)
    
    return json.loads(result.stdout)

def get_copy_transactions(copy_id):
    """Get transactions for a specific copy"""
    result = subprocess.run([
        'ppdm-cli', 'copies', 'transactions', 'list',
        '--copy-id', copy_id,
        '--output', 'json-raw'
    ], capture_output=True, text=True)
    
    return json.loads(result.stdout)

def end_transaction(transaction_id, state='SUCCEEDED'):
    """End a transaction"""
    result = subprocess.run([
        'ppdm-cli', 'copies', 'transactions', 'end',
        '--id', transaction_id,
        '--state', state
    ], capture_output=True, text=True)
    
    return result.returncode == 0

# Example usage
stuck_copies = get_stuck_copies()
for copy in stuck_copies:
    transactions = get_copy_transactions(copy['id'])
    for tx in transactions:
        if tx.get('endState') is None:
            print(f"Ending transaction {tx['id']} for copy {copy['id']}")
            end_transaction(tx['id'])
```

## API Reference

### Copy Catalog Service (v3 API)

The copy transaction commands use the copy-catalog service v3 API:

- **Get Copy Metadata**: `GET /api/v3/copy-metadata/{id}`
- **End Copy Transaction**: `POST /api/v3/copy-transactions/{id}/end`

These endpoints are accessible through the main PPDM server (port 8443) as a proxy to the internal copy-catalog service.

## Additional Resources

### Main Documentation
- [Command Reference](../COMMAND_REFERENCE.md) - Complete command reference
- [CRUD Standards](../CRUD_STANDARDS.md) - Command implementation standards
- [Main README](../README.md) - Project overview and setup

### Related Commands
- [Activities](ACTIVITIES.md) - Monitor backup and restore activities
- [Backup and Restore](BACKUP_RESTORE.md) - Backup and restore operations

---

*For the complete command reference, see [COMMAND_REFERENCE.md](../COMMAND_REFERENCE.md).*
