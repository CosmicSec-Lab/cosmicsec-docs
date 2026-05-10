# AnoNet#

**AnoNet Integration** — anonymous overlay network.

## Overview#

Access and analyze content on the AnoNet anonymous overlay network.

## Features#

- **AnoNet crawling** — access AnoNet resources#
- **Overlay routing** — AnoNet's routing protocol#
- **Content discovery** — find AnoNet sites#
- **Anonymous access** — privacy-preserving queries#

## Configuration#

Environment variables:
- `ANONET_ENABLED` — enable AnoNet integration (default: `true`)
- `ANONET_HOST` — AnoNet host (default: `127.0.0.1`)
- `ANONET_PORT` — AnoNet port (default: `4150`)

## API Endpoints#

- `POST /deepintel/anonet/crawl` — crawl AnoNet resource#
- `GET /deepintel/anonet/search` — search AnoNet#
- `POST /deepintel/anonet/monitor` — monitor AnoNet#

## Usage#

```python
from cosmicsec_deepintel import AnoNetClient

client = AnoNetClient()
content = client.crawl("anonet://example")
```
