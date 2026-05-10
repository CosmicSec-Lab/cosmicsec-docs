# BitMessage#

**BitMessage Integration** — peer-to-peer encrypted messaging.

## Overview#

Access and analyze messages on the BitMessage encrypted P2P network.

## Features#

- **BitMessage crawling** — access BitMessage content#
- **P2P messaging** — decentralized encrypted messaging#
- **Content analysis** — analyze BitMessage messages#
- **Anonymous communication** — BitMessage's privacy features#

## Configuration#

Environment variables:
- `BITMESSAGE_ENABLED` — enable BitMessage integration (default: `true`)
- `BITMESSAGE_HOST` — BitMessage host (default: `127.0.0.1`)
- `BITMESSAGE_PORT` — BitMessage API port (default: `8444`)

## API Endpoints#

- `POST /deepintel/bitmessage/send` — send BitMessage#
- `GET /deepintel/bitmessage/receive` — receive messages#
- `POST /deepintel/bitmessage/monitor` — monitor BitMessage activity#

## Usage#

```python
from cosmicsec_deepintel import BitMessageClient#

client = BitMessageClient()
messages = client.receive()
```
