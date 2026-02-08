# Authentication Guide

This guide covers all supported authentication methods for Agent Platform SDK.

## Overview

Agent Platform SDK supports the following authentication methods:

- [Bearer Token](#bearer-token)
- [API Key](#api-key)

## Getting API Credentials

1. Sign up for an account at [your-platform.com](https://api.agent-platform.com/v1)
2. Navigate to the API section in your dashboard
3. Generate new API credentials
4. Copy your credentials securely

## Bearer Token

Authentication using Bearer tokens.

### Usage

```typescript
const client = new AgentPlatformSDK({
  bearerToken: 'your_bearer_token'
});
```

## API Key

The simplest authentication method using an API key.

### Setup

1. Get your API key from the dashboard
2. Include it in requests via header or parameter

### Usage

```typescript
const client = new AgentPlatformSDK({
  apiKey: 'your_api_key_here'
});
```

### Headers

```http
Authorization: Bearer your_api_key_here
```

## Environment Variables

We recommend storing your credentials as environment variables:

```.env
# Agent Platform SDK Configuration
API_KEY=your_api_key_here
API_SECRET=your_api_secret_here
ENVIRONMENT=production
```

## Security Best Practices

### ✅ Do's

- Store credentials as environment variables
- Use different credentials for different environments
- Regularly rotate your API keys
- Monitor API usage and access logs
- Use HTTPS for all API requests
- Implement proper error handling

### ❌ Don'ts

- Never commit credentials to version control
- Don't log credentials in application logs
- Avoid hardcoding credentials in source code
- Don't share credentials via email or chat
- Don't use production credentials in development

## Testing Authentication

```typescript
async function testAuth() {
  try {
    const user = await client.getCurrentUser();
    console.log('Authentication successful:', user.email);
    return true;
  } catch (error) {
    console.error('Authentication failed:', error.message);
    return false;
  }
}

testAuth();
```

## Troubleshooting

### Common Issues

#### Invalid Credentials

**Problem**: Getting 401 Unauthorized errors

**Solution**: 
- Verify your API key is correct
- Check if credentials have expired
- Ensure you're using the right environment credentials

#### Rate Limiting

**Problem**: Getting 429 Too Many Requests errors

**Solution**:
- Implement exponential backoff
- Check your rate limits in the dashboard
- Consider upgrading your plan

#### Permissions Issues

**Problem**: Getting 403 Forbidden errors

**Solution**:
- Verify your account has the required permissions
- Check if your subscription includes the features you're trying to access
- Contact support if permissions seem incorrect

### Debug Mode

Enable debug mode to see detailed authentication logs:

```typescript
const client = new AgentPlatformSDK({
  apiKey: 'your_api_key',
  debug: true
});

// Debug logs will show:
// - Request URLs and headers
// - Response status and data
// - Authentication details
```

## Migration from v1.x

If you're upgrading from an older version:

```typescript
// v1.x (deprecated)
const client = new OldClient('api_key');

// v2.x (current)
const client = new AgentPlatformSDK({
  apiKey: 'api_key',
  version: '2.0'
});

// Method name changes:
// v1: client.getUsers() -> v2: client.listUsers()
// v1: client.createUser() -> v2: client.users.create()
```

---

Need help? Check our [FAQ](./FAQ.md) or [contact support](mailto:support@example.com).
