# SynapseEngine Setup Guide

## Prerequisites
Before installing SynapseEngine, ensure your system meets these requirements:

- **Operating System**: Linux (Ubuntu 22.04 LTS or newer), macOS (12.6+), or Windows (10/11 with WSL2)
- **Python**: 3.10 or higher (with pip)
- **Node.js**: 18.x LTS or newer (optional for JS/TS components)
- **Memory**: Minimum 4GB RAM (8GB recommended for complex workflows)
- **Storage**: 500MB available space

## Installation Methods

### Method 1: Core Package Installation
1. Create a virtual environment (recommended):
   ```bash
   python -m venv synapse-env
   source synapse-env/bin/activate  # Linux/macOS
   # OR
   .\synapse-env\Scripts\activate  # Windows
   ```

2. Install the core engine:
   ```bash
   pip install synapse-engine-core==3.2.0
   ```

### Method 2: Full Toolkit Installation (Includes All Components)
```bash
pip install synapse-engine[full]==3.2.0
```

### Method 3: Component-Specific Installation
```bash
pip install synapse-engine-core==3.2.0
pip install synapse-nlp==2.1.4  # Natural Language Processing
pip install synapse-vision==1.8.3  # Computer Vision
pip install synapse-integrations==4.0.1  # API Connectors
```

## Configuration Setup

1. Generate your configuration file:
   ```bash
   synapse configure init
   ```

2. Edit the generated `~/.synapse/config.yaml`:
   ```yaml
   core:
     log_level: INFO
     cache_dir: ~/.synapse/cache
     execution_mode: hybrid  # local | cloud | hybrid

   integrations:
     openai:
       api_key: YOUR_API_KEY
     aws:
       access_key_id: YOUR_AWS_ACCESS_KEY
       secret_access_key: YOUR_AWS_SECRET_KEY
   ```

3. Set environment variables (alternative to config file):
   ```bash
   export SYNAPSE_OPENAI_API_KEY='your_api_key'
   export SYNAPSE_AWS_ACCESS_KEY_ID='your_access_key'
   ```

## Verification

Run these commands to confirm proper installation:

1. Check version:
   ```bash
   synapse --version
   ```
   Expected output: `SynapseEngine 3.2.0 (Build 4271)`

2. Start the local server:
   ```bash
   synapse server start
   ```

3. Execute a test workflow:
   ```bash
   synapse execute --workflow examples/hello_synapse.yml
   ```

## Development Setup (Advanced)

For custom component development:

1. Clone the repository:
   ```bash
   git clone https://github.com/synapse-engine/core.git
   cd core
   ```

2. Install development dependencies:
   ```bash
   pip install -r requirements-dev.txt
   ```

3. Run test suite:
   ```bash
   pytest tests/ --cov=synapse --cov-report=html
   ```

## Troubleshooting

Common issues and solutions:

**Issue**: `ModuleNotFoundError` during import  
**Solution**:  
```bash
pip install synapse-engine-core --upgrade
```

**Issue**: API connection timeouts  
**Solution**:  
1. Verify firewall settings
2. Check proxy configuration in `config.yaml`
3. Test connection: `synapse netcheck`

**Issue**: GPU acceleration not detected  
**Solution**:  
1. Install CUDA Toolkit 12.x
2. Run: `pip install synapse-engine[gpu]==3.2.0`

## Next Steps

Proceed to:  
- [Workflow Creation Guide](../workflows/basic-workflows.md)  
- [API Reference](../api/core-modules.md)  
- [Component Marketplace](../marketplace/README.md)