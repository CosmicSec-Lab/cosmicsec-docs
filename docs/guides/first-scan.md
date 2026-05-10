# First Scan Guide#

**Run Your First Vulnerability Scan.**

## Overview#

Learn how to run your first security scan with CosmicSec.

## Prerequisites#

- CosmicSec platform running (docker-compose up -d)#
- API key or credentials set up#

## Step 1: Configure Target#

```python
from cosmicsec_sdk import CosmicSecClient#

client = CosmicSecClient(api_key="your-key")
```

## Step 2: Run Scan#

```python
result = client.scan.create(
    target="example.com",
    scan_type="web_application",
    options={"depth": 3}
)
```

## Step 3: Monitor Progress#

```python
status = client.scan.get_status(scan_id=result["scan_id"])
```

## Step 4: View Results#

```python
report = client.report.generate(scan_id=result["scan_id"])
```

## Tips#

- Start with `scan_type="light"` for quick results#
- Use `depth` to control crawl depth#
- Enable notifications for scan completion#
