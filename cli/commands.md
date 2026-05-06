# CLI Commands#

**15+ CLI Commands** — complete command reference.

## Overview#

Detailed reference for all CosmicSec CLI commands.

## Scan Commands#

### `scan create`#
```bash#
cosmicsec scan create --target example.com --type web#
```
Creates a new vulnerability scan.

Options:
- `--target` — target URL/IP (required)#
- `--type` — scan type: `web`, `network`, `api`, `cloud`#
- `--depth` — crawl depth (default: `3`)#
- `--options` — JSON string of additional options#

### `scan list`#
```bash#
cosmicsec scan list --status running#
```

### `scan status`#
```bash#
cosmicsec scan status --id scan_12345#
```

## Intel Commands#

### `intel lookup`#
```bash#
cosmicsec intel lookup --target example.com#
```

### `intel feed-list`#
```bash#
cosmicsec intel feed-list#
```

## Incident Commands#

### `incident list`#
```bash#
cosmicsec incident list --severity high#
```

### `incident create`#
```bash#
cosmicsec incident create --title "Issue" --severity high#
```

## Configuration Commands#

### `config set`#
```bash#
cosmicsec config set api_key "your_key"#
```

### `config list`#
```bash#
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
