# SynapseEngine API Response Codes

## Overview
This document outlines the standard HTTP response codes returned by the SynapseEngine API. All responses follow RFC 7231 standards with application-specific enhancements.

## Response Structure
Successful responses and errors share a common JSON structure:

```json
{
  "status": "success | partial_success | error",
  "code": 200,
  "message": "Descriptive message",
  "data": {},
  "request_id": "req_abc123xyz456"
}
```

Error responses include additional details:
```json
{
  "status": "error",
  "code": 400,
  "message": "Validation failure",
  "errors": [
    {"field": "input.prompt", "code": "INVALID_LENGTH", "message": "Prompt exceeds 500 characters"}
  ],
  "documentation_url": "https://docs.synapseengine.com/errors/INVALID_LENGTH",
  "request_id": "req_abc123xyz456"
}
```

## Success Codes
### 200 OK
- **Description**: Standard success response
- **Required Fields**: `data` payload

### 201 Created
- **Description**: Resource successfully created
- **Headers**:  
  `Location: /v1/models/model_cust789xyz`
- **Special Case**: Returns created resource in `data` field

### 202 Accepted
- **Description**: Request accepted for asynchronous processing
- **Response Features**:
  ```json
  {
    "monitor_endpoint": "/v1/queue/jobs/job_abc123",
    "estimated_completion": "2023-08-15T14:30:00Z"
  }
  ```

## Client Errors
### 400 Bad Request
- **Common Causes**:
  - Malformed JSON
  - Invalid parameter types
  - Missing required fields

### 401 Unauthorized
- **Solution Steps**:
  1. Verify API key validity
  2. Check key permissions
  3. Validate authentication header format (`Authorization: Bearer sk_live_...`)

### 403 Forbidden
- **Scenarios**:
  - Valid credentials with insufficient permissions
  - Account suspended
  - Regional restrictions

### 404 Not Found
- **Standard Message**:  
  `The requested resource /v1/models/nonexistent could not be located`

### 429 Rate Limited
- **Headers**:
  - `X-RateLimit-Limit: 100`
  - `X-RateLimit-Remaining: 0`
  - `X-RateLimit-Reset: 300`
  - `Retry-After: 300`
  
## Server Errors
### 500 Internal Server Error
- **Action Required**: 
  - Retry with exponential backoff
  - Include `request_id` in support tickets

### 501 Not Implemented
- **Indicates**: Unsupported API version in request header
  (`Accept: application/vnd.synapse.v3+json`)

### 503 Service Unavailable
- **Common During**:
  - Platform maintenance
  - Critical security patches
  - Major version upgrades
- **Watch For**:  
  `Retry-After: 3600` header for estimated downtime

## Business Logic Errors
### 402 Payment Required
- **Trigger Conditions**:
  - Insufficient API credits
  - Subscription plan limits exceeded
- **Response Features**:
  ```json
  {
    "quota_reset": "2023-08-01T00:00:00Z",
    "upgrade_url": "https://console.synapseengine.com/billing"
  }
  ```

### 409 Conflict
- **Resolution Path**:
  ```json
  {
    "conflicting_resource": "/v1/pipelines/pl_12345",
    "resolution_options": ["retry", "override", "rename"]
  }
  ```

## Troubleshooting Guide
When encountering errors:
1. Validate request against OpenAPI specification
2. Check error `documentation_url` for context-specific guidance
3. Examine `request_id` timestamps in audit logs
4. Verify SDK compatibility when using client libraries

API Health Status:  
`GET /v1/system/healthcheck`  
Returns operational status of core services.