# 📘 TypeScript SDK

## Overview

The **CosmicSec TypeScript SDK** provides type-safe, Promise-based access to the CosmicSec platform. Built with Zod for runtime validation and supports WebSocket for real-time features.

**Location:** `cosmicsec-sdk/sdk/typescript/`  
**NPM:** `@cosmicsec/sdk`

## Installation

### From NPM
```bash
npm install @cosmicsec/sdk
# or
yarn add @cosmicsec/sdk
# or
pnpm add @cosmicsec/sdk
```

### From Source
```bash
git clone https://github.com/cosmicsec/cosmicsec-sdk.git
cd cosmicsec-sdk/sdk/typescript
npm install
npm run build
```

## Quick Start

```typescript
import { CosmicSecClient } from '@cosmicsec/sdk';

// Initialize client
const client = new CosmicSecClient({
  apiKey: 'cs_live_12345...',
  baseUrl: 'https://api.cosmicsec.com'  // Optional
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

## Authentication

### API Key (Recommended)
```typescript
const client = new CosmicSecClient({
  apiKey: 'cs_live_12345...'
});
```

### Email/Password Login
```typescript
const client = new CosmicSecClient();
await client.login({
  email: 'user@company.com',
  password: 'SecureP@ssw0rd',
  totpCode: '123456'  // If 2FA enabled
});
```

### SSO Token
```typescript
const client = new CosmicSecClient({
  apiKey: '<token-from-sso-flow>'
});
```

## Scans

### List Scans
```typescript
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
```typescript
const scan = await client.scans.create({
  target: 'example.com',
  scanType: 'web_app',
  tools: ['nmap', 'nuclei', 'nikto'],
  options: {
    ports: '1-65535',
    rateLimit: 100
  },
  schedule: {
    type: 'recurring',
    cron: '0 2 * * *'
  }
});

console.log(`Scan created: ${scan.scanId}`);
console.log(`Status: ${scan.status}`);
```

### Get Scan Status
```typescript
const scan = await client.scans.get('scan_12345');
console.log(`Progress: ${scan.progress}%`);
console.log(`Vulnerabilities found: ${scan.vulnerabilitiesCount}`);
```

### Cancel Scan
```typescript
await client.scans.cancel('scan_12345');
console.log('Scan cancelled');
```

### Get Results
```typescript
const results = await client.scans.getResults('scan_12345');
console.log(`Found ${results.vulnerabilities.length} vulnerabilities`);

results.vulnerabilities.forEach((vuln) => {
  console.log(`[${vuln.severity.toUpperCase()}] ${vuln.title}`);
  console.log(`  CVE: ${vuln.cve}`);
  console.log(`  CVSS: ${vuln.cvssScore}`);
});
```

### Wait for Completion
```typescript
const scan = await client.scans.create({
  target: 'example.com',
  scanType: 'network'
});

// Poll until complete
const completedScan = await client.scans.wait(scan.scanId, {
  timeout: 3600000,  // 1 hour
  interval: 5000      // Check every 5 seconds
});

console.log('Scan completed!');
const results = await client.scans.getResults(completedScan.scanId);
```

## AI Analysis

### Analyze Vulnerability
```typescript
const analysis = await client.ai.analyze({
  content: 'SQL injection vulnerability in login form...',
  analysisType: 'vulnerability',
  context: {
    target: 'example.com'
  }
});

console.log(`Severity: ${analysis.severity}`);
console.log(`Summary: ${analysis.summary}`);
console.log('Recommendations:');
analysis.recommendations.forEach((rec) => console.log(`  - ${rec}`));
```

### Multi-Model Ensemble
```typescript
const consensus = await client.ai.ensembleAnalyze({
  content: 'Analyze this critical vulnerability...',
  models: ['gpt-4o', 'claude-3-opus', 'llama-3.1-70b'],
  strategy: 'weighted_vote'
});

console.log(`Consensus severity: ${consensus.consensus.severity}`);
console.log(`Confidence: ${consensus.consensus.confidence}`);
```

### AI Chat (WebSocket Real-Time)
```typescript
const chat = client.ai.chat();

// Send message
await chat.send('How do I fix a SQL injection vulnerability?');

// Receive response
chat.on('message', (response) => {
  console.log(response.response);
  console.log('\nSuggestions:');
  response.suggestions.forEach((s: string) => console.log(`  - ${s}`));
});

// Close when done
chat.close();
```

