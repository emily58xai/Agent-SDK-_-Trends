# Examples and Tutorials

Comprehensive examples for Agent Platform SDK covering common use cases and advanced scenarios.

## Table of Contents

- [Quick Start](#quick-start)
- [Basic Operations](#basic-operations)
- [Advanced Usage](#advanced-usage)
- [Error Handling](#error-handling)
- [Real-World Examples](#real-world-examples)
- [Best Practices](#best-practices)

## Quick Start

### Installation and Setup

```bash
npm install agent-platform-sdk
```

```typescript
const { AgentPlatformSDK } = require('agent-platform-sdk');

// Initialize client
const client = new AgentPlatformSDK({
  apiKey: process.env.API_KEY
});

async function quickStart() {
  try {
    // Test connection
    const status = await client.getStatus();
    console.log('✅ Connected successfully');
    
    // Fetch some data
    const data = await client.getData({ limit: 5 });
    console.log('📊 Data retrieved:', data.length, 'items');
    
    // Create new item
    const newItem = await client.createItem({
      name: 'Test Item',
      description: 'Created via SDK'
    });
    console.log('✨ Created item:', newItem.id);
    
  } catch (error) {
    console.error('❌ Error:', error.message);
  }
}

quickStart();
```

## Basic Operations

### Create Operation

```typescript
const newItem = await client.createItem({
  name: 'My Item',
  category: 'example',
  tags: ['demo', 'sdk']
});
console.log('Created:', newItem.id);
```

### Read Operation

```typescript
// Get single item
const item = await client.getItem('item_123');

// Get multiple items
const items = await client.getItems({
  category: 'example',
  limit: 20
});
```

### Update Operation

```typescript
const updated = await client.updateItem('item_123', {
  name: 'Updated Name',
  tags: ['updated', 'demo']
});
```

### Delete Operation

```typescript
await client.deleteItem('item_123');
console.log('Item deleted successfully');
```

## Advanced Usage

### Batch Operations

```typescript
async function batchOperations() {
  // Batch create
  const itemsToCreate = [
    { name: 'Item 1', category: 'batch' },
    { name: 'Item 2', category: 'batch' },
    { name: 'Item 3', category: 'batch' }
  ];
  
  const created = await client.batchCreate(itemsToCreate);
  console.log('Batch created:', created.length, 'items');
}
```

### Filtering and Searching

```typescript
// Simple filtering
const activeItems = await client.getItems({
  status: 'active',
  category: 'important'
});
```

### Pagination

```typescript
// Manual pagination
let page = 1;
let hasMore = true;

while (hasMore) {
  const response = await client.getItems({
    page: page,
    limit: 100
  });
  
  hasMore = response.hasMore;
  page++;
}
```

## Error Handling

### Basic Error Handling

```typescript
async function handleErrors() {
  try {
    const result = await client.riskyOperation();
    return result;
  } catch (error) {
    console.error('Error:', error.message);
    throw error;
  }
}
```

### Advanced Error Handling with Retry Logic

```typescript
class RetryableClient {
  constructor(client) {
    this.client = client;
    this.maxRetries = 3;
  }

  async withRetry(operation) {
    for (let attempt = 1; attempt <= this.maxRetries; attempt++) {
      try {
        return await operation();
      } catch (error) {
        if (attempt === this.maxRetries) {
          throw error;
        }
        await new Promise(resolve => setTimeout(resolve, 1000 * attempt));
      }
    }
  }
}
```

## Real-World Examples

### Building a User Management System

```typescript
class UserManager {
  constructor(client) {
    this.client = client;
  }

  async createUser(userData) {
    const user = await this.client.createUser({
      ...userData,
      status: 'active',
      createdAt: new Date().toISOString()
    });
    
    return user;
  }
}
```

### Data Synchronization

```typescript
class DataSynchronizer {
  constructor(sourceClient, targetClient) {
    this.source = sourceClient;
    this.target = targetClient;
  }

  async syncData() {
    const sourceData = await this.source.getAllData();
    
    for (const record of sourceData) {
      await this.target.createItem(record);
    }
  }
}
```

### Webhook Integration

```typescript
const express = require('express');
const app = express();

app.use(express.json());

app.post('/webhook', (req, res) => {
  console.log('Received webhook:', req.body);
  res.status(200).send('OK');
});

app.listen(3000);
```

## Best Practices

### 1. Connection Management

```typescript
class ConnectionManager {
  constructor() {
    this.clients = new Map();
  }

  getClient(apiKey) {
    if (!this.clients.has(apiKey)) {
      const client = new AgentPlatformSDK({ apiKey });
      this.clients.set(apiKey, client);
    }
    
    return this.clients.get(apiKey);
  }
}
```

### 2. Rate Limiting

```typescript
class RateLimiter {
  constructor(requestsPerSecond = 10) {
    this.rps = requestsPerSecond;
    this.requests = [];
  }

  async waitIfNeeded() {
    const now = Date.now();
    this.requests = this.requests.filter(time => now - time < 1000);
    
    if (this.requests.length >= this.rps) {
      await new Promise(resolve => setTimeout(resolve, 1000));
    }
    
    this.requests.push(Date.now());
  }
}
```

### 3. Caching Strategies

```typescript
class CachedClient {
  constructor(client, ttl = 300000) {
    this.client = client;
    this.cache = new Map();
    this.ttl = ttl;
  }

  async getWithCache(method, params = {}) {
    const key = JSON.stringify({ method, params });
    const cached = this.cache.get(key);

    if (cached && Date.now() - cached.timestamp < this.ttl) {
      return cached.data;
    }

    const data = await this.client[method](params);
    this.cache.set(key, { data, timestamp: Date.now() });
    
    return data;
  }
}
```

### 4. Logging and Monitoring

```typescript
const winston = require('winston');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'sdk.log' })
  ]
});

logger.info('SDK operation completed');
```

## Testing

### Unit Tests

```typescript
const { expect } = require('chai');
const sinon = require('sinon');

describe('Agent Platform SDK Tests', () => {
  let client;

  beforeEach(() => {
    client = new AgentPlatformSDK({
      apiKey: 'test_key'
    });
  });

  it('should return data successfully', async () => {
    const result = await client.getData();
    expect(result).to.be.an('object');
  });
});
```

### Integration Tests

```typescript
describe('Integration Tests', () => {
  it('should handle real API calls', async () => {
    const client = new AgentPlatformSDK({
      apiKey: process.env.TEST_API_KEY
    });
    
    const result = await client.getData();
    expect(result.success).to.be.true;
  });
});
```

## Performance Optimization

### Concurrent Requests

```typescript
async function handleConcurrentRequests() {
  const promises = Array.from({ length: 10 }, (_, i) => 
    client.getData({ id: i })
  );
  
  const results = await Promise.all(promises);
  console.log('Processed', results.length, 'requests concurrently');
}
```

### Memory Management

```typescript
class MemoryEfficientClient {
  constructor(client) {
    this.client = client;
  }

  async processLargeDataset() {
    let cursor = null;
    
    do {
      const batch = await this.client.getData({ cursor, limit: 100 });
      
      for (const item of batch.data) {
        await this.processItem(item);
      }
      
      cursor = batch.nextCursor;
      
      // Force garbage collection if available
      if (global.gc) {
        global.gc();
      }
    } while (cursor);
  }
}
```

---

More examples coming soon! Check our [GitHub repository](undefined) for the latest updates.
