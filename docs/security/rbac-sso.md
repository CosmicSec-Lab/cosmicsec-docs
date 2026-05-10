# 🔐 Security — RBAC & SSO

## Overview

CosmicSec implements a comprehensive **Role-Based Access Control (RBAC)** system with **Single Sign-On (SSO)** integration for enterprise environments.

## RBAC System

### Architecture
```
User → Role Assignment → Permissions → Resource Access
```

### 8 Built-in Roles

| Role | Description | Typical User |
|------|-------------|--------------|
| **super_admin** | Full system access | System owner |
| **admin** | Organization admin | IT/Security manager |
| **analyst** | Security analyst | Security team |
| **soc_analyst** | SOC team member | SOC analyst |
| **threat_hunter** | Threat hunting specialist | Threat intel team |
| **auditor** | Compliance auditor | Audit team |
| **viewer** | Read-only access | Management, stakeholders |
| **api_user** | Service account | CI/CD, integrations |

### 20+ Fine-Grained Permissions

#### Scan Permissions
- `scan:create` — Create new scans
- `scan:read` — View scan results
- `scan:cancel` — Cancel running scans
- `scan:delete` — Delete scans
- `scan:configure` — Modify scan settings

#### Report Permissions
- `report:create` — Generate reports
- `report:read` — View reports
- `report:delete` — Delete reports
- `report:export` — Export reports

#### AI Permissions
- `ai:use` — Use AI analysis
- `ai:ensemble` — Use multi-model ensemble
- `ai:learning` — Submit feedback (learning)
- `ai:remediation` — Generate remediation playbooks
- `ai:predictive` — Access predictive analytics

#### Threat Intelligence
- `threat:read` — View threat intel
- `threat:hunt` — Conduct threat hunting
- `threat:mitigate` — Mitigate threats
- `threat:export` — Export intel data

#### User Management
- `user:create` — Create users
- `user:read` — View users
- `user:update` — Update users
- `user:delete` — Delete users
- `role:assign` — Assign roles

#### Administrative
- `org:manage` — Manage organization
- `settings:update` — Update settings
- `compliance:read` — View compliance reports
- `compliance:audit` — Run compliance audits
- `admin:*` — Full admin access (super_admin only)

### Role-Permission Matrix

| Permission | super_admin | admin | analyst | soc_analyst | threat_hunter | auditor | viewer | api_user |
|-----------|-------------|--------|----------|--------------|---------------|---------|--------|----------|
| scan:create | ✅ | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ⚙️ |
| scan:read | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⚙️ |
| ai:use | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ | ⚙️ |
| threat:hunt | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ | ⚙️ |
| user:create | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| admin:* | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

⚙️ = Configurable per API key

## Custom Roles

### Create Custom Role
```http
POST /api/v1/admin/roles
```

**Request:**
```json
{
  "name": "pentester",
  "description": "Penetration tester role",
  "permissions": [
    "scan:create", "scan:read", "scan:cancel",
    "report:create", "report:read",
    "ai:use", "ai:remediation"
  ]
}
```

### Assign Role to User
```http
PUT /api/v1/admin/users/{user_id}/role
```

**Request:**
```json
{
  "role": "pentester"
}
```

## SSO Integration

### Supported Protocols
- **SAML 2.0** — Most enterprise IdPs
- **OIDC (OpenID Connect)** — Modern standard
- **OAuth2** — GitHub, Google, Microsoft

### SAML 2.0 Setup

#### 1. Configure Identity Provider (IdP)
Get these from your IdP (Okta, Azure AD, Keycloak, etc.):
- Entity ID
- SSO URL
- SLO URL
- X.509 Certificate

