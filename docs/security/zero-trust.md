# Zero-Trust Architecture#

**Zero-Trust Network Access (ZTNA)** module.

## Overview#

Implements zero-trust principles: "never trust, always verify" for all connections.

## Features#

- **Device posture checking** — verify device health before access
- **Microsegmentation** — isolate workloads
- **mTLS** — mutual TLS authentication
- **Continuous verification** — re-verify throughout session
- **Least privilege access** — grant minimal required access

## Components#

- **Policy Engine** — evaluate access requests
- **Trust Scoring** — calculate device/user trust
- **Identity Verification** — multi-factor authentication
- **Network Isolation** — microsegmentation

## Configuration#

Environment variables:
- `ZTNA_ENABLED` — enable zero-trust (default: `true`)
- `MTLS_REQUIRED` — require mTLS (default: `true`)
- `DEVICE_POSTURE_CHECK` — check device health (default: `true`)

## API Endpoints#

- `POST /ztna/verify` — verify device posture
- `GET /ztna/trust-score` — get trust score
- `POST /ztna/access-request` — request access
- `GET /ztna/policies` — list access policies
