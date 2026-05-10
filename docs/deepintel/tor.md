# Tor Network#

**Tor Network Integration** — .onion crawling and monitoring.

## Overview#

Connects to the Tor network for dark web intelligence gathering.

## Features#

- **.onion crawling** — access Tor hidden services#
- **Tor proxy support** — route through Tor#
- **Hidden service discovery** — find new .onion sites#
- **Content analysis** — analyze Tor-hosted content#
- **Anonymous queries** — Tor-based OSINT#

## Configuration#

Environment variables:
- `TOR_ENABLED` — enable Tor integration (default: `true`)
- `TOR_PROXY` — Tor SOCKS proxy (default: `socks5://127.0.0.1:9050`)
- `TOR_TIMEOUT` — request timeout (default: `30`)

## API Endpoints#

- `POST /deepintel/tor/crawl` — crawl .onion site#
- `GET /deepintel/tor/search` — search Tor content#
- `POST /deepintel/tor/monitor` — monitor .onion site#
- `GET /deepintel/tor/discovered` — list discovered services#

## Usage#

```python
from cosmicsec_deepintel import TorClient#

client = TorClient()
content = client.crawl("http://example.onion")
```
