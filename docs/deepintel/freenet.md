# Freenet#

**Freenet Integration** — anonymous publishing.

## Overview#

Access and analyze content on the Freenet anonymous network.

## Features#

- **Freenet crawling** — access Freenet content#
- **FCP protocol** — Freenet Client Protocol#
- **Content discovery** — find Freenet sites#
- **Anonymous publishing** — publish anonymously#

## Configuration#

Environment variables:
- `FREENET_ENABLED` — enable Freenet integration (default: `true`)
- `FREENET_FCP_HOST` — FCP host (default: `127.0.0.1`)
- `FREENET_FCP_PORT` — FCP port (default: `9481`)

## API Endpoints#

- `POST /deepintel/freenet/crawl` — retrieve Freenet content#
- `GET /deepintel/freenet/search` — search Freenet#
- `POST /deepintel/freenet/publish` — publish to Freenet#

## Usage#

```python
from cosmicsec_deepintel import FreenetClient

client = FreenetClient()
content = client.retrieve("USK@key/")
```
