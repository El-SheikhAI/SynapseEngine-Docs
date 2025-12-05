# Development Setup Guide

This document provides comprehensive instructions for setting up your local development environment for contributing to the SynapseEngine project. Follow these steps to ensure your system meets all requirements and dependencies for effective development.

## Prerequisites

### System Requirements
- Operating System: Linux (Ubuntu 22.04+ recommended), macOS (12.0+), or Windows 10/11 (WSL2 strongly recommended)
- CPU: 64-bit processor (4 cores minimum)
- RAM: 16GB+ recommended
- Storage: 10GB available space

### Essential Tools
1. **Version Control**
   - Git 2.35+ ([Installation Guide](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git))
   
2. **Runtime Environments**
   - Python 3.8-3.11 ([pyenv Recommended](https://github.com/pyenv/pyenv))
   - Node.js 18.x LTS ([nvm Recommended](https://github.com/nvm-sh/nvm))
   - Java JDK 17 (for connector modules)

3. **Containerization**
   - Docker 20.10+ ([Installation Guide](https://docs.docker.com/engine/install/))
   - Docker Compose 2.15+

4. **Package Managers**
   - pip 23.0+
   - npm 9.0+

## Installation Procedure

### 1. Repository Setup
```bash
git clone https://github.com/yourorganization/SynapseEngine-Docs.git
cd SynapseEngine-Docs
git submodule update --init --recursive
```

### 2. Environment Initialization
```bash
python -m venv .venv
source .venv/bin/activate  # Linux/macOS
# OR
.\.venv\Scripts\activate  # Windows
```

### 3. Dependency Installation
```bash
pip install --upgrade pip wheel
pip install -r requirements/dev.txt
npm install --prefix ./frontend
```

### 4. Service Containers
```bash
docker-compose -f docker-compose.dev.yml up -d
```

## Configuration

### Environment Variables
Create `.env` file from template:
```bash
cp .env.sample .env
```

Configure essential variables:
```env
# Core Services Configuration
SYNAPSE_API_KEY=your_dev_key
NEURAL_MODULES_PATH=./modules
LOG_LEVEL=DEBUG

# Database Configuration
POSTGRES_USER=synapse_dev
POSTGRES_PASSWORD=secure_password
POSTGRES_DB=engine_dev
```

## Build and Run

### Core System
```bash
python -m synapse_core --env=development
```

### Frontend Development
```bash
cd frontend && npm run dev
```

### Module Compilation
```bash
python -m build_modules --clean
```

## Testing Framework

### Unit Tests
```bash
pytest tests/unit --cov=synapse_core
```

### Integration Tests
```bash
pytest tests/integration --raise-on-warnings
```

### End-to-End Tests
```bash
npm test --prefix ./frontend
```

## Troubleshooting

### Common Issues

1. **Dependency Conflicts**
   - Solution: Rebuild environment from scratch using `make clean-env && make env`

2. **Port Conflicts**
   - Solution: Check running services with `lsof -i :PORT_NUMBER`

3. **Database Connection Failures**
   - Verify Docker services: `docker-compose -f docker-compose.dev.yml ps`
   - Reset databases: `make reset-db`

4. **CORS Errors in Development**
   - Ensure frontend is running on `http://localhost:3000`
   - Verify `CORS_ORIGINS` includes your development URL in `.env`

## Maintenance

### Dependency Updates
1. Update Python requirements:
   ```bash
   pip freeze > requirements/dev.txt
   ```

2. Update NPM packages:
   ```bash
   npm update --prefix ./frontend
   ```

### System Cleanup
```bash
make clean  # Removes build artifacts
make nuke   # Full system reset (containers + environments)
```

This environment configuration meets all requirements for developing core features, connectors, and neural modules. Report any environment inconsistencies to the Infrastructure team via project issues.