# Cjdns#

**Cjdns Integration** — encrypted IPv6 mesh.

## Overview#

Access and analyze content on the Cjdns encrypted mesh network (.kcpsp).

## Features#

- **Cjdns crawling** — access .kcpsp sites#
- **IPv6 mesh routing** — Cjdns's routing protocol#
- **Encryption** — automatic end-to-end encryption#
- **Content discovery** — find Cjdns resources#

## Configuration#

Environment variables:
- `CJDNS_ENABLED` — enable Cjdns integration (default: `true`)
- `CJDNS_HOST` — Cjdns host (default: `127.0.0.1`)
- `CJDNS_PORT` — Cjdns port (default: `4453`)

## API Endpoints#

- `POST /deepintel/cjdns/crawl` — crawl .kcpsp site#
- `GET /deepintel/cjdns/search` — search Cjdns#
- `POST /deepintel/cjdns/monitor` — monitor Cjdns site#

## Usage#

```python
from cosmicsec_deepintel import CjdnsClient#

client = CjdnsClient()
content = client.crawl("http://example.kcpsp")
```
