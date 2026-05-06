# Namecoin#

**Namecoin Integration** — decentralized DNS.

## Overview#

Access and analyze .bit domains via Namecoin decentralized DNS.

## Features#

- **.bit domain resolution** — resolve Namecoin domains#
- **Blockchain lookup** — query Namecoin blockchain#
- **Domain discovery** — find new .bit domains#
- **Registration monitoring** — track domain registrations#

## Configuration#

Environment variables:
- `NAMECOIN_ENABLED` — enable Namecoin integration (default: `true`)
- `NAMECOIN_HOST` — Namecoin host (default: `127.0.0.1`)
- `NAMECOIN_PORT` — Namecoin JSON-RPC port (default: `8336`)

## API Endpoints#

- `POST /deepintel/namecoin/resolve` — resolve .bit domain#
- `GET /deepintel/namecoin/search` — search Namecoin#
- `GET /deepintel/namecoin/domains` — list discovered domains#

## Usage#

```python
from cosmicsec_deepintel import NamecoinClient

client = NamecoinClient()
ip = client.resolve("example.bit")
```
