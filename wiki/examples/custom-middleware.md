# Building Custom Middleware in SynapseEngine

Middleware components in SynapseEngine enable granular control over data flows within neural workflows. They operate as interceptors between neural components and external APIs, allowing for pre/post-processing, validation, observability, and transformation.

## Middleware Structure
All middleware must implement this interface:

```python
class SynapseMiddleware:
    async def process_input(self, context: WorkflowContext, data: Any) -> Any:
        """Intercept and modify incoming data"""
        return data

    async def process_output(self, context: WorkflowContext, output: Any) -> Any:
        """Intercept and modify outgoing results"""
        return output

    @property
    def configuration_schema(self) -> dict:
        """Define runtime parameters (optional)"""
        return {}
```

## Example 1: Input Logger
This middleware logs payload metadata before neural processing:

```python
import logging
from datetime import datetime

class InputLogger(SynapseMiddleware):
    def __init__(self, log_level: str = "INFO"):
        self.logger = logging.getLogger("InputLogger")
        self.log_level = log_level.upper()

    async def process_input(self, context, data):
        log_entry = {
            "timestamp": datetime.utcnow().isoformat(),
            "workflow_id": context.workflow_id,
            "data_type": type(data).__name__,
            "data_size": len(data) if hasattr(data, '__len__') else None
        }
        
        getattr(self.logger, self.log_level.lower())(log_entry)
        return data

    @property
    def configuration_schema(self):
        return {
            "type": "object",
            "properties": {
                "log_level": {
                    "type": "string",
                    "enum": ["DEBUG", "INFO", "WARNING", "ERROR"],
                    "default": "INFO"
                }
            }
        }
```

## Example 2: Output Sanitizer
Redact sensitive information from neural outputs:

```python
import re

class OutputSanitizer(SynapseMiddleware):
    def __init__(self, patterns: list = [r"\d{3}-\d{2}-\d{4}"]):
        self.regexes = [re.compile(p) for p in patterns]

    async def process_output(self, context, output):
        if isinstance(output, str):
            for regex in self.regexes:
                output = regex.sub("[REDACTED]", output)
        return output

    @property
    def configuration_schema(self):
        return {
            "type": "object",
            "properties": {
                "patterns": {
                    "type": "array",
                    "items": {"type": "string"},
                    "description": "List of regex patterns to redact"
                }
            }
        }
```

## Configuration Example
Deploy middleware through workflow definitions:

```yaml
workflows:
  sentiment_analysis:
    components:
      - model: sentiment-classifier-v4
    middleware:
      - class: InputLogger
        config:
          log_level: DEBUG
      - class: OutputSanitizer
        config:
          patterns:
            - r"\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b"  # Emails
```

## Best Practices
1. **Idempotency**: Design middleware operations to be repeatable without side effects
2. **Context Awareness**: Leverage `WorkflowContext` for system state information
3. **Performance Budget**: Asynchronous operations with <50ms latency overhead
4. **Error Propagation**: Raise `MiddlewareException` for operational failures
5. **Testing**: Validate against both structured and unstructured payloads

Middleware execution order follows declaration sequence in the workflow configuration with this pattern:
```
Input → Middleware 1 → ... → Middleware N → Neural Component → Middleware N → ... → Middleware 1 → Output
```

## Diagnostic Tips
Monitor middleware performance through:
```bash
synapse-cli metrics middleware --workflow=sentiment_analysis --timeframe=1h
```

View processing logs:
```bash
synapse-cli logs middleware --component=OutputSanitizer
```

For complete API specifications, refer to [Middleware Development Guide](../architecture/middleware-architecture.md).