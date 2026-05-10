# CLI Overview#

**CosmicSec CLI** — 15+ commands for security operations.

## Overview#

Command-line interface for CosmicSec platform management.

## Installation#

```bash#
pip install cosmicsec-cli#
# or#
npm install -g @cosmicsec/cli#
```

## Main Commands#

### Scan Commands#
```bash#
cosmicsec scan create --target example.com#
cosmicsec scan list#
cosmicsec scan status --id scan_12345#
```

### Threat Intel#
```bash#
cosmicsec intel lookup --target example.com#
cosmicsec intel feed-list#
cosmicsec intel monitor --feed cisa-alerts#
```

### Incident Management#
```bash#
cosmicsec incident list#
cosmicsec incident create --title "Issue" --severity high#
cosmicsec incident update --id inc_123 --status resolved#
```

## Configuration#
```bash#
# Set API endpoint#
cosmicsec config set endpoint "https://api.cosmicsec.com"#

# Set API key#
cosmicsec config set api_key "your_key"#

# Check config#
cosmicsec config list#
```

## Output Formats#
```bash#
# JSON output#
cosmicsec scan list --format json#

# Table output#
cosmicsec scan list --format table#

# CSV output#
cosmicsec scan list --format csv#
```
