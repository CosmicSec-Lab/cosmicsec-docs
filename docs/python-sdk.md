# Python SDK Documentation

The CosmicSec Python SDK provides a premium, enterprise-grade client for interacting with the CosmicSec platform.

## Installation

```bash
# Basic installation
pip install cosmicsec-sdk

# With all optional dependencies
pip install cosmicsec-sdk[full]

# Development version
pip install git+https://github.com/cosmicsec/cosmicsec-sdk.git
```

## Quick Start

```python
from cosmicsec_sdk import CosmicSecClient

# Initialize client
client = CosmicSecClient(
    api_key="your-api-key",
    base_url="https://api.cosmicsec.com",
    environment="production"
)

# Test connection
health = client.health()
print(health)
```

## Advanced Features

### 🔄 Smart Caching

```python
from cosmicsec_sdk import RedisCache, FileCache

# Use Redis cache
client = CosmicSecClient(
    cache_backend="redis",
    redis_url="redis://localhost:6379"
)

# Or file-based cache
client = CosmicSecClient(
    cache_backend="file",
    cache_dir=".cosmicsec_cache"
)
```

### 🛡️ Circuit Breaker & Retry

```python
# Automatic retry with exponential backoff
client = CosmicSecClient(
    max_retries=5,
    retry_backoff_factor=0.5,
    enable_circuit_breaker=True
)
```

### 🔐 Request Signing (HMAC)

```python
client = CosmicSecClient(
    api_key="your-key",
    hmac_secret="your-secret",
    request_signing=True
)
```

### 📡 WebSocket Support

```python
import asyncio
from cosmicsec_sdk import CosmicSecClient

async def listen_events():
    client = CosmicSecClient()
    ws = await client.connect_websocket("/events/scan-updates")
    
    async for message in ws:
        print(f"Event: {message}")
```

### 🚀 Bulk Operations

```python
# Bulk scan multiple targets
targets = ["example1.com", "example2.com", "192.168.1.0/24"]
result = client.bulk_scan(targets, scan_type="nmap")
print(f"Bulk scan ID: {result['bulk_scan_id']}")
```

### 🔄 Pagination Iterator

```python
# Iterate through all scans with automatic pagination
for scan in client.scan_iterator(limit=50):
    print(f"Scan: {scan['scan_id']} - {scan['status']}")
```

## API Reference

### Scans

```python
# Create scan
scan = client.create_scan({
    "target": "example.com",
    "scan_types": ["nmap", "nuclei"],
    "tool": "custom-tool"
})

# Get scan details
scan = client.get_scan("scan-123")

# List scans
scans = client.list_scans(limit=10, offset=0)
```

### Security Operations

```python
# Get metrics
metrics = client.get_metrics(category="security")

# Get dashboard
dashboard = client.get_dashboard()

# Create incident
incident = client.create_incident({
    "title": "Suspicious Activity",
    "description": "Multiple failed login attempts",
    "category": "authentication",
    "severity": "high"
})

# Create vulnerability
vuln = client.create_vulnerability({
    "title": "CVE-2024-1234",
    "description": "Remote code execution vulnerability",
    "severity": "critical",
    "cve_id": "CVE-2024-1234"
})
```

### AI Features

```python
# Analyze target with AI
analysis = client.analyze("example.com", analysis_type="comprehensive")

# Chat with security copilot
response = client.chat("How do I mitigate CVE-2024-1234?")
```

## Models (Type Safety)

```python
from cosmicsec_sdk import ScanRequest, IncidentRequest

# Type-safe request construction
scan_req = ScanRequest(
    target="example.com",
    scan_types=["nmap"],
    timeout=300
)

scan = client.create_scan(scan_req)
```

## Middleware Pipeline

```python
from cosmicsec_sdk import AuthMiddleware, AuditMiddleware

# Custom middleware
class CustomMiddleware:
    def process_request(self, method, url, **kwargs):
        print(f"Request: {method} {url}")
        return kwargs
    
    def process_response(self, response):
        return response

client = CosmicSecClient()
client.add_middleware(CustomMiddleware())
```

## Error Handling

```python
from cosmicsec_sdk.exceptions import (
    CosmicSecError,
    RateLimitError,
    CircuitBreakerOpenError
)

try:
    scan = client.create_scan(payload)
except RateLimitError as e:
    print(f"Rate limited. Retry after: {e.retry_after}")
except CircuitBreakerOpenError:
    print("Circuit breaker is open. Service temporarily unavailable.")
except CosmicSecError as e:
    print(f"API error: {e.message}")
```

## Configuration Profiles

```python
import json

# Export config
config_json = client.export_config()
with open("cosmicsec-config.json", "w") as f:
    f.write(config_json)

# Import config
with open("cosmicsec-config.json", "r") as f:
    client.import_config(f.read())
```

## Testing

```bash
# Run tests
pytest tests/

# With coverage
pytest --cov=cosmicsec_sdk tests/

# Run specific test
pytest tests/test_client.py::TestCosmicSecClient::test_health -v
```

## Examples

Check out the [examples directory](https://github.com/cosmicsec/cosmicsec-sdk/tree/main/examples) for more use cases:

- `bulk_scan.py` - Bulk scanning with progress tracking
- `websocket_listener.py` - Real-time event streaming
- `custom_middleware.py` - Implementing custom middleware
- `multi_tenant.py` - Multi-tenant configuration

## Support

- **Documentation**: [docs.cosmicsec.com/sdks/python](https://docs.cosmicsec.com/sdks/python)
- **Issues**: [GitHub Issues](https://github.com/cosmicsec/cosmicsec-sdk/issues)
- **Discord**: [Join our community](https://discord.gg/cosmicsec)
