# Rate Limiting Guide

## Overview
SynapseEngine implements systematic rate limiting to ensure equitable resource distribution, maintain system stability, and prevent API abuse. This guide details our rate limiting policies, strategies to monitor usage, and best practices for handling rate limits gracefully.

## How Rate Limiting Works
Our system employs a token bucket algorithm with these characteristics:
- **Per-second caps**: Limits concurrent operations 
- **Per-minute quotas**: Constrains sustained usage
- **Variable limits**: Adjusts based on:
  - Authentication status
  - Service tier
  - Workload criticality
- **Cost weighting**: Resource-intensive operations consume more quota units

## Rate Limits by Context
| Context              | Default Limit                   | Visibility                 |
|----------------------|---------------------------------|----------------------------|
| Authenticated User   | 60 requests/second (burst)      | Headers & Monitoring Portal|
|                      | 2000 requests/minute (sustained)|                            |
| Anonymous Session    | 5 requests/second               | Headers only               |
| Plugin Integrations  | Varies by component SLA         | Component-specific metrics |

## Rate Limit Headers
All API responses include these HTTP headers:
```http
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 58
X-RateLimit-Reset: 30
Retry-After: 5  # Present only when throttled
```

## Handling Rate Limits
### Recommended Pattern (Python Example)
```python
from tenacity import retry, wait_exponential, stop_after_attempt

@retry(wait=wait_exponential(multiplier=1, max=60),
       stop=stop_after_attempt(5))
def make_api_request():
    try:
        response = session.post(endpoint, payload)
        response.raise_for_status()
        return response.json()
    except HTTPError as e:
        if e.response.status_code == 429:
            reset_time = int(e.response.headers.get('Retry-After', 5))
            raise TryAgain()
        raise
```

### Best Practices
1. **Distributed Systems**: Localize rate limiters per service node
2. **Request Optimization**:
   - Batch operations via `/bulk` endpoints
   - Use `fields=minimal` parameter to reduce payloads
3. **Caching Strategies**:
   - Implement ETag validation
   - Cache static resources for 120-300 seconds
4. **Queue Management**:
   - Prioritize mission-critical workflows
   - Implement dead-letter queues for non-essential retries

## Monitoring and Adjustment
1. Access real-time metrics via **Dashboard > Analytics > API Usage**
2. Configure alerts at 75%, 90%, and 100% of quota thresholds
3. Temporary limit increases available during migration windows
4. Permanent quota upgrades require service plan review

## Operational Parameters
```yaml
# Configuration Template (synapse-config.yaml)
rate_limiting:
  adaptive_scaling: true
  burst_buffer: 1.25x_nominal
  cost_map:
    text_generation: 1.0
    image_synthesis: 2.5
    model_finetuning: 10.0
  fallback_strategy: "failover_to_cached_response"
```

## Special Considerations
- **Webhook Responses**: Exempt from counting toward main API quotas
- **DLP Operations**: Compliance scans have dedicated rate pools
- **Cross-Region Calls**: Count against regional quotas

Contact [support@synapseengine.com](mailto:support@synapseengine.com) for enterprise quota consultations or SLA exceptions.