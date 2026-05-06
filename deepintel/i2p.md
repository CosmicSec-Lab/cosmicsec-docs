# I2P Network#

**Invisible Internet Project (I2P)** integration.

## Overview#

Access and crawl I2P eepsites (.i2p) for intelligence.

## Features#

- **.i2p site crawling** — access I2P eepsites#
- **I2P proxy support** — route through I2P#
- **Eepsite discovery** — find new .i2p sites#
- **Content analysis** — analyze I2P-hosted content#

## Configuration#

Environment variables:
- `I2P_ENABLED` — enable I2P integration (default: `true`)
- `I2P_PROXY` — I2P SOCKS proxy (default: `socks://127.0.0.1:4447`)
- `I2P_TIMEOUT` — request timeout (default: `30`)

## API Endpoints#

- `POST /deepintel/i2p/crawl` — crawl .i2p site#
- `GET /deepintel/i2p/search` — search I2P content#
- `POST /deepintel/i2p/monitor` — monitor .i2p site#

## Usage#

```python
from cosmicsec_deepintel import I2PClient#

client = I2PClient()
content = client.crawl("http://example.i2p")
```
