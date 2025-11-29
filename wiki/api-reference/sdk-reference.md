# SynapseEngine SDK Reference

## Overview
The SynapseEngine Software Development Kit (SDK) provides programmatic access to SynapseEngine's neural components and integration capabilities. This reference covers the core Python SDK for creating, managing, and executing adaptive AI workflows.

---

## SDK Quickstart
```python
from synapse_engine import SynapseClient

# Initialize client
client = SynapseClient(
    api_key="sk_yourapikey123",
    environment="production"  # or "staging"
)

# Execute a prebuilt pipeline
response = client.execute_pipeline(
    pipeline_id="text-sentiment",
    inputs={"text": "SynapseEngine delivers exceptional adaptive capabilities"}
)
```

---

## Core Concepts
### Modules
Preconfigured neural components with standardized I/O:
```python
translation_module = client.get_module("lang-translate-v2")
```

### Pipelines
Sequences of connected modules with routing logic:
```python
customer_analysis_pipeline = {
    "version": "2.1",
    "stages": [
        {"module": "sentiment-analysis", "params": {"model": "v3-large"}},
        {"module": "intent-detection", "threshold": 0.7}
    ]
}
```

### Connectors
API integration handlers:
```python
salesforce_connector = client.get_connector("salesforce-crm")
```

### Context
Execution state management:
```python
session_context = client.create_context(
    expires_in=3600,  # seconds
    persist=True
)
```

---

## API Reference

### SynapseClient Class
#### Initialization
```python
SynapseClient(
    api_key: str,
    environment: str = "production",
    timeout: int = 30,
    max_retries: int = 3
)
```

### Core Methods
#### `create_pipeline()`
```python
client.create_pipeline(
    definition: dict,
    version_notes: str = "",
    tags: List[str] = []
) -> PipelineResponse
```

#### `execute_pipeline()`
```python
client.execute_pipeline(
    pipeline_id: str,
    inputs: dict,
    context_id: str = None,
    async_exec: bool = False
) -> ExecutionResult
```

#### `register_custom_module()`
```python
client.register_custom_module(
    name: str,
    endpoint: str,
    input_schema: dict,
    output_schema: dict
) -> ModuleRegistration
```

### Response Objects
#### PipelineResponse
```json
{
  "id": "pipe_XkBj4fG21",
  "version": "2.1",
  "created_at": "2023-08-15T14:32:10Z",
  "active": true
}
```

#### ExecutionResult
```json
{
  "execution_id": "exe_qTWb7Nk43",
  "results": {...},
  "metrics": {
    "total_time": 0.45,
    "modules_executed": 3
  },
  "context_state": {...}
}
```

---

## Error Handling
### Common Exceptions
| Code | Exception | Resolution |
|------|-----------|------------|
| 401 | `AuthenticationError` | Verify API key validity |
| 403 | `PermissionError` | Check scope permissions |
| 429 | `RateLimitError` | Implement exponential backoff |
| 503 | `ServiceUnavailable` | Retry with `max_retries` |

### HTTP Status Reference
- 200 OK: Successful execution
- 202 Accepted: Async operation started
- 400 Bad Request: Invalid input schema
- 404 Not Found: Resource doesn't exist
- 422 Unprocessable Entity: Pipeline configuration error

---

## Advanced Topics
### Pipeline Versioning
```python
# Rollback to specific version
client.set_pipeline_version(
    pipeline_id="customer-feedback",
    version="1.8"
)
```

### Execution Monitoring
```python
# Get real-time metrics
client.get_execution_metrics(
    execution_id="exe_qTWb7Nk43",
    include_stages=True
)
```

### Idempotency Keys
```python
# Guarantee exactly-once execution
client.execute_pipeline(
    pipeline_id="order-processing",
    inputs={...},
    idempotency_key="ord_1938234"
)
```

---

## Best Practices
1. Use context sessions for stateful workflows
2. Validate module I/O schemas during development
3. Implement circuit breakers for third-party connectors
4. Monitor through `X-Synapse-Metrics` response headers
5. Utilize webhooks for async pipeline completion events

> **Note**: Always test pipeline changes in the `staging` environment before production deployment.