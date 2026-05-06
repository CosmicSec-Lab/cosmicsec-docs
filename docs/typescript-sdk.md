# TypeScript SDK Documentation

The CosmicSec TypeScript SDK provides a premium, type-safe client for web applications and Node.js backends.

## Installation

```bash
# npm
npm install @cosmicsec/sdk-ts

# yarn
yarn add @cosmicsec/sdk-ts

# pnpm
pnpm add @cosmicsec/sdk-ts
```

## Quick Start

```typescript
import { CosmicSecClient } from '@cosmicsec/sdk-ts';

// Initialize client
const client = new CosmicSecClient({
  apiKey: 'your-api-key',
  baseUrl: 'https://api.cosmicsec.com',
  environment: 'production'
});

// Test connection
const health = await client.health();
console.log(health);
```

## React Hook

```tsx
import { useCosmicSec } from '@cosmicsec/sdk-ts';

function App() {
  const { client, health, loading, createScan } = useCosmicSec({
    apiKey: 'your-api-key'
  });

  if (loading) return <div>Loading...</div>;

  return (
    <div>
      <h1>Status: {health.status}</h1>
      <button onClick={() => createScan({ target: 'example.com' })}>
        Start Scan
      </button>
    </div>
  );
}
```

## Advanced Features

### 🔄 Smart Caching

```typescript
// Cache is enabled by default with TTL and LRU eviction
const client = new CosmicSecClient({
  enableCache: true,
  cacheTTL: 300000, // 5 minutes
  cacheMaxSize: 1000
});

// GET requests are automatically cached
const dashboard1 = await client.getDashboard(); // Fetches from API
const dashboard2 = await client.getDashboard(); // Returns from cache
```

### 🛡️ Circuit Breaker & Retry

```typescript
const client = new CosmicSecClient({
  enableRetry: true,
  maxRetries: 3,
  retryBackoffFactor: 0.5,
  enableCircuitBreaker: true
});
```

### 🔐 HMAC Request Signing

```typescript
const client = new CosmicSecClient({
  apiKey: 'your-key',
  hmacSecret: 'your-secret',
  requestSigning: true
});
```

### 📡 WebSocket Support

```typescript
const client = new CosmicSecClient();

// Connect to real-time events
const ws = client.connectWebSocket('/events/scan-updates');

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log('Event:', data);
};

ws.onerror = (error) => {
  console.error('WebSocket error:', error);
};
```

### 🚀 Batch Operations

```typescript
// Bulk scan multiple targets
const result = await client.bulkScan(
  ['example1.com', 'example2.com', '192.168.1.0/24'],
  'nmap'
);
console.log(`Bulk scan ID: ${result.bulk_scan_id}`);
```

### 🔄 Async Iterator (Pagination)

```typescript
// Iterate through all scans with automatic pagination
for await (const scan of client.scanIterator(50)) {
  console.log(`Scan: ${scan.scan_id} - ${scan.status}`);
}
```

## API Reference

### Client Configuration

```typescript
interface SDKConfig {
  baseUrl?: string;
  environment?: 'development' | 'staging' | 'production';
  timeout?: number;
  maxRetries?: number;
  retryBackoffFactor?: number;
  cacheTTL?: number;
  enableCache?: boolean;
  enableRetry?: boolean;
  authMethod?: 'bearer' | 'api-key' | 'hmac';
  apiKey?: string;
  hmacSecret?: string;
  logLevel?: 'debug' | 'info' | 'warn' | 'error';
}
```

### Scans API

```typescript
// Create scan
const scan = await client.createScan({
  target: 'example.com',
  scanTypes: ['nmap', 'nuclei'],
  tool: 'custom-tool',
  timeout: 300
});

// Get scan details
const scan = await client.getScan('scan-123');

// List scans
const scans = await client.listScans({ limit: '10', offset: '0' });
```

### Security Operations

```typescript
// Get metrics
const metrics = await client.getMetrics('security');

// Get dashboard
const dashboard = await client.getDashboard();

// Create incident
const incident = await client.createIncident({
  title: 'Suspicious Activity',
  description: 'Multiple failed login attempts',
  category: 'authentication',
  severity: 'high'
});

// Create vulnerability
const vuln = await client.createVulnerability({
  title: 'CVE-2024-1234',
  description: 'Remote code execution',
  severity: 'critical',
  cveId: 'CVE-2024-1234'
});
```

### AI Features

```typescript
// AI analysis
const analysis = await client.analyze({
  target: 'example.com',
  type: 'comprehensive'
});

// Chat with security copilot
const response = await client.chat('How do I mitigate CVE-2024-1234?');
```

## Type Safety with Zod

```typescript
import { z } from 'zod';

// All requests are validated with Zod schemas
const ScanRequestSchema = z.object({
  target: z.string(),
  scanTypes: z.array(z.string()).optional(),
  tool: z.string().optional(),
  timeout: z.number().optional()
});

// Type inference
type ScanRequest = z.infer<typeof ScanRequestSchema>;
```

## Middleware Pipeline

```typescript
import { Middleware } from '@cosmicsec/sdk-ts';

// Custom middleware
const customMiddleware: Middleware = async (request) => {
  console.log(`Request: ${request.method} ${request.url}`);
  return request;
};

client.middlewares.add(customMiddleware);
```

## Error Handling

```typescript
try {
  const scan = await client.createScan(payload);
} catch (error) {
  if (error.message.includes('Circuit breaker is open')) {
    console.error('Service temporarily unavailable');
  } else if (error.message.includes('Rate limit exceeded')) {
    console.error('Rate limited - try again later');
  } else {
    console.error('API error:', error.message);
  }
}
```

## Utility Methods

```typescript
// Clear cache
await client.clearCache();

// Export/Import config
const configJson = client.exportConfig();
client.importConfig(configJson);

// Get current config
const config = client.getConfig();
```

## Testing

```bash
# Run tests
npm test

# With coverage
npm run test:coverage

# Watch mode
npm run test:watch
```

## Examples

Check out the [examples directory](https://github.com/cosmicsec/cosmicsec-sdk/tree/main/sdk/ts/examples):

- `react-app.tsx` - React integration with hooks
- `node-cli.ts` - Node.js CLI application
- `websocket-listener.ts` - Real-time event streaming
- `bulk-operations.ts` - Batch processing example

## Browser Support

The SDK works in all modern browsers. For older browsers, you may need polyfills:

```html
<script src="https://polyfill.io/v3/polyfill.min.js?features=es2015,es2016,es2017"></script>
<script src="node_modules/@cosmicsec/sdk-ts/dist/index.umd.js"></script>
<script>
  const client = new CosmicSec.CosmicSecClient({ apiKey: 'your-key' });
</script>
```

## Support

- **Documentation**: [docs.cosmicsec.com/sdks/typescript](https://docs.cosmicsec.com/sdks/typescript)
- **Issues**: [GitHub Issues](https://github.com/cosmicsec/cosmicsec-sdk/issues)
- **Discord**: [Join our community](https://discord.gg/cosmicsec)
