# Basic Usage of SynapseEngine

This guide provides a foundational walkthrough for configuring and executing workflows using SynapseEngine. For comprehensive specifications, refer to the [Core Concepts documentation](../concepts/core-concepts.md).

---

## 1. Installation

Install via package manager:

```bash
pip install synapse-engine
```

**or**

```bash
conda install -c synapse-engine synapse-engine
```

---

## 2. Project Initialization

Create a new project context:

```bash
synapse init my_project --template=standard
```

This generates:
```
my_project/
├── config/
│   └── engine.yaml
└── pipelines/
    └── sample_pipeline.yaml
```

---

## 3. Your First Workflow ("Hello World")

### 3.1 Run a Prebuilt Component

```python
from synapse.components.text import TextAnalyzer

analyzer = TextAnalyzer(api_key="YOUR_API_KEY")
result = analyzer.process(
    text="SynapseEngine enables composable AI systems", 
    analysis_type="sentiment"
)

print(result.metrics)
```

**Sample Output:**
```json
{
  "sentiment": {"score": 0.92, "label": "positive"},
  "entities": ["SynapseEngine", "AI systems"]
}
```

---

## 4. Core Concepts Implementation

### 4.1 Component Configuration
Define components in `config/components.yaml`:

```yaml
components:
  vision_processor:
    module: synapse.components.vision
    class: ImageRecognizer
    params:
      model_version: "resnet-50"
      api_timeout: 30
```

### 4.2 Connector Setup
Configure data sources in `config/connectors.yaml`:

```yaml
connectors:
  aws_in:
    type: s3
    bucket: "input-data-lake"
    region: "us-west-2"
```

### 4.3 Pipeline Construction
Create `pipelines/text_processing.yaml`:

```yaml
pipeline:
  stages:
    - name: text_extraction
      component: pdf_extractor
      inputs: ${connectors.aws_in}/documents
    - name: sentiment_analysis
      component: text_analyzer
      depends_on: text_extraction
      params:
        granularity: "sentence"
```

---

## 5. Workflow Execution

### 5.1 Start the Engine
Initialize the control plane:

```python
from synapse.engine import WorkflowEngine

engine = WorkflowEngine(
    config_path="config/engine.yaml",
    pipeline_registry="pipelines/"
)
```

### 5.2 Execute a Pipeline

```python
execution_id = engine.execute(
    pipeline="text_processing",
    input_parameters={"document_id": "P04392"},
    async_mode=True
)
```

Monitor execution status:

```python
status = engine.get_execution_status(execution_id)
print(f"Current state: {status.state}")
```

---

## 6. Deployment

### 6.1 Start Production Service

```bash
synapse serve --config config/prod.yaml --port 8080
```

### 6.2 API Endpoint Verification

```bash
curl -X POST http://localhost:8080/execute \
  -H "Content-Type: application/json" \
  -d '{"pipeline":"text_processing","params":{"document_id":"XZ209"}}'
```

**Expected Response:**
```json
{
  "execution_id": "3fa85f64-3b7a-46bb-b6a2-c901234567ef",
  "status_endpoint": "/executions/3fa85f64-3b7a-46bb-b6a2-c901234567ef"
}
```

---

## Next Steps
Proceed to [Advanced Workflow Patterns](../usage/advanced-patterns.md) for:
- Conditional execution branching
- Feedback loop implementations
- Dynamic component loading
- Performance optimization techniques

> **Tip:** Validate configurations using `synapse validate --config config/` before deployment.