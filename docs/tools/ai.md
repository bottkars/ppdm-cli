# AI-Powered Command Interface

## Overview

The `ai` command provides an AI-powered command interface using OpenAI and Ollama LLMs for natural language to CLI command translation. It enables users to interact with the PPDM CLI using natural language, with intelligent command generation, safety validation, and context-aware suggestions.

## Features

### Natural Language to CLI Translation
- **Command generation**: Convert natural language to PPDM CLI commands
- **Context awareness**: Understands PPDM CLI context and structure
- **Command explanation**: Explain what CLI commands do
- **Complex queries**: Handle complex natural language queries

### Safety Features
- **Destructive operation detection**: Automatically detects dangerous commands
- **Confidence scoring**: Provides confidence scores for command accuracy
- **Confirmation prompts**: Requires confirmation for dangerous commands
- **Dry-run mode**: Test generated commands without execution

### Multi-Provider Support
- **OpenAI**: Cloud-based GPT models (gpt-3.5-turbo, gpt-4, gpt-4-turbo)
- **Ollama**: Local LLM processing (mistral, codellama:7b, llama2)
- **Privacy**: Local processing with Ollama
- **Offline capability**: Ollama works without internet connection

## Usage

### Basic Command Translation

```bash
# Simple command translation
ppdm-cli ai "show me all policies with 0 assets"

# Get command explanation
ppdm-cli ai "what does this command do: protection-policies list -f \"numberOfAssets eq 0\""

# Generate complex queries
ppdm-cli ai "show me failed backups from last 24 hours"
```

### Safety Validation

```bash
# Destructive operation requires confirmation
ppdm-cli ai "delete all policies with no assets"

# Auto-confirm destructive operations
ppdm-cli ai "delete all policies with no assets" --confirm

# Dry-run mode to test
ppdm-cli ai "delete all policies with no assets" --dry-run
```

### Provider Selection

```bash
# Use OpenAI (default)
ppdm-cli ai "list storage systems" --provider openai

# Use Ollama (default)
ppdm-cli ai "list storage systems" --provider ollama

# Specify model
ppdm-cli ai "create backup policy" --provider openai --model gpt-4
ppdm-cli ai "list storage systems" --provider ollama --model mistral
```

### Show Command Only

```bash
# Show only the generated command without explanation
ppdm-cli ai "show me all assets" --show-command
```

## Flags

| Flag | Description | Default |
|------|-------------|---------|
| `--prompt` | Natural language prompt for AI command generation | Required |
| `--provider` | AI provider (ollama or openai) | ollama |
| `--model` | AI model to use | gpt-3.5-turbo |
| `--api-key` | OpenAI API key (env: OPENAI_API_KEY) | From environment |
| `--openai-url` | OpenAI-compatible API URL (env: OPENAI_URL) | OpenAI default |
| `--ollama-url` | Ollama API URL (env: OLLAMA_URL) | http://localhost:11434 |
| `--confirm` | Auto-confirm destructive operations | False |
| `--dry-run` | Show generated command without executing | False |
| `--show-command` | Show only the generated command | False |

## AI Providers

### OpenAI

**Setup:**
```bash
# Set API key
export OPENAI_API_KEY="your-api-key"

# Or use flag
ppdm-cli ai "list assets" --api-key "your-api-key"
```

**Recommended Models:**
- `gpt-3.5-turbo` - Fast, cost-effective (default)
- `gpt-4` - Most accurate, slower
- `gpt-4-turbo` - Balanced performance

**Examples:**
```bash
# Use GPT-4
ppdm-cli ai "show me all policies" --provider openai --model gpt-4

# Use GPT-3.5-turbo
ppdm-cli ai "list storage systems" --provider openai --model gpt-3.5-turbo
```

### Ollama

**Setup:**
```bash
# Install Ollama
# Visit https://ollama.ai/ for installation instructions

# Pull a model
ollama pull mistral
ollama pull codellama:7b
ollama pull llama2

# Start Ollama server
ollama serve
```

**Recommended Models:**
- `mistral` - Good balance of speed and accuracy
- `codellama:7b` - Optimized for code/command generation
- `llama2` - General purpose

**Examples:**
```bash
# Use Mistral
ppdm-cli ai "show me all assets" --provider ollama --model mistral

# Use CodeLlama
ppdm-cli ai "list storage systems" --provider ollama --model codellama:7b

# Use custom Ollama URL
ppdm-cli ai "list policies" --provider ollama --ollama-url "http://localhost:11434"
```

## Examples

### Command Generation

```bash
# Simple queries
ppdm-cli ai "show me all assets"
ppdm-cli ai "list all protection policies"
ppdm-cli ai "show failed backups"

# Complex queries
ppdm-cli ai "show me failed backups from last 24 hours"
ppdm-cli ai "list all policies with 0 assets"
ppdm-cli ai "show storage systems with less than 50% capacity"
```

### Command Explanation

```bash
# Explain command
ppdm-cli ai "what does this command do: protection-policies list -f \"numberOfAssets eq 0\""

# Explain complex command
ppdm-cli ai "explain: assets list -f 'protectionStatus eq \"PROTECTED\"' --output json"
```

### Destructive Operations

```bash
# Delete operations require confirmation
ppdm-cli ai "delete all policies with no assets"

# Auto-confirm
ppdm-cli ai "delete all policies with no assets" --confirm

# Dry-run first
ppdm-cli ai "delete all policies with no assets" --dry-run
```

### Provider-Specific Usage