#### 2. Configure CosmicSec (SP)
```yaml
# config/saml.yml
saml:
  enabled: true
  idp:
    entity_id: "https://idp.company.com/metadata"
    sso_url: "https://idp.company.com/sso"
    slo_url: "https://idp.company.com/slo"
    x509_cert: |
      -----BEGIN CERTIFICATE-----
      ...
      -----END CERTIFICATE-----
  sp:
    entity_id: "https://cosmicsec.company.com"
    private_key: |
      -----BEGIN PRIVATE KEY-----
      ...
      -----END PRIVATE KEY-----
    certificate: |
      -----BEGIN CERTIFICATE-----
      ...
      -----END CERTIFICATE-----
  attributes:
    email: "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"
    name: "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    role: "http://schemas.microsoft.com/ws/2008/06/identity/claims/role"
```

#### 3. Test SAML Flow
```bash
# Initiate login
curl -v "http://localhost:8001/api/v1/auth/saml/login?organization=acme-corp"

# Should redirect to IdP login page
```

### OIDC Setup

#### 1. Create OIDC Client in IdP
Get these values:
- Issuer URL
- Client ID
- Client Secret

#### 2. Configure CosmicSec
```yaml
# config/oidc.yml
oidc:
  enabled: true
  providers:
    - name: "keycloak"
      issuer: "https://keycloak.company.com/realms/master"
      client_id: "${OIDC_CLIENT_ID}"
      client_secret: "${OIDC_CLIENT_SECRET}"
      scopes: ["openid", "profile", "email"]
      claim_mappings:
        email: "email"
        name: "preferred_username"
        role: "roles"
```

#### 3. Test OIDC Flow
```bash
# Initiate login
curl -v "http://localhost:8001/api/v1/auth/oidc/login?organization=acme-corp"

# Should redirect to IdP authorization endpoint
```

### OAuth2 (Social Login)

#### Supported Providers
- GitHub
- Google
- Microsoft

#### Configuration
```yaml
# config/oauth2.yml
oauth2:
  enabled: true
  providers:
    github:
      client_id: "${GITHUB_CLIENT_ID}"
      client_secret: "${GITHUB_CLIENT_SECRET}"
      scopes: ["user:email"]
    google:
      client_id: "${GOOGLE_CLIENT_ID}"
      client_secret: "${GOOGLE_CLIENT_SECRET}"
      scopes: ["openid", "profile", "email"]
```

## JWT Token Structure

```json
{
  "header": {
    "alg": "HS256",
    "typ": "JWT"
  },
  "payload": {
    "sub": "user_12345",
    "email": "user@company.com",
    "name": "John Doe",
    "role": "analyst",
    "permissions": ["scan:create", "scan:read", "ai:use"],
    "org_id": "org_12345",
    "org_name": "Acme Corp",
    "iat": 1620000000,
    "exp": 1620003600
  }
}
```

## Middleware Usage

### Checking Permissions in Code
```python
from functools import wraps
from fastapi import HTTPException, Depends

def require_permission(permission: str):
    def decorator(func):
        @wraps(func)
        async def wrapper(*args, **kwargs):
            user = kwargs.get('current_user') or await get_current_user()
            if not user.has_permission(permission):
                raise HTTPException(
                    status_code=403,
                    detail=f"Insufficient permissions. Required: {permission}"
                )
            return await func(*args, **kwargs)
        return wrapper
    return decorator

# Usage
@require_permission("scan:create")
async def create_scan(scan_data: ScanCreate, current_user: User = Depends(get_current_user)):
    # Only users with scan:create permission can access
    ...
```

### Checking Roles
```python
def require_role(role: str):
    def decorator(func):
        @wraps(func)
        async def wrapper(*args, **kwargs):
            user = await get_current_user()
            if user.role != role and user.role != "super_admin":
                raise HTTPException(403, "Insufficient role")
            return await func(*args, **kwargs)
        return wrapper
    return decorator
```

## API Key Permissions

API keys can have restricted permissions:

```http
POST /api/v1/auth/api-keys
```

**Request:**
```json
{
  "name": "CI/CD Scanner",
  "permissions": ["scan:create", "scan:read"],
  "expires_at": "2027-05-05T22:00:00Z"
}
```

