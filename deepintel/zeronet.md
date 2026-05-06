# ZeroNet#

**ZeroNet Integration** — decentralized websites.

## Overview#

Access and crawl ZeroNet sites (.bit) for intelligence.

## Features#

- **.bit site crawling** — access ZeroNet websites#
- **ZeroNet proxy support** — route through ZeroNet#
- **Site discovery** — find new .bit sites#
- **Content analysis** — analyze ZeroNet-hosted content#

## Configuration#

Environment variables:
- `ZERONET_ENABLED` — enable ZeroNet integration (default: `true`)
- `ZERONET_PROXY` — ZeroNet proxy (default: `http://127.0.0.1:43110`)
- `ZERONET_TIMEOUT` — request timeout (default: `30`)

## API Endpoints#

- `POST /deepintel/zeronet/crawl` — crawl .bit site#
- `GET /deepintel/zeronet/search` — search ZeroNet content#
- `POST /deepintel/zeronet/monitor` — monitor .bit site#

## Usage#

```python
from cosmicsec_deepintel import ZeroNetClient

client = ZeroNetClient()
content = client.crawl("http://example.bit")
```
