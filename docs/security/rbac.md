# RBAC & SSO System#

**Role-Based Access Control (RBAC)** and **Single Sign-On (SSO)** module.

## Overview#

The RBAC/SSO module provides enterprise-grade access control with support for SAML, OIDC, and OAuth2.

## Roles#

- **Admin** — full system access
- **Security Analyst** — read/write on security data
- **Auditor** — read-only access
- **Read-Only** — view-only access
- **Custom roles** — define your own permissions

## Features#

- SAML 2.0 integration
- OIDC/OAuth2 support  
- Multi-factor authentication (2FA)
- Session management
- Policy-based access control

## Configuration#

Environment variables:
- `SSO_ENABLED` — enable SSO (default: `true`)
- `SAML_IDP_METADATA_URL` — SAML IdP metadata URL
- `OIDC_CLIENT_ID` — OIDC client ID
- `OIDC_CLIENT_SECRET` — OIDC client secret (use Vault)

## API Endpoints#

- `POST /auth/login` — authenticate user
- `POST /auth/logout` — end session
- `GET /rbac/roles` — list roles
- `POST /rbac/assign-role` — assign role to user
- `GET /sso/metadata` — SAML/OIDC metadata

## Usage#

```python
from cosmicsec_core.rbac import RBACEngine

engine = RBACEngine()
allowed = engine.check_permission(user_id, "scan:create")
```