### Threat Hunting
```typescript
const results = await client.ai.threatHunting({
  query: 'Show me all critical vulnerabilities in web applications'
});

console.log(results.summary);
results.results.forEach((vuln) => {
  console.log(`  - ${vuln.title} (${vuln.severity})`);
});
```

### Submit Feedback (For Learning)
```typescript
await client.ai.submitFeedback({
  analysisId: 'analysis_001',
  rating: 5,  // 1-5 stars
  correct: true,
  feedback: 'Excellent analysis!'
});
```

## Reports

### Generate Report
```typescript
const report = await client.reports.generate({
  scanId: 'scan_12345',
  reportType: 'executive',  // executive, technical, compliance
  format: 'pdf'  // pdf, html, json, csv
});

console.log(`Report generated: ${report.reportId}`);
console.log(`Download URL: ${report.downloadUrl}`);
```

### List Reports
```typescript
const reports = await client.reports.list({ limit: 10 });
reports.forEach((report) => {
  console.log(`${report.reportId} - ${report.createdAt}`);
});
```

### Download Report
```typescript
await client.reports.download('report_12345', {
  outputPath: './reports/'
});
```

## Error Handling

```typescript
import {
  CosmicSecError,
  AuthenticationError,
  RateLimitError,
  NotFoundError,
  ServerError
} from '@cosmicsec/sdk';

try {
  const scan = await client.scans.get('invalid_id');
} catch (error) {
  if (error instanceof NotFoundError) {
    console.log('Scan not found');
  } else if (error instanceof RateLimitError) {
    console.log(`Rate limited. Retry after ${error.retryAfter} seconds`);
  } else if (error instanceof AuthenticationError) {
    console.log('Invalid API key');
  } else if (error instanceof ServerError) {
    console.log(`Server error: ${error.message}`);
  } else if (error instanceof CosmicSecError) {
    console.log(`General error: ${error.message}`);
  }
}
```

## WebSocket Support

### Real-Time Scan Updates
```typescript
const ws = client.scans.subscribe('scan_12345');

ws.on('progress', (data) => {
  console.log(`Progress: ${data.progress}%`);
});

ws.on('vulnerability_found', (vuln) => {
  console.log(`New vulnerability: ${vuln.title}`);
});

ws.on('completed', () => {
  console.log('Scan completed!');
  ws.close();
});
```

### Real-Time Notifications
```typescript
const notifications = client.notifications.subscribe();

notifications.on('notification', (notif) => {
  console.log(`[${notif.severity.toUpperCase()}] ${notif.title}`);
  console.log(`  ${notif.message}`);
});
```

## Configuration

### Client Options
```typescript
const client = new CosmicSecClient({
  apiKey: 'cs_live_...',
  baseUrl: 'https://api.cosmicsec.com',
  timeout: 30000,          // Request timeout in ms
  maxRetries: 3,          // Retry failed requests
  retryDelay: 1000,        // Initial retry delay
  retryBackoff: 2.0,       // Exponential backoff
  websocketUrl: 'wss://ws.cosmicsec.com'
});
```

### Environment Variables
```bash
export COSMICSEC_API_KEY=cs_live_...
export COSMICSEC_BASE_URL=https://api.cosmicsec.com
export COSMICSEC_TIMEOUT=30000
export COSMICSEC_MAX_RETRIES=3
```

## Type Safety (Zod Validation)

All responses are validated with Zod schemas:

```typescript
import { z } from 'zod';
import { ScanSchema } from '@cosmicsec/sdk/schemas';

// TypeScript infers type from schema
type Scan = z.infer<typeof ScanSchema>;

// Runtime validation
const scan = ScanSchema.parse(response);  // Throws if invalid
const scanSafe = ScanSchema.safeParse(response);  // Returns result object
```

## Browser Usage

The SDK is browser-ready:

```html
<script type="module">
  import { CosmicSecClient } from 'https://unpkg.com/@cosmicsec/sdk/dist/browser.min.js';
  
  const client = new CosmicSecClient({
    apiKey: 'cs_live_...'
  });
  
  const scans = await client.scans.list();
  console.log(scans);
</script>
```

## Next Steps

- [Python SDK](../python-sdk.md)
- [Go SDK](../go-sdk.md)
- [JavaScript SDK](../js-sdk.md)
- [API Reference](../../dev/api-docs.md)
- [Examples on GitHub](https://github.com/cosmicsec/cosmicsec-sdk/examples)
