# Nym#

**Nym Mixnet Integration** — privacy mixnet.

## Overview#

Access and analyze content on the Nym privacy mixnet.

## Features#

- **Nym crawling** — access mixnet resources#
- **Mixnet routing** — privacy-preserving routing#
- **Gateway support** — use Nym gateways#
- **Anonymous communication** — Nym's privacy features#

## Configuration#

Environment variables:
- `NYM_ENABLED` — enable Nym integration (default: `true`)
- `NYM_GATEWAY` — Nym gateway address (default: `ws://127.0.0.1:1977`)
- `NYM_TIMEOUT` — request timeout (default: `30`)

## API Endpoints#

- `POST /deepintel/nym/send` — send via Nym mixnet#
- `GET /deepintel/nym/receive` — receive from Nym#
- `POST /deepintel/nym/monitor` — monitor Nym activity#

## Usage#

```python
from cosmicsec_deepintel import NymClient#

client = NymClient()
result = client.send("nym://example")
```
