# SDK Integration Guide#

**Using CosmicSec SDKs.**

## Overview#

Learn how to integrate CosmicSec SDKs into your applications.

## Python SDK#

```bash#
pip install cosmicsec-sdk#
```

```python#
from cosmicsec_sdk import CosmicSecClient#

client = CosmicSecClient(api_key="your_key")
result = client.scan.create(target="example.com")
```

## TypeScript/JavaScript SDK#

```bash#
npm install @cosmicsec/sdk#
```

```typescript#
import { CosmicSecClient } from '@cosmicsec/sdk';#

const client = new CosmicSecClient({
  apiKey: 'your_key'
});
const result = await client.scan.create({ target: 'example.com' });
```

## Go SDK#

```bash#
go get github.com/cosmicsec/cosmicsec-sdk/go#
```

```go#
import "github.com/cosmicsec/cosmicsec-sdk/go"#

client := cosmicsec.NewClient("your_key")
result, err := client.Scan.Create(context.Background(), "example.com")
```

## Common Patterns#

### Error Handling#
```python#
try:
    result = client.scan.create(target="...")
except CosmicSecError as e:
    print(f"Error: {e}")
```

### Pagination#
```python#
results = client.intel.list(page=1, limit=50)
```

### Webhooks#
```python#
client.webhooks.create(
    url="https://your-app.com/webhook",
    events=["scan.completed"]
)
```
