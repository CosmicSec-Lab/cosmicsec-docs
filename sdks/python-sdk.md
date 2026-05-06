# 🐍 Python SDK

## Overview

The **CosmicSec Python SDK** provides a full-featured client library for interacting with the CosmicSec platform. Built with Pydantic for type safety, includes middleware, retry logic, and async support.

**Location:** `cosmicsec-sdk/sdk/python/`  
**PyPI:** `cosmicsec-sdk`

## Installation

### From PyPI
```bash
pip install cosmicsec-sdk
```

### From Source
```bash
git clone https://github.com/cosmicsec/cosmicsec-sdk.git
cd cosmicsec-sdk/sdk/python
pip install -e .
```

## Quick Start

```python
from cosmicsec import CosmicSecClient

# Initialize client
client = CosmicSecClient(
    api_key="cs_live_12345...",
    base_url="https://api.cosmicsec.com"  # Optional, defaults to production
)

# Or login with credentials
client = CosmicSecClient()
client.login(email="user@company.com", password="SecureP@ssw0rd")

# Use the client
scans = client.scans.list()
print(f"Total scans: {len(scans)}")
```

## Authentication

### API Key (Recommended for automation)
```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_12345...")
```

### Email/Password Login
```python
client = CosmicSecClient()
client.login(
    email="user@company.com",
    password="SecureP@ssw0rd",
    totp_code="123456"  # If 2FA enabled
)
```

### SSO Login
```python
# For SSO, use the web flow and pass the token
client = CosmicSecClient(
    api_key="<token-from-sso-flow>"
)
```

## Scans

### List Scans
```python
# All scans
scans = client.scans.list()

# Filter by status
completed_scans = client.scans.list(status="completed")

# Pagination
scans = client.scans.list(limit=10, offset=0)
```

### Create Scan
```python
scan = client.scans.create(
    target="example.com",
    scan_type="web_app",
    tools=["nmap", "nuclei", "nikto"],
    options={
        "ports": "1-65535",
        "rate_limit": 100
    },
    schedule={
        "type": "recurring",
        "cron": "0 2 * * *"
    }
)

print(f"Scan created: {scan.scan_id}")
print(f"Status: {scan.status}")
```

### Get Scan Status
```python
scan = client.scans.get("scan_12345")
print(f"Progress: {scan.progress}%")
print(f"Vulnerabilities found: {scan.vulnerabilities_count}")
```

### Cancel Scan
```python
client.scans.cancel("scan_12345")
print("Scan cancelled")
```

### Get Results
```python
results = client.scans.get_results("scan_12345")
print(f"Found {len(results.vulnerabilities)} vulnerabilities")

for vuln in results.vulnerabilities:
    print(f"[{vuln.severity.upper()}] {vuln.title}")
    print(f"  CVE: {vuln.cve}")
    print(f"  CVSS: {vuln.cvss_score}")
```

### Wait for Completion
```python
scan = client.scans.create(target="example.com", scan_type="network")
scan.wait()  # Blocks until scan completes
print("Scan completed!")
results = scan.get_results()
```

## AI Analysis

### Analyze Vulnerability
```python
analysis = client.ai.analyze(
    content="SQL injection vulnerability in login form...",
    analysis_type="vulnerability",
    context={"target": "example.com"}
)

print(f"Severity: {analysis.severity}")
print(f"Summary: {analysis.summary}")
print("Recommendations:")
for rec in analysis.recommendations:
    print(f"  - {rec}")
```

### Multi-Model Ensemble
```python
consensus = client.ai.ensemble_analyze(
    content="Analyze this critical vulnerability...",
    models=["gpt-4o", "claude-3-opus", "llama-3.1-70b"],
    strategy="weighted_vote"
)

print(f"Consensus severity: {consensus['consensus']['severity']}")
print(f"Confidence: {consensus['consensus']['confidence']}")
```

### AI Chat
```python
response = client.ai.chat(
    message="How do I fix a SQL injection vulnerability?",
    context={"user_id": "user_123"}
)

print(response.response)
print("\nSuggestions:")
for suggestion in response.suggestions:
    print(f"  - {suggestion}")
```

### Threat Hunting
```python
results = client.ai.threat_hunting(
    query="Show me all critical vulnerabilities in web applications"
)

print(results.summary)
for vuln in results.results:
    print(f"  - {vuln.title} ({vuln.severity})")
```

### Submit Feedback (For Learning)
```python
client.ai.submit_feedback(
    analysis_id="analysis_001",
    rating=5,  # 1-5 stars
    correct=True,
    feedback="Excellent analysis!"
)
```

## Reports

### Generate Report
```python
report = client.reports.generate(
    scan_id="scan_12345",
    report_type="executive",  # executive, technical, compliance
    format="pdf"  # pdf, html, json, csv
)

print(f"Report generated: {report.report_id}")
print(f"Download URL: {report.download_url}")
```

