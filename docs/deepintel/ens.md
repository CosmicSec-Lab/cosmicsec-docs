# ENS — Ethereum Name Service#

**ENS Integration** — decentralized DNS for Ethereum.

## Overview#

Resolve and analyze .eth domains via ENS.

## Features#

- **.eth domain resolution** — resolve ENS names#
- **Blockchain lookup** — query ENS smart contract#
- **Domain monitoring** — track ENS registrations#
- **Reverse resolution** — resolve address to ENS name#

## Configuration#

Environment variables:
- `ENS_ENABLED` — enable ENS integration (default: `true`)
- `ENS_PROVIDER` — Ethereum provider (default: `https://mainnet.infura.io`)
- `ENS_TIMEOUT` — request timeout (default: `30`)

## API Endpoints#

- `POST /deepintel/ens/resolve` — resolve .eth domain#
- `GET /deepintel/ens/reverse` — reverse resolve address#
- `GET /deepintel/ens/discovered` — list discovered domains#

## Usage#

```python
from cosmicsec_deepintel import ENSClient#

client = ENSClient()
ip = client.resolve("example.eth")
```
