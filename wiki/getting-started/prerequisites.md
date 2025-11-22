# Prerequisites for SynapseEngine

This document outlines the mandatory requirements to begin using SynapseEngine. Verify that your environment meets these specifications before proceeding with installation.

---

## System Requirements

### Minimum Configuration
- **Operating System**: Linux Kernel 5.4+/macOS 12+/Windows 10 (64-bit)
- **CPU**: x86-64 or ARM64 processor with 4 physical cores
- **RAM**: 8GB DDR4
- **Storage**: 20GB available SSD space
- **Network**: 100 Mbps internet connection

### Recommended Configuration
- **Operating System**: Ubuntu 22.04 LTS/macOS Ventura/Windows 11
- **CPU**: 8-core processor (Intel Xeon E5-26xx v4+/AMD EPYC 7xx2+ or equivalent)
- **RAM**: 16GB DDR5
- **Storage**: NVMe SSD with 100GB free space
- **Network**: 1 Gbps internet connection with low latency (<50ms)

---

## Software Dependencies

### Core Runtime
- **Python**: 3.10–3.12 (`python --version`)
  ```bash
  # Verify installation
  python3 --version
  ```
- **Node.js**: 18.x LTS (`node --version`)
- **Docker Engine**: 24.0+ (`docker --version`)
- **PostgreSQL**: 15.x (`psql --version`)

### Framework Libraries
- PyTorch 2.0+ with CUDA 12.1 (GPU users only)
- TensorFlow 2.15+ (Optional for model conversion)
- RabbitMQ 3.12+ for event streaming

---

## Accounts & Services

1. **SynapseEngine License Key**  
   Register at [portal.synapseengine.ai](https://portal.synapseengine.ai) to obtain your trial/production key.

2. **Cloud Provider Credentials**  
   Valid access tokens for at least one:
   - AWS IAM Role with `s3:FullAccess`
   - Azure Service Principal
   - Google Cloud Service Account

3. **API Endpoints**  
   Whitelist these domains in your firewall:
   ```
   api.synapseengine.ai:443
   registry.neuralflux.ai:5000
   ```

---

## Network & Security

### Mandatory Configurations
- Open outbound ports: `443 (HTTPS)`, `5672 (AMQP)`, `5432 (PostgreSQL)`
- TLS 1.3+ support for external API communication
- Firewall exceptions for `/usr/local/synapse` (Linux/macOS) or `C:\Program Files\SynapseEngine` (Windows)

---

## Optional Tooling
- **IDE**: VS Code with Python/Jinja extensions
- **CLI Utilities**: `curl 8.4.0+`, `jq 1.7+`, `wget 2.4+`
- **Monitoring**: Prometheus 2.47+ + Grafana 10.1+

---

## Verification Checklist

Confirm readiness by executing:
```bash
synapse-check-env --validate
```

Expected output:
```
[OK] System resources adequate
[OK] Python 3.10.12 detected
[OK] Docker Daemon active
[OK] License key authenticated
```

> **Troubleshooting**  
> Resolve failures using `synapse-check-env --verbose` before proceeding to installation.

---

**Next Steps**: Proceed to [Installation Guide](./installation.md) once all prerequisites are satisfied.