```bash
# OpenAI with GPT-4
ppdm-cli ai "show me all assets" --provider openai --model gpt-4

# Ollama with Mistral
ppdm-cli ai "list storage systems" --provider ollama --model mistral

# Ollama with CodeLlama
ppdm-cli ai "create backup policy" --provider ollama --model codellama:7b
```

## Use Cases

### 1. Quick Command Generation

```bash
# Generate command without remembering exact syntax
ppdm-cli ai "show me all assets with protection status PROTECTED"

# Generate complex filter
ppdm-cli ai "list policies created in the last 30 days"
```

### 2. Learning and Education

```bash
# Learn command syntax
ppdm-cli ai "what does this command do: assets list -f 'protectionStatus eq \"PROTECTED\"'"

# Understand filters
ppdm-cli ai "explain how to filter activities by status"
```

### 3. Complex Query Generation

```bash
# Generate complex OData filters
ppdm-cli ai "show me failed backups from last 24 hours with size > 100GB"

# Generate multi-condition filters
ppdm-cli ai "list assets that are protected and have name containing 'database'"
```

### 4. Safety Validation

```bash
# Validate destructive commands
ppdm-cli ai "delete all policies with no assets" --dry-run

# Test before execution
ppdm-cli ai "delete asset asset-123" --dry-run
```

### 5. Automation Scripting

```bash
# Generate commands for scripts
ppdm-cli ai "show me all assets" --show-command > script.sh

# Generate multiple commands
ppdm-cli ai "list all policies and their asset counts" --show-command
```

## Tips and Best Practices

### 1. Use Clear Natural Language
```bash
# Good: Clear and specific
ppdm-cli ai "show me all assets with protection status PROTECTED"

# Avoid: Vague queries
ppdm-cli ai "show assets"
```

### 2. Use Dry-Run for Destructive Operations
```bash
# Always test destructive commands
ppdm-cli ai "delete all policies with no assets" --dry-run

# Review before execution
ppdm-cli ai "delete asset asset-123" --dry-run
```

### 3. Choose Appropriate Provider
```bash
# Use Ollama for privacy and offline
ppdm-cli ai "list assets" --provider ollama

# Use OpenAI for accuracy
ppdm-cli ai "complex query" --provider openai --model gpt-4
```

### 4. Use Appropriate Model
```bash
# Fast queries: gpt-3.5-turbo or mistral
ppdm-cli ai "list assets" --model gpt-3.5-turbo

# Complex queries: gpt-4
ppdm-cli ai "complex analysis" --model gpt-4

# Code generation: codellama:7b
ppdm-cli ai "generate command" --model codellama:7b
```

### 5. Review Generated Commands
```bash
# Show command only for review
ppdm-cli ai "list assets" --show-command

# Review before execution
ppdm-cli ai "delete policy policy-123" --dry-run
```

## Troubleshooting

### OpenAI API Key Issues

```bash
# Set API key
export OPENAI_API_KEY="your-api-key"

# Verify key is set
echo $OPENAI_API_KEY

# Use flag instead
ppdm-cli ai "list assets" --api-key "your-api-key"
```

### Ollama Connection Issues

```bash
# Check if Ollama is running
curl http://localhost:11434/api/tags

# Start Ollama server
ollama serve

# Check Ollama URL
ppdm-cli ai "list assets" --provider ollama --ollama-url "http://localhost:11434"
```

### Model Not Found

```bash
# Pull model (Ollama)
ollama pull mistral
ollama pull codellama:7b

# Check available models
ollama list

# Use correct model name
ppdm-cli ai "list assets" --provider ollama --model mistral
```

### Poor Command Generation

```bash
# Try different model
ppdm-cli ai "list assets" --model gpt-4

# Use more specific language
ppdm-cli ai "show me all assets with protection status PROTECTED"

# Use show-command to review
ppdm-cli ai "list assets" --show-command
```

### Safety Confirmation Not Working

```bash
# Use --confirm flag
ppdm-cli ai "delete policy policy-123" --confirm

# Use dry-run first
ppdm-cli ai "delete policy policy-123" --dry-run
```

## Security Considerations

### API Key Security
- **Never expose API keys** in logs or version control
- **Use environment variables** for API keys
- **Rotate API keys** regularly
- **Use least privilege** for API keys

### Command Safety
- **Always use dry-run** for destructive operations
- **Review generated commands** before execution
- **Use --confirm** only when certain
- **Audit AI-generated commands** in production

### Privacy Considerations
- **Ollama**: Local processing, no data leaves your system
- **OpenAI**: Cloud processing, data sent to OpenAI
- **Sensitive data**: Avoid including sensitive data in prompts
- **Compliance**: Ensure AI provider compliance with your requirements

## Related Commands

- **activities** - Monitor PPDM activities
- **assets** - Manage PPDM assets
- **protection-policies** - Manage protection policies
- **curl** - Direct API access

## API Information

- **Operation ID**: Multi-Provider LLM Integration
- **Model Integration**: OpenAI API and Ollama API
- **OpenAI Documentation**: https://platform.openai.com/docs
- **Ollama Documentation**: https://github.com/ollama/ollama-go

## Requirements

### OpenAI
- OpenAI API key
- Internet connection
- Supported model (gpt-3.5-turbo, gpt-4, gpt-4-turbo)

### Ollama
- Local Ollama installation
- Ollama server running
- Downloaded model (mistral, codellama:7b, llama2)

---

*For more information about other tools, see the [tools documentation index](README.md).*
