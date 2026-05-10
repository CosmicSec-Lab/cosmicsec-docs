# Privacy Chains#

**Privacy Blockchain Integration** — Monero, Zcash tracking.

## Overview#

Monitor privacy-focused blockchains for suspicious activity.

## Features#

- **Monero tracking** — analyze XMR transactions#
- **Zcash tracking** — analyze ZEC transactions#
- **Transaction clustering** — group related transactions#
- **Suspicious activity** — flag unusual patterns#

## Configuration#

Environment variables:
- `PRIVACY_CHAINS_ENABLED` — enable (default: `true`)
- `MONERO_NODE` — Monero node (default: `127.0.0.1:18081`)
- `ZCASH_NODE` — Zcash node (default: `127.0.0.1:8232`)

## API Endpoints#

- `POST /deepintel/privacy-chains/track` — track transaction#
- `GET /deepintel/privacy-chains/alerts` — get alerts#
- `POST /deepintel/privacy-chains/analyze` — analyze address#

## Usage#

```python
from cosmicsec_deepintel import PrivacyChainClient#

client = PrivacyChainClient()
alerts = client.get_alerts()
```
