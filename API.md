# API Reference

Complete API reference for Agent Platform SDK v1.0.0

## Base URL

```
https://api.agent-platform.com/v1
```

## Authentication

All API requests require authentication. See [Authentication Guide](./AUTHENTICATION.md) for setup instructions.

## SDK Client

### Initialization

```typescript
const { AgentPlatformSDK } = require('agent-platform-sdk');

const client = new AgentPlatformSDK({
  apiKey: 'your_api_key',
  baseURL: 'https://api.agent-platform.com/v1',
  timeout: 30000,
  retries: 3,
  debug: false
});
```

### Configuration Options

| Option | Type | Required | Description |
|--------|------|----------|-------------|
| `apiKey` | string | Yes* | Your API key for authentication |
| `baseURL` | string | No | Custom base URL (default: https://api.agent-platform.com/v1) |
| `timeout` | number | No | Request timeout in milliseconds (default: 30000) |
| `retries` | number | No | Number of retry attempts (default: 3) |
| `debug` | boolean | No | Enable debug logging (default: false) |

*Required unless using other authentication methods

## API Endpoints

### GET /agents List all agents

No description provided

```typescript
// Example usage
const result = await client.endpoint();
```

**Parameters**: See method documentation below
**Response**: Standard API response format

### POST /agents Create a new agent

No description provided

```typescript
// Example usage
const result = await client.endpoint();
```

**Parameters**: See method documentation below
**Response**: Standard API response format

### GET /agents/{id} Get agent details

No description provided

```typescript
// Example usage
const result = await client.endpoint();
```

**Parameters**: See method documentation below
**Response**: Standard API response format

### PUT /agents/{id} Update an agent

No description provided

```typescript
// Example usage
const result = await client.endpoint();
```

**Parameters**: See method documentation below
**Response**: Standard API response format

### DELETE /agents/{id} Delete an agent

No description provided

```typescript
// Example usage
const result = await client.endpoint();
```

**Parameters**: See method documentation below
**Response**: Standard API response format

### POST /agents/{id}/execute Execute agent task

No description provided

```typescript
// Example usage
const result = await client.endpoint();
```

**Parameters**: See method documentation below
**Response**: Standard API response format

### GET /tasks List tasks

No description provided

```typescript
// Example usage
const result = await client.endpoint();
```

**Parameters**: See method documentation below
**Response**: Standard API response format

### POST /tasks Create a task

No description provided

```typescript
// Example usage
const result = await client.endpoint();
```

**Parameters**: See method documentation below
**Response**: Standard API response format

### GET /tasks/{id} Get task status

No description provided

```typescript
// Example usage
const result = await client.endpoint();
```

**Parameters**: See method documentation below
**Response**: Standard API response format

## Response Format

All API responses follow this standard format:

```json
{
  "success": true,
  "data": {},
  "message": "Success message",
  "timestamp": "2024-01-01T00:00:00Z",
  "requestId": "req_1234567890"
}
```

## Error Handling

### Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable error message",
    "details": {}
  },
  "timestamp": "2024-01-01T00:00:00Z",
  "requestId": "req_1234567890"
}
```

### Common Error Codes

| Code | Status | Description |
|------|--------|-------------|
| `UNAUTHORIZED` | 401 | Invalid or missing authentication |
| `FORBIDDEN` | 403 | Insufficient permissions |
| `NOT_FOUND` | 404 | Resource not found |
| `VALIDATION_ERROR` | 422 | Invalid request parameters |
| `RATE_LIMITED` | 429 | Too many requests |
| `INTERNAL_ERROR` | 500 | Internal server error |

### Error Handling in Code

```typescript
try {
  const result = await client.getData();
  return result;
} catch (error) {
  if (error.code === 'UNAUTHORIZED') {
    // Handle authentication error
    throw new Error('Please check your API credentials');
  } else if (error.code === 'RATE_LIMITED') {
    // Handle rate limiting
    await new Promise(resolve => setTimeout(resolve, error.retryAfter * 1000));
    return client.getData(); // Retry
  } else {
    // Handle other errors
    console.error('API Error:', error.message);
    throw error;
  }
}
```

## Pagination

Large datasets are paginated using cursor-based pagination:

```typescript
async function getAllData() {
  let allData = [];
  let cursor = null;
  
  do {
    const response = await client.getData({
      cursor: cursor,
      limit: 100
    });
    
    allData = allData.concat(response.data);
    cursor = response.nextCursor;
  } while (cursor);
  
  return allData;
}
```

## Rate Limiting

The API implements rate limiting to ensure fair usage:

- **Rate Limit**: 1000 requests per hour per API key
- **Headers**: Rate limit information is returned in response headers
  - `X-RateLimit-Limit`: Request limit per hour
  - `X-RateLimit-Remaining`: Remaining requests
  - `X-RateLimit-Reset`: Timestamp when limit resets

## SDK Methods

### Core Methods

#### `getData(options?)`

Retrieve data with optional filtering.

**Parameters:**
- `options` (object, optional): Query options
  - `limit` (number): Number of results to return
  - `cursor` (string): Pagination cursor
  - `filter` (object): Filter criteria

**Returns:** Promise<ApiResponse>

#### `createItem(data)`

Create a new item.

**Parameters:**
- `data` (object): Item data

**Returns:** Promise<ApiResponse>

#### `updateItem(id, data)`

Update an existing item.

**Parameters:**
- `id` (string): Item ID
- `data` (object): Updated item data

**Returns:** Promise<ApiResponse>

#### `deleteItem(id)`

Delete an item.

**Parameters:**
- `id` (string): Item ID

**Returns:** Promise<void>

## Types and Interfaces

### Core Types

```typescript
interface ApiResponse<T = any> {
  success: boolean;
  data: T;
  message: string;
  timestamp: string;
  requestId: string;
}

interface ApiError {
  code: string;
  message: string;
  details?: any;
}

interface ClientOptions {
  apiKey: string;
  baseURL?: string;
  timeout?: number;
  retries?: number;
  debug?: boolean;
}
```

## Advanced Usage

### Custom Headers

```typescript
const client = new AgentPlatformSDK({
  apiKey: 'your_api_key',
  headers: {
    'X-Custom-Header': 'custom-value',
    'X-Client-Version': '1.0.0'
  }
});
```

### Middleware

```typescript
client.use('request', (request) => {
  console.log('Making request:', request.url);
  request.headers['X-Request-ID'] = Date.now().toString();
  return request;
});

client.use('response', (response) => {
  console.log('Response received:', response.status);
  return response;
});
```

### Webhooks

```typescript
const express = require('express');
const app = express();

app.use(express.json());

app.post('/webhook', (req, res) => {
  const signature = req.headers['x-webhook-signature'];
  
  if (client.verifyWebhook(req.body, signature)) {
    console.log('Webhook verified:', req.body);
    // Process webhook data
    res.status(200).send('OK');
  } else {
    res.status(401).send('Invalid signature');
  }
});

app.listen(3000, () => {
  console.log('Webhook server running on port 3000');
});
```

---

For more examples and tutorials, see [EXAMPLES.md](./EXAMPLES.md).
