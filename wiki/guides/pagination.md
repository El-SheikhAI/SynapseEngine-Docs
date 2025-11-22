# Pagination Guide

SynapseEngine employs a standardized pagination system to manage large datasets efficiently across all API endpoints returning collections. This guide documents implementation patterns and best practices for consuming paginated resources.

## Default Behavior
- All list endpoints return **20 items per page** by default
- Responses include only the current page's dataset
- Total dataset remains available until explicitly truncated (TTL: 24 hours)

## Query Parameters
Control pagination through these URL parameters:

| Parameter   | Type    | Description                           | Constraints             |
|-------------|---------|---------------------------------------|-------------------------|
| `page`      | Integer | Specific page number                  | Min: 1, Max: 10000      |
| `per_page`  | Integer | Items per page                        | Min: 1, Max: 100        |
| `max_per_page` | Integer | Override system maximum (requires `admin` scope) | Max: 1000 |

```http
GET /v1/models?page=3&per_page=50
```

## Response Format
All paginated responses follow this JSON structure:

```json
{
  "data": [
    { /* resource object */ },
    { /* resource object */ }
  ],
  "pagination": {
    "current_page": 3,
    "per_page": 50,
    "total_pages": 17,
    "total_items": 815,
    "_links": {
      "first": "/v1/models?page=1",
      "prev": "/v1/models?page=2",
      "next": "/v1/models?page=4",
      "last": "/v1/models?page=17"
    }
  }
}
```

## Best Practices
1. **Page Size Optimization**
   - Start with default `per_page` values
   - Increase gradually based on client capabilities
   - Monitor `X-RateLimit-Remaining` header when using large pages

2. **Iteration Patterns**
   ```python
   # Sequential access
   current_page = 1
   while current_page <= response['pagination']['total_pages']:
       process_data(response['data'])
       current_page += 1
       response = get_next_page(response['pagination']['_links']['next'])
   
   # Direct access (bookmarkable URLs)
   save_bookmark(response['pagination']['_links']['last'])
   ```

3. **Concurrency Control**
   - Use `ETag` headers for cache validation
   - Implement exponential backoff when receiving `429 Too Many Requests`
   - Avoid parallel page requests within the same dataset

## Edge Case Handling
- **Empty pages**: Returns `204 No Content` beyond `total_pages`
- **Invalid parameters**: Falls back to defaults with `400 Bad Request` 
- **Expired datasets**: Returns `410 Gone` with `regen_url` in headers

**Important**: Always use provided `_links` rather than constructing URLs manually, as pagination tokens may contain session-specific encryption.

## Performance Notes
- Page 1 returns fastest (cached by default)
- Pages 2-10 have linear latency growth
- Pages >10 may trigger async materialization (check `X-Async-Request` header)
- Sorting operations (`?sort=field_name`) disable parallel materialization

This implementation complies with RFC 8288 (Web Linking) while maintaining compatibility with GraphQL cursor patterns through the `X-Cursor` header in OPTIONS responses. 

> **Enterprise Tip**: Contact support to enable `Seek Method` pagination for datasets exceeding 100K records, which uses index-based positioning instead of page counting.