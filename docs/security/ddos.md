# DDoS Protection#

**DDoS Protection Service** — mitigate 10K+ RPS attacks.

## Overview#

Protects all services from Distributed Denial-of-Service attacks with automatic mitigation.

## Features#

- **Rate limiting** — per-IP and per-user limits#
- **Challenge-response** — CAPTCHA and JS challenges#
- **Geofencing** — block high-risk countries#
- **Auto-scaling** — dynamically scale protection#
- **Real-time analytics** — attack visualization#

## Configuration#

Environment variables:
- `DDOS_ENABLED` — enable DDoS protection (default: `true`)
- `DDOS_MAX_RPS` — max requests per second per IP (default: `1000`)
- `CHALENGE_REQUIRED` — require challenge (default: `true`)
- `GEOFENCE_COUNTRIES` — comma-separated country codes to block#

## API Endpoints#

- `GET /ddos/status` — protection status#
- `POST /ddos/block-ip` — manually block an IP#
- `GET /ddos/attack-logs` — view attack logs#
- `POST /ddos/whitelist` — add IP to whitelist#

## Usage#

```python
from cosmicsec_services.ddos_protection import DDoSProtection#

protector = DDoSProtection()
is_allowed = protector.check_request(request)
```
