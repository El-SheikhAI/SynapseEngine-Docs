# SynapseEngine CLI Command Reference

## Overview
The SynapseEngine Command-Line Interface (CLI) provides programmatic control over neural component orchestration, workflow execution, and system configuration. This documentation covers essential commands with their respective options and usage examples.

---

## Installation and Setup
```bash
# Install global CLI package
pip install synapseengine-cli

# Verify installation
synap --version
```

---

## Basic Operations

### Authentication
```bash
# Login to SynapseEngine services
synap auth login

# Logout from current session
synap auth logout

# Display authentication status
synap auth status
```

### Verbosity Control
```bash
synap [command] -v  # Verbose output (Level 1)
synap [command] -vv  # Debug output (Level 2)
```

---

## Workflow Management

### Create New Workflow
```bash
synap workflow create [-d|--description DESCRIPTION] WORKFLOW_NAME
```

**Options:**
- `--description`: Human-readable workflow description
- `-f/--file`: Specify workflow definition file (default: workflow.yaml)

### Deploy Workflow
```bash
synap workflow deploy WORKFLOW_ID
```

### Execute Workflow
```bash
synap workflow execute WORKFLOW_ID [--param KEY=VALUE...]
```

### List Workflows
```bash
synap workflow list [--status STATUS_FILTER]
```

---

## System Configuration

### View Configuration
```bash
synap config list
```

### Set Configuration Values
```bash
synap config set PROPERTY VALUE

# Example: Set processing cluster
synap config set cluster.url https://cluster.synapse.ai
```

---

## Plugin Management

### Available Plugins
```bash
synap plugin search [QUERY]

# Install plugin
synap plugin install PLUGIN_NAME[@VERSION]

# List installed plugins
synap plugin list
```

### Plugin Configuration
```bash
synap plugin configure PLUGIN_NAME
```

---

## Execution Contexts

### Manage Runtime Environments
```bash
synap env create ENV_NAME
synap env switch ENV_NAME
synap env list
```

### Environment Variables
```bash
synap env set VAR_NAME VALUE
synap env unset VAR_NAME
```

---

## API Integration

### Third-Party Service Connections
```bash
synap api connect SERVICE_NAME [-c|--credential FILE]
synap api disconnect SERVICE_NAME
```

### Service Status Verification
```bash
synap api verify SERVICE_NAME
```

---

## Help System

### Command Documentation
```bash
synap help [COMMAND]

# Display help for workflow command group
synap workflow --help
```

---

## Advanced Features

### Batch Processing
```bash
synap batch create -i INPUT_FILE -w WORKFLOW_ID
synap batch status BATCH_ID
```

### Output Redirection
```bash
synap workflow execute WORKFLOW_ID --output results.json
```

---

**Best Practices Tip:** Use `synap --version-check` regularly to ensure CLI compatibility with backend services. Experimental features are accessible via `synap experimental [command]`.