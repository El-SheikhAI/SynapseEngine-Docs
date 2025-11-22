# SynapseEngine Core Types Reference

This document defines the foundational data types used across the SynapseEngine API. These types enable consistent interaction with neural components, workflow definitions, and API integrations.

---

## Base Types

### 1. NeuralComponent
Represents a single AI processing unit within a workflow.

**Properties:**
```typescript
{
  id: string;
  version: string;
  type: ComponentType; // ['TRANSFORMER', 'CLASSIFIER', 'GENERATOR', 'ANALYZER']
  inputs: SchemaDefinition;
  outputs: SchemaDefinition;
  config: { [key: string]: ComponentParameter };
  metadata: {
    author: string;
    description: string;
    vendor?: string;
  };
}
```

**Example:**
```json
{
  "id": "sentiment-analyzer-v3",
  "version": "3.1.0",
  "type": "ANALYZER",
  "inputs": {
    "text": "string"
  },
  "outputs": {
    "sentiment": "number",
    "confidence": "number"
  },
  "config": {
    "language": { "type": "string", "default": "en" }
  }
}
```

---

### 2. WorkflowDefinition
Blueprint for chaining neural components and API calls.

**Properties:**
```typescript
{
  id: string;
  nodes: WorkflowNode[];
  edges: WorkflowEdge[];
  environmentVariables: string[];
}
```

**Subtypes:**
- **WorkflowNode**
  ```typescript
  {
    id: string;
    componentId: string;
    configOverrides?: { [key: string]: any };
  }
  ```
  
- **WorkflowEdge**
  ```typescript
  {
    sourceNodeId: string;
    targetNodeId: string;
    sourceOutput: string;
    targetInput: string;
    transformation?: string; // Optional data transformation script
  }
  ```

---

## Data Handling Types

### 3. DataPayload
Standard container for information transferred between components.

**Properties:**
```typescript
{
  contentType: string; // MIME type
  encoding: 'utf8' | 'base64' | 'binary';
  data: any;
  metadata?: { [key: string]: string };
}
```

---

### 4. ExecutionContext
Runtime information for workflow execution.

**Properties:**
```typescript
{
  executionId: string;
  sessionId?: string;
  timestamp: string; // ISO 8601 format
  environment: 'development' | 'staging' | 'production';
  apiCredentials: APICredential[];
}
```

---

## Integration Types

### 5. APIConnection
Configuration for external service integrations.

**Properties:**
```typescript
{
  serviceId: string;
  baseURL: string;
  authenticationType: 'OAUTH2' | 'API_KEY' | 'BASIC';
  authConfig: {
    keyLocation: 'header' | 'query';
    keyName: string;
  };
  defaultHeaders?: { [key: string]: string };
  rateLimit?: number; // Requests per second
}
```

---

## Error Types

### 6. EngineError
Standard error response format.

**Properties:**
```typescript
{
  errorCode: string;
  message: string;
  componentId?: string;
  workflowId?: string;
  details?: {
    [key: string]: any;
    documentationUrl?: string;
  };
}
```

---

## Enumerations

### ComponentType
```typescript
'TRANSFORMER' | 'CLASSIFIER' | 'GENERATOR' | 'ANALYZER' | 'CONNECTOR'
```

### ExecutionStatus
```typescript
'PENDING' | 'RUNNING' | 'COMPLETED' | 'FAILED' | 'PAUSED' | 'CANCELLED'
```

---

> **Note:** All timestamp fields follow ISO 8601 format (YYYY-MM-DDTHH:mm:ss.sssZ) in UTC timezone. String formats must be UTF-8 encoded unless otherwise specified in individual component documentation.