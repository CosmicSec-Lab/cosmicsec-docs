# GNUnet#

**GNUnet Integration** — peer-to-peer framework.

## Overview#

Access and analyze content on the GNUnet anonymous network.

## Features#

- **GNUnet crawling** — access GNUnet services#
- **CAAD protocol** — GNUnet's anonymous routing#
- **Content discovery** — find GNUnet resources#
- **Anonymous publishing** — publish to GNUnet#

## Configuration#

Environment variables:
- `GNUNET_ENABLED` — enable GNUnet integration (default: `true`)
- `GNUNET_HOST` — GNUnet host (default: `127.0.0.1`)
- `GNUNET_PORT` — GNUnet port (default: `2086`)

## API Endpoints#

- `POST /deepintel/gnunet/crawl` — retrieve GNUnet content#
- `GET /deepintel/gnunet/search` — search GNUnet#
- `POST /deepintel/gnunet/publish` — publish to GNUnet#

## Usage#

```python
from cosmicsec_deepintel import GNUnetClient#

client = GNUnetClient()
content = client.retrieve("gnunet://example")
```
