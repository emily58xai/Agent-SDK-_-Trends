# Agent Platform SDK

A comprehensive TypeScript SDK for building and managing AI agents with task execution, memory management, and workflow automation capabilities.

[![npm version](https://badge.fury.io/js/agent-platform-sdk.svg)](https://badge.fury.io/js/agent-platform-sdk)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Table of Contents

- [Installation](#installation)
- [Quick Start](#quick-start)
- [Authentication](#authentication)
- [Features](#features)
- [API Reference](#api-reference)
- [Examples](#examples)
- [Contributing](#contributing)
- [License](#license)

## Installation

Install the SDK using npm:

```bash
npm install agent-platform-sdk
```

Or using yarn:

```bash
yarn add agent-platform-sdk
```

## Quick Start

```typescript
const { AgentPlatformSDK } = require('agent-platform-sdk');

const client = new AgentPlatformSDK({
  apiKey: process.env.API_KEY,
  baseURL: 'https://api.agent-platform.com/v1'
});

async function main() {
  try {
    const result = await client.getData();
    console.log('Success:', result);
  } catch (error) {
    console.error('Error:', error.message);
  }
}

main();
```

## Authentication

The Agent Platform SDK supports multiple authentication methods:

- **Bearer Token**: See [Authentication Guide](./AUTHENTICATION.md#bearer-token)
- **API Key**: See [Authentication Guide](./AUTHENTICATION.md#api-key)

For detailed authentication setup instructions, see [AUTHENTICATION.md](./AUTHENTICATION.md).

## Features

- ✅ agent_creation
- ✅ task_execution
- ✅ memory_management
- ✅ tool_integration
- ✅ conversation_handling
- ✅ workflow_automation

## API Reference

For comprehensive API documentation, see [API.md](./API.md).

### Quick API Overview

- `GET /agents - List all agents`
- `POST /agents - Create a new agent`
- `GET /agents/{id} - Get agent details`
- `PUT /agents/{id} - Update an agent`
- `DELETE /agents/{id} - Delete an agent`


And 4 more endpoints...

## Examples

Check out [EXAMPLES.md](./EXAMPLES.md) for comprehensive usage examples including:

- Basic CRUD operations
- Advanced querying
- Error handling
- Pagination
- Real-world use cases

## Documentation

- [📚 API Reference](./API.md)
- [🔐 Authentication Guide](./AUTHENTICATION.md)  
- [💡 Examples & Tutorials](./EXAMPLES.md)
- [🚀 Migration Guide](./MIGRATION.md)

## Contributing

We welcome contributions! Please see our [Contributing Guide](./CONTRIBUTING.md) for details.

## Support

- 📖 [Documentation](./API.md)
- 🐛 [Report Issues](undefined/issues)
- 💬 [Discussions](undefined/discussions)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

Made with ❤️ by the Agent Platform SDK team
