# IPFS#

**InterPlanetary File System (IPFS)** integration.

## Overview#

Access and analyze content on the decentralized IPFS network.

## Features#

- **Content addressing** — retrieve by hash#
- **Decentralized storage** — access distributed content#
- **Pinning support** — keep important content#
- **Gateway fallback** — use public gateways#

## Configuration#

Environment variables:
- `IPFS_ENABLED` — enable IPFS integration (default: `true`)
- `IPFS_API` — IPFS API endpoint (default: `http://127.0.0.1:5001`)
- `IPFS_GATEWAY` — public gateway (default: `https://ipfs.io`)

## API Endpoints#

- `POST /deepintel/ipfs/retrieve` — get content by hash#
- `POST /deepintel/ipfs/pin` — pin content#
- `GET /deepintel/ipfs/search` — search IPFS#

## Usage#

```python
from cosmicsec_deepintel import IPFSClient#

client = IPFSClient()
content = client.retrieve("QmXo7B...hash")
```
