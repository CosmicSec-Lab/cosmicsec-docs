# Hyperboria#

**Hyperboria Integration** — mesh network.

## Overview#

Access and analyze content on the Hyperboria mesh network (Cjdns-based).

## Features#

- **Hyperboria crawling** — access Hyperboria sites#
- **Mesh routing** — decentralized routing#
- **Content discovery** — find Hyperboria resources#
- **Peer discovery** — automatic peer finding#

## Configuration#

Environment variables:
- `HYPERBORIA_ENABLED` — enable Hyperboria integration (default: `true`)
- `HYPERBORIA_HOST` — Hyperboria host (default: `127.0.0.1`)
- `HYPERBORIA_PORT` — Hyperboria port (default: `4453`)

## API Endpoints#

- `POST /deepintel/hyperboria/crawl` — crawl Hyperboria site#
- `GET /deepintel/hyperboria/search` — search Hyperboria#
- `POST /deepintel/hyperboria/monitor` — monitor Hyperboria site#

## Usage#

```python
from cosmicsec_deepintel import HyperboriaClient#

client = HyperboriaClient()
content = client.crawl("http://example.hyperboria")
```