**Response:**
```json
{
  "api_key": "cs_live_12345...",
  "key_id": "key_12345",
  "permissions": ["scan:create", "scan:read"],
  "expires_at": "2027-05-05T22:00:00Z"
}
```

## Organization-Level RBAC

### Multi-Tenancy
```
Organization (Acme Corp)
├── Users
│   ├── admin (Org Admin)
│   ├── analyst1 (Analyst)
│   ├── analyst2 (Analyst)
│   └── viewer1 (Viewer)
├── Settings
│   ├── SSO Configuration
│   ├── Role Customizations
│   └── Permission Overrides
```

### Organization Settings
```http
GET /api/v1/settings/general
```

**Response:**
```json
{
  "organization": {
    "name": "Acme Corp",
    "id": "org_12345",
    "settings": {
      "allow_registration": false,
      "require_2fa": true,
      "default_role": "viewer",
      "ip_whitelist": ["192.168.1.0/24"],
      "session_timeout": 3600
    },
    "sso": {
      "saml_enabled": true,
      "oidc_enabled": false
    }
  }
}
```

## Audit Logging

All RBAC and SSO events are logged:

```json
{
  "timestamp": "2026-05-05T22:00:00Z",
  "event_type": "permission_check",
  "user_id": "user_12345",
  "resource": "/api/v1/scans",
  "permission": "scan:create",
  "result": "allowed"
}
```

### Audit Endpoints
```http
GET /api/v1/admin/audit/logs?event_type=permission_check&limit=100
```

## Best Practices

### 1. Principle of Least Privilege
- Assign minimum necessary permissions
- Use custom roles for specific needs
- Regularly audit user permissions

### 2. Regular Access Reviews
```bash
# List users with admin role
curl http://localhost:8001/api/v1/admin/users?role=admin \
  -H "Authorization: Bearer <admin_token>"
```

### 3. Rotate API Keys
```bash
# List API keys
curl http://localhost:8001/api/v1/auth/api-keys \
  -H "Authorization: Bearer <token>"

# Revoke old key and create new one
curl -X DELETE http://localhost:8001/api/v1/auth/api-keys/key_old \
  -H "Authorization: Bearer <token>"

curl -X POST http://localhost:8001/api/v1/auth/api-keys \
  -H "Authorization: Bearer <token>" \
  -d '{"name": "New Key", "permissions": [...]}' 
```

### 4. Enforce 2FA for Admins
```yaml
# config/security.yml
security:
  require_2fa_for_roles: ["super_admin", "admin"]
  allow_2fa_recovery_codes: true
```

## Troubleshooting

### Permission Denied
```bash
# Check user permissions
curl http://localhost:8001/api/v1/admin/users/user_12345 \
  -H "Authorization: Bearer <admin_token>"

# Check JWT token payload
echo "eyJhbG..." | base64 -d | jq .

# Verify permission check in logs
docker logs auth-service | grep "permission_check"
```

### SSO Login Fails
```bash
# Check SAML/OIDC config
cat config/saml.yml
cat config/oidc.yml

# Test IdP connectivity
curl -v https://idp.company.com/metadata

# Check SSO logs
docker logs auth-service | grep "saml\|oidc"
```

### Role Not Applying
```bash
# Check role assignment
curl http://localhost:8001/api/v1/admin/users/user_12345 \
  -H "Authorization: Bearer <admin_token>" \
  | jq .role

# Update role
curl -X PUT http://localhost:8001/api/v1/admin/users/user_12345/role \
  -H "Authorization: Bearer <admin_token>" \
  -d '{"role": "analyst"}'
```

## Next Steps

- [Data Residency & GDPR](../security/data-residency.md)
- [Quantum Cryptography](../security/quantum-crypto.md)
- [Zero-Trust Architecture](../security/zero-trust.md)
- [Auth Service API](../services/auth-service.md)
- [Organization Service](../services/org-service.md)