### List Reports
```python
reports = client.reports.list(limit=10)
for report in reports:
    print(f"{report.report_id} - {report.created_at}")
```

### Download Report
```python
client.reports.download(
    report_id="report_12345",
    output_path="./reports/"
)
```

## Threat Intelligence

### Search Threats
```python
threats = client.threat_intel.search(
    query="ransomware",
    severity="critical",
    limit=50
)

for threat in threats:
    print(f"{threat.threat_type}: {threat.description}")
```

### Get Threat Details
```python
threat = client.threat_intel.get("threat_12345")
print(f"Type: {threat.type}")
print(f"Severity: {threat.severity}")
print(f"MITRE: {threat.mitre_id}")
```

## User & Organization Management

### Get Profile
```python
profile = client.auth.get_profile()
print(f"Logged in as: {profile.email}")
print(f"Role: {profile.role}")
```

### List Users (Admin)
```python
users = client.admin.list_users(role="analyst")
for user in users:
    print(f"{user.email} - {user.role}")
```

### Create User (Admin)
```python
user = client.admin.create_user(
    email="newuser@company.com",
    name="New User",
    role="analyst",
    organization="acme-corp"
)
print(f"User created: {user.user_id}")
```

### Invite to Organization
```python
invitation = client.org.invite_user(
    email="colleague@company.com",
    role="analyst"
)
print(f"Invitation sent: {invitation.invite_id}")
```

## Async Support

```python
import asyncio
from cosmicsec import AsyncCosmicSecClient

async def main():
    client = AsyncCosmicSecClient(api_key="cs_live_...")
    
    # Create multiple scans concurrently
    tasks = [
        client.scans.create(target=f"target{i}.com", scan_type="web_app")
        for i in range(5)
    ]
    scans = await asyncio.gather(*tasks)
    print(f"Created {len(scans)} scans")

asyncio.run(main())
```

## Error Handling

```python
from cosmicsec.exceptions import (
    CosmicSecError,
    AuthenticationError,
    RateLimitError,
    NotFoundError,
    ServerError
)

try:
    scan = client.scans.get("invalid_id")
except NotFoundError:
    print("Scan not found")
except RateLimitError as e:
    print(f"Rate limited. Retry after {e.retry_after} seconds")
except AuthenticationError:
    print("Invalid API key")
except ServerError as e:
    print(f"Server error: {e}")
except CosmicSecError as e:
    print(f"General error: {e}")
```

## Retry Logic

The SDK includes automatic retry with exponential backoff:

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(
    api_key="cs_live_...",
    max_retries=3,          # Retry up to 3 times
    retry_delay=1.0,        # Initial delay 1 second
    retry_backoff=2.0,      # Double delay each retry
    timeout=30.0            # Request timeout
)
```

## Middleware

### Custom Middleware
```python
from cosmicsec.middleware import Middleware

class LoggingMiddleware(Middleware):
    def before_request(self, request):
        print(f"Request: {request.method} {request.url}")
    
    def after_request(self, response):
        print(f"Response: {response.status_code}")

client = CosmicSecClient(
    api_key="cs_live_...",
    middleware=[LoggingMiddleware()]
)
```

### Built-in Middleware
- **RetryMiddleware** — Automatic retries
- **AuthMiddleware** — Authentication handling
- **LoggingMiddleware** — Request/response logging
- **MetricsMiddleware** — Prometheus metrics

## Type Safety (Pydantic)

All responses are Pydantic models:

```python
from cosmicsec.models import Scan, Vulnerability

scan: Scan = client.scans.get("scan_12345")
print(scan.scan_id)  # IDE autocomplete works!

for vuln in scan.vulnerabilities:
    vuln: Vulnerability = vuln
    print(vuln.severity)  # Type-checked
```

## Configuration

### Environment Variables
```bash
export COSMICSEC_API_KEY=cs_live_...
export COSMICSEC_BASE_URL=https://api.cosmicsec.com
export COSMICSEC_TIMEOUT=30
export COSMICSEC_MAX_RETRIES=3
```

### Config File
```python
from cosmicsec import CosmicSecClient, Config

config = Config(
    api_key="cs_live_...",
    base_url="https://api.cosmicsec.com",
    timeout=30.0,
    max_retries=3
)

client = CosmicSecClient(config=config)
```

## Testing

```python
import pytest
from cosmicsec import CosmicSecClient
from cosmicsec.testing import MockClient

def test_scan_creation():
    # Use mock client for testing
    client = MockClient()
    scan = client.scans.create(target="example.com")
    assert scan.scan_id is not None
    assert scan.status == "queued"
```

## Next Steps

- [TypeScript SDK](../typescript-sdk.md)
- [Go SDK](../go-sdk.md)
- [JavaScript SDK](../js-sdk.md)
- [API Reference](../dev/api-docs.md)
- [Examples on GitHub](https://github.com/cosmicsec/cosmicsec-sdk/examples)
