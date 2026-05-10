# CLI Usage Guide#

**Mastering the CosmicSec CLI.**

## Overview#

Learn how to use the CosmicSec command-line interface for security operations.

## Installation#

```bash#
pip install cosmicsec-cli#
# or#
npm install -g @cosmicsec/cli#
```

## Common Commands#

### Running a Scan#

```bash#
cosmicsec scan create --target example.com --type web#
```

### Checking Status#

```bash#
cosmicsec scan status --id scan_12345#
```

### Getting Threat Intel#

```bash#
cosmicsec intel lookup --target example.com#
```

### Managing Incidents#

```bash#
cosmicsec incident list#
cosmicsec incident create --title "Issue" --severity high#
```

## Configuration#

```bash#
# Set API key#
cosmicsec config set api_key "your_key"#

# Set endpoint#
cosmicsec config set endpoint "https://api.cosmicsec.com"#
```

## Output Formats#

```bash#
# JSON output#
cosmicsec scan list --format json#

# Table output#
cosmicsec scan list --format table#
```

## Authentication#

```bash#
# Login#
cosmicsec auth login#

# Check status#
cosmicsec auth status#
```
