# Lokinet#

**Lokinet Integration** — mixnet protocol.

## Overview#

Access and analyze content on the Lokinet privacy network.

## Features#

- **Lokinet crawling** — access .loki sites#
- **SOCKS proxy** — route through Lokinet#
- **Service discovery** — find new .loki sites#
- **Traffic analysis** — analyze Lokinet traffic#

## Configuration#

Environment variables:
- `LOKINET_ENABLED` — enable Lokinet integration (default: `true`)
- `LOKINET_PROXY` — Lokinet SOCKS proxy (default: `socks5://127.0.0.1:9050`)
- `LOKINET_TIMEOUT` — request timeout (default: `30`)

## API Endpoints#

- `POST /deepintel/lokinet/crawl` — crawl .loki site#
- `GET /deepintel/lokinet/search` — search Lokinet#
- `POST /deepintel/lokinet/monitor` — monitor .loki site#

## Usage#

```python
from cosmicsec_deepintel import LokinetClient#

client = LokinetClient()
content = client.crawl("http://example.loki")
```
