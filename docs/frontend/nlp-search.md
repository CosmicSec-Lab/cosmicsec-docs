# NLP Search Page#

**Natural Language Search** — intent recognition search.

## Overview#

Search CosmicSec using natural language queries.

## Features#

- **Intent recognition** — understand what you're looking for#
- **Entity extraction** — pull out IPs, domains, CVEs#
- **Auto-suggestions** — as you type#
- **Search history** — revisit past searches#
- **Saved searches** — bookmark common queries#

## Usage#

```typescript#
import { NLPSearchPage } from '@/pages';#

<NLPSearchPage#
  placeholder="Search threats, scans, reports..."#
/>#
```

## Example Queries#

- "Show me recent ransomware attacks"#
- "Find vulnerabilities in Apache"#
- "List all high severity incidents"#
- "Show scan results for example.com"#
- "Find threats targeting IoT devices"#

## Integration#

- **WebSocket** — real-time results#
- **Search API** — `/api/search` endpoint#
- **Export** — save search results as PDF/JSON#
- **Sharing** — share search results with team#
