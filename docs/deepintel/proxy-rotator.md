# Proxy Rotator#

**Proxy Rotation Manager** — weighted rotation for anonymity across networks.

## Overview#

Manages a pool of proxies with health checking, automatic failover, and weighted rotation.

## Features#

- **Weighted rotation** — rotate based on proxy weight#
- **Health checking** — automatic proxy health verification#
- **Automatic failover** — switch on proxy failure#
- **Network categorization** — organize by network type (Tor, I2P, etc.)#
- **Statistics tracking** — success/failure rates#

## Configuration#

Environment variables:
- `DEEPINTEL_PROXY_POOL` — comma-separated proxy list#
- `DEEPINTEL_PROXY_HEALTH_INTERVAL` — health check interval (default: `300` seconds)#
- `STORAGE_MODE` — `dynamic` or `emergency_json`#

## API Endpoints#

- `GET /deepintel/proxy-rotator/status` — get proxy pool status#
- `POST /deepintel/proxy-rotator/add` — add new proxy#
- `DELETE /deepintel/proxy-rotator/{url}` — remove proxy#
- `GET /deepintel/proxy-rotator/health` — health check all proxies#

## Usage#

```python
from cosmicsec_deepintel import ProxyRotator#

rotator = ProxyRotator()
proxy = rotators.get_proxy(network="tor")
```
