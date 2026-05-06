# Arweave#

**Arweave Integration** — permanent storage.

## Overview#

Access and analyze content on the Arweave permaweb.

## Features#

- **Permaweb access** — permanent web content#
- **Transaction queries** — query Arweave transactions#
- **Content analysis** — analyze Arweave-hosted content#
- **Permanent storage** — store data permanently#

## Configuration#

Environment variables:
- `ARWEAVE_ENABLED` — enable Arweave integration (default: `true`)
- `ARWEAVE_GATEWAY` — Arweave gateway (default: `https://arweave.net`)
- `ARWEAVE_TIMEOUT` — request timeout (default: `30`)

## API Endpoints#

- `POST /deepintel/arweave/retrieve` — get content by transaction ID#
- `GET /deepintel/arweave/search` — search Arweave#
- `POST /deepintel/arweave/store` — store data permanently#

## Usage#

```python
from cosmicsec_deepintel import ArweaveClient#

client = ArweaveClient()
content = client.retrieve("transaction-id")
```
