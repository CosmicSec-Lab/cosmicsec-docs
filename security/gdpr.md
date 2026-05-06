# GDPR Compliance#

**GDPR Compliance Module** — General Data Protection Regulation support.

## Overview#

Ensures the platform meets GDPR requirements for EU users' personal data.

## Features#

- **Right to erasure** — delete user data on request  
- **Data portability** — export user data in machine-readable format
- **Consent management** — track and manage user consent
- **Breach notification** — automated breach alerts within 72 hours#
- **DPIA** — Data Protection Impact Assessment tools#

## Configuration#

Environment variables:
- `GDPR_ENABLED` — enable GDPR compliance (default: `true`)
- `DATA_RETENTION_DAYS` — how long to keep data (default: `365`)
- `BREACH_NOTIFICATION_EMAIL` — email for breach alerts#
- `CONSENT_REQUIRED` — require consent for new users (default: `true`)

## API Endpoints#

- `POST /gdpr/export-data` — export user's personal data#
- `DELETE /gdpr/erase-data` — erase user's personal data#
- `GET /gdpr/consent-status` — check user's consent status#
- `POST /gdpr/update-consent` — update user consent preferences#
- `GET /gdpr/dpia-report` — generate DPIA report#

## Usage#

```python
from cosmicsec_core.gdpr import GDPRManager

manager = GDPRManager()
success = manager.erase_user_data(user_id)
```
