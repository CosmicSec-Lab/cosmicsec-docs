# API Integration Guide#

**Connecting Your Applications to CosmicSec.**

## Overview#

Learn how to integrate CosmicSec APIs into your applications.

## Authentication#

```python#
from cosmicsec_sdk import CosmicSecClient#

client = CosmicSecClient(
    api_key="your_api_key",
    base_url="https://api.cosmicsec.com"
)
```

## Common Operations#

### Creating a Scan#

```python#
scan = client.scan.create(
    target="example.com",
    scan_type="web_application"
)
```

### Getting Threat Intel#

```python#
intel = client.intel.get_threats(
    target="example.com"
)
```

### Managing Incidents#

```python#
incident = client.incidents.create(
    title="Suspicious activity",
    severity="high"
)
```

## Webhook Integration#

```python#
webhook = client.webhooks.create(
    url="https://your-app.com/webhook",
    events=["scan.completed", "threat.detected"]
)
```

## Error Handling#

```python#
try:
    result = client.scan.create(target="...")
except CosmicSecError as e:
    print(f"API error: {e}")
```
