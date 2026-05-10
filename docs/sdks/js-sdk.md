# 🟨 JavaScript SDK

## Overview

The **CosmicSec JavaScript SDK** is a browser-ready client library for the CosmicSec platform. It's a UMD bundle that works in browsers, Node.js, and React Native.

**Location:** `cosmicsec-sdk/sdk/javascript/`  
**NPM:** `@cosmicsec/sdk-js`

## Installation

### From NPM
```bash
npm install @cosmicsec/sdk-js
# or
yarn add @cosmicsec/sdk-js
```

### Browser (CDN)
```html
<script src="https://unpkg.com/@cosmicsec/sdk-js/dist/browser.min.js"></script>
<script>
  const client = new CosmicSec.SDK.Client({
    apiKey: 'cs_live_12345...'
  });
</script>
```

### From Source
```bash
git clone https://github.com/cosmicsec/cosmicsec-sdk.git
cd cosmicsec-sdk/sdk/javascript
npm install
npm run build
```

## Quick Start

### Node.js / Bundler (Webpack, Vite, etc.)
```javascript
const { CosmicSecClient } = require('@cosmicsec/sdk-js');
// or
import { CosmicSecClient } from '@cosmicsec/sdk-js';

// Initialize client
const client = new CosmicSecClient({
  apiKey: 'cs_live_12345...'
});

// Or login with credentials
const client = new CosmicSecClient();
await client.login({
  email: 'user@company.com',
  password: 'SecureP@ssw0rd'
});

// Use the client
const scans = await client.scans.list();
console.log(`Total scans: ${scans.length}`);
```

### Browser (Global)
```html
<script src="https://unpkg.com/@cosmicsec/sdk-js/dist/browser.min.js"></script>
<script>
  (async () => {
    const client = new CosmicSec.SDK.Client({
      apiKey: 'cs_live_12345...'
    });
    
    const scans = await client.scans.list();
    console.log(`Total scans: ${scans.length}`);
  })();
</script>
```

## Authentication

### API Key
```javascript
const client = new CosmicSecClient({
  apiKey: 'cs_live_12345...'
});
```

### Email/Password Login
```javascript
const client = new CosmicSecClient();
await client.login({
  email: 'user@company.com',
  password: 'SecureP@ssw0rd',
  totpCode: '123456'  // If 2FA enabled
});
```

## Scans

### List Scans
```javascript
// All scans
const scans = await client.scans.list();

// Filter by status
const completedScans = await client.scans.list({
  status: 'completed'
});

// Pagination
const scans = await client.scans.list({
  limit: 10,
  offset: 0
});
```

### Create Scan
```javascript
const scan = await client.scans.create({
  target: 'example.com',
  scanType: 'web_app',
  tools: ['nmap', 'nuclei', 'nikto'],
  options: {
    ports: '1-65535',
    rateLimit: 100
  }
});

console.log(`Scan created: ${scan.scanId}`);
```

### Get Results
```javascript
const results = await client.scans.getResults('scan_12345');
console.log(`Found ${results.vulnerabilities.length} vulnerabilities`);

results.vulnerabilities.forEach((vuln) => {
  console.log(`[${vuln.severity.toUpperCase()}] ${vuln.title}`);
});
```

## AI Analysis

### Analyze Vulnerability
```javascript
const analysis = await client.ai.analyze({
  content: 'SQL injection vulnerability...',
  analysisType: 'vulnerability'
});

console.log(`Severity: ${analysis.severity}`);
console.log(`Summary: ${analysis.summary}`);
```

### AI Chat (WebSocket)
```javascript
const chat = client.ai.chat();

// Send message
await chat.send('How do I fix SQL injection?');

// Receive response
chat.on('message', (response) => {
  console.log(response.response);
});

// Close
chat.close();
```

## Error Handling

```javascript
const { CosmicSecError, AuthenticationError, RateLimitError } = require('@cosmicsec/sdk-js');

try {
  const scan = await client.scans.get('invalid_id');
} catch (error) {
  if (error instanceof NotFoundError) {
    console.log('Scan not found');
  } else if (error instanceof RateLimitError) {
    console.log(`Rate limited. Retry after ${error.retryAfter}s`);
  } else if (error instanceof CosmicSecError) {
    console.log(`Error: ${error.message}`);
  }
}
```

## React Native

The SDK works with React Native:

```javascript
// App.js
import { CosmicSecClient } from '@cosmicsec/sdk-js';

export default function App() {
  const [scans, setScans] = useState([]);
  
  useEffect(() => {
    async function fetchScans() {
      const client = new CosmicSecClient({
        apiKey: 'cs_live_...'
      });
      const data = await client.scans.list();
      setScans(data);
    }
    fetchScans();
  }, []);
  
  return (
    <View>
      <Text>Total scans: {scans.length}</Text>
    </View>
  );
}
```

## Configuration

```javascript
const client = new CosmicSecClient({
  apiKey: 'cs_live_...',
  baseUrl: 'https://api.cosmicsec.com',
  timeout: 30000,
  maxRetries: 3,
  retryDelay: 1000
});
```

## Browser Compatibility

| Browser | Version |
|---------|---------|
| Chrome  | 90+     |
| Firefox | 88+     |
| Safari   | 14+     |
| Edge     | 90+     |
| IE       | Not supported |

## Next Steps

- [TypeScript SDK](./typescript-sdk.md)
- [Python SDK](./python-sdk.md)
- [Go SDK](./go-sdk.md)
- [API Reference](../../dev/api-docs.md)
