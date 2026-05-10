# 🔐 Auth Service

## Overview

The **Auth Service** handles authentication, authorization, user management, and SSO integration. It implements JWT, 2FA, OAuth2, and SAML/OIDC for enterprise SSO.

**Location:** `cosmicsec-services/services/auth_service/`  
**Port:** 8001  
**Framework:** FastAPI

## Features

### 1. Authentication Methods
- **JWT (JSON Web Tokens)** — Stateless authentication
- **2FA/TOTP** — Time-based one-time passwords (Google Authenticator, Authy)
- **OAuth2** — GitHub, Google, Microsoft providers
- **SSO (SAML 2.0, OIDC)** — Enterprise single sign-on
- **API Keys** — Service-to-service auth

### 2. User Management
- User registration with email verification
- Profile management
- Password reset via email
- Account lockout after failed attempts
- Session management

### 3. RBAC (Role-Based Access Control)
- 8 Built-in Roles: super_admin, admin, analyst, auditor, viewer, api_user, soc_analyst, threat_hunter
- 20+ Fine-grained Permissions
- Role assignment/revocation
- Permission checking middleware

### 4. Organization Management
- Multi-tenant organizations
- Team management
- Invite users to organization
- Organization-level settings

## API Endpoints

### Authentication

#### Register
```http
POST /api/v1/auth/register
```

**Request:**
```json
{
  "email": "user@company.com",
  "password": "SecureP@ssw0rd",
  "name": "John Doe",
  "organization": "acme-corp"
}
```

**Response:**
```json
{
  "user_id": "user_12345",
  "email": "user@company.com",
  "verification_required": true,
  "message": "Registration successful. Please verify your email."
}
```

#### Login
```http
POST /api/v1/auth/login
```

**Request:**
```json
{
  "email": "user@company.com",
  "password": "SecureP@ssw0rd",
  "totp_code": "123456"  // If 2FA enabled
}
```

**Response:**
```json
{
  "access_token": "eyJhbG...",
  "refresh_token": "eyJhbG...",
  "token_type": "bearer",
  "expires_in": 3600,
  "user": {
    "user_id": "user_12345",
    "email": "user@company.com",
    "role": "analyst",
    "permissions": ["scan:create", "report:read"]
  }
}
```

#### Refresh Token
```http
POST /api/v1/auth/refresh
```

**Request:**
```json
{
  "refresh_token": "eyJhbG..."
}
```

#### Verify Email
```http
GET /api/v1/auth/verify?token=xyz123
```

#### Password Reset
```http
POST /api/v1/auth/password-reset
```
Request password reset email.

```http
POST /api/v1/auth/password-reset/confirm
```
Reset password with token.

### 2FA Management

#### Enable 2FA
```http
POST /api/v1/auth/2fa/enable
```

**Response:**
```json
{
  "secret": "JBSWY3DPEHPK3PXP",
  "qr_code": "otpauth://totp/CosmicSec:user@company.com?secret=...",
  "backup_codes": ["abcd1234", "efgh5678", ...]
}
```

#### Verify 2FA
```http
POST /api/v1/auth/2fa/verify
```

**Request:**
```json
{
  "totp_code": "123456"
}
```

#### Disable 2FA
```http
POST /api/v1/auth/2fa/disable
```

### OAuth2 & SSO

#### Initiate OAuth2
```http
GET /api/v1/auth/oauth2/{provider}/login
```
Redirects to provider (GitHub, Google, Microsoft).

#### OAuth2 Callback
```http
GET /api/v1/auth/oauth2/{provider}/callback
```

#### SAML Login
```http
GET /api/v1/auth/saml/login?organization=acme-corp
```

#### OIDC Login
```http
GET /api/v1/auth/oidc/login?organization=acme-corp
```

### User Management

#### Get Profile
```http
GET /api/v1/auth/profile
```
Requires: `Authorization: Bearer <token>`

#### Update Profile
```http
PUT /api/v1/auth/profile
```

**Request:**
```json
{
  "name": "John Smith",
  "phone": "+1234567890"
}
```

#### Change Password
```http
POST /api/v1/auth/password/change
```

#### Get API Keys
```http
GET /api/v1/auth/api-keys
```

#### Create API Key
```http
POST /api/v1/auth/api-keys
```

**Response:**
```json
{
  "api_key": "cs_live_12345...",
  "key_id": "key_12345",
  "created_at": "2026-05-05T22:00:00Z",
  "expires_at": "2027-05-05T22:00:00Z"
}
```

#### Revoke API Key
```http
DELETE /api/v1/auth/api-keys/{key_id}
```

### Admin Endpoints

#### List Users
```http
GET /api/v1/admin/users?role=analyst&limit=10
```

#### Get User
```http
GET /api/v1/admin/users/{user_id}
```

#### Update User Role
```http
PUT /api/v1/admin/users/{user_id}/role
```

**Request:**
```json
{
  "role": "admin"
}
```

#### Delete User
```http
DELETE /api/v1/admin/users/{identifier}
```
Identifier can be email or userId.

#### Get Organization Users
```http
GET /api/v1/admin/organizations/{org_id}/users
```

### Settings

#### Get General Settings
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
      "session_timeout": 3600
    }
  },
  "security": {
    "password_policy": {
      "min_length": 12,
      "require_uppercase": true,
      "require_lowercase": true,
      "require_numbers": true,
      "require_special": true
    },
    "max_login_attempts": 5,
    "lockout_duration": 900
  }
}
```

## RBAC System

### Roles

| Role | Description | Permissions |
|------|-------------|-------------|
| **super_admin** | Full system access | All permissions |
| **admin** | Organization admin | User management, settings, all read/write |
| **analyst** | Security analyst | scan:*, report:*, ai:use |
| **soc_analyst** | SOC team member | monitor:*, alert:*, incident:* |
| **threat_hunter** | Threat hunting specialist | threat:*, hunt:*, ai:use |
| **auditor** | Compliance auditor | report:read, compliance:*, audit:* |
| **viewer** | Read-only access | *:read |
| **api_user** | Service account | Limited per API key |

### Permissions

```python
PERMISSIONS = [
    "scan:create", "scan:read", "scan:cancel", "scan:delete",
    "report:create", "report:read", "report:delete",
    "ai:use", "ai:ensemble", "ai:learning",
    "threat:read", "threat:hunt", "threat:mitigate",
    "user:create", "user:read", "user:update", "user:delete",
    "role:assign", "permission:grant",
    "org:manage", "settings:update",
    "compliance:read", "compliance:audit",
    "monitor:*", "alert:*", "incident:*",
    "audit:*", "admin:*"
]
```

### Checking Permissions

```python
from functools import wraps

def require_permission(permission: str):
    def decorator(func):
        @wraps(func)
        async def wrapper(*args, **kwargs):
            user = get_current_user()
            if not user.has_permission(permission):
                raise HTTPException(403, "Insufficient permissions")
            return await func(*args, **kwargs)
        return wrapper
    return decorator

@require_permission("scan:create")
async def create_scan():
    ...
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
    "role": "analyst",
    "permissions": ["scan:create", "report:read"],
    "org_id": "org_12345",
    "iat": 1620000000,
    "exp": 1620003600
  }
}
```

## SSO Configuration

### SAML 2.0

```yaml
# config/saml.yml
saml:
  enabled: true
  idp:
    entity_id: "https://idp.company.com/metadata"
    sso_url: "https://idp.company.com/sso"
    slo_url: "https://idp.company.com/slo"
    x509_cert: "${SAML_CERT}"
  sp:
    entity_id: "https://cosmicsec.company.com"
    private_key: "${SP_PRIVATE_KEY}"
    certificate: "${SP_CERT}"
  attributes:
    email: "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"
    name: "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    role: "http://schemas.microsoft.com/ws/2008/06/identity/claims/role"
```

### OIDC (OpenID Connect)

```yaml
oidc:
  enabled: true
  providers:
    - name: "keycloak"
      issuer: "https://keycloak.company.com/realms/master"
      client_id: "${OIDC_CLIENT_ID}"
      client_secret: "${OIDC_CLIENT_SECRET}"
      scopes: ["openid", "profile", "email"]
```

## Security Features

### Password Policy
- Minimum 12 characters
- Uppercase, lowercase, numbers, special chars
- Check against breached passwords (HaveIBeenPwned API)
- Password history (prevent reuse)

### Account Lockout
- 5 failed attempts → lockout for 15 minutes
- Progressive delays between attempts
- Admin unlock capability

### Session Management
- JWT expiry: 1 hour (configurable)
- Refresh token expiry: 30 days
- Session revocation on password change
- Concurrent session limits

### Rate Limiting
- Login: 5 attempts per minute per IP
- Password reset: 3 requests per hour per email
- API: 2000 requests per minute per token

## Data Models

### User
```python
class User:
    user_id: str
    email: str
    hashed_password: str
    name: str
    role: str
    permissions: List[str]
    organization_id: str
    is_active: bool
    is_verified: bool
    totp_enabled: bool
    totp_secret: Optional[str]
    backup_codes: List[str]
    created_at: datetime
    last_login: Optional[datetime]
```

### Organization
```python
class Organization:
    org_id: str
    name: str
    domain: str
    settings: Dict
    subscription_tier: str  # free, pro, enterprise
    created_at: datetime
```

## CLI Usage

```bash
# Login
cosmicsec auth login --email user@company.com

# Register
cosmicsec auth register --email user@company.com --org acme-corp

# Enable 2FA
cosmicsec auth 2fa enable

# Create API key
cosmicsec auth api-key create --name "Production API"
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

# Login
client = CosmicSecClient()
client.login(email="user@company.com", password="SecureP@ssw0rd")

# Or use API key
client = CosmicSecClient(api_key="cs_live_12345...")

# Get profile
profile = client.auth.get_profile()
print(f"Logged in as: {profile.email}")

# Create user (admin)
client.auth.create_user(
    email="newuser@company.com",
    role="analyst",
    organization="acme-corp"
)
```

## Configuration

### Environment Variables
```bash
# Service
AUTH_SERVICE_PORT=8001
AUTH_SERVICE_HOST=0.0.0.0

# JWT
JWT_SECRET=your-super-secret-key
JWT_ALGORITHM=HS256
JWT_EXPIRY_HOURS=24

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec

# Redis (sessions)
REDIS_URL=redis://redis:6379

# OAuth2
GITHUB_CLIENT_ID=...
GITHUB_CLIENT_SECRET=...
GOOGLE_CLIENT_ID=...
GOOGLE_CLIENT_SECRET=...
MICROSOFT_CLIENT_ID=...
MICROSOFT_CLIENT_SECRET=...

# SAML
SAML_ENABLED=true
SAML_CERT=...
SP_PRIVATE_KEY=...

# Password Policy
PASSWORD_MIN_LENGTH=12
PASSWORD_REQUIRE_SPECIAL=true
MAX_LOGIN_ATTEMPTS=5
LOCKOUT_DURATION=900

# Email (for verification, password reset)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=...
SMTP_PASS=...
```

## Monitoring

### Metrics
- `auth_logins_total` — Total login attempts (success/failure)
- `auth_registrations_total` — New user registrations
- `auth_2fa_enabled_total` — Users with 2FA enabled
- `auth_sso_logins_total` — SSO login count by provider
- `auth_failed_attempts_total` — Failed login attempts

### Alerts
- Brute force attack detected (100+ failed logins in 10 minutes)
- Unusual login location (new country/IP)
- Multiple failed 2FA attempts
- Privilege escalation attempts

## Troubleshooting

### Login Fails
```bash
# Check user status
curl http://localhost:8001/api/v1/admin/users/user_12345 \
  -H "Authorization: Bearer <admin_token>"

# Check if account is locked
# Look for "locked_until" field

# Reset password
curl -X POST http://localhost:8001/api/v1/auth/password-reset \
  -d '{"email": "user@company.com"}'
```

### JWT Token Invalid
```bash
# Decode token to check expiry
echo "eyJhbG..." | base64 -d

# Check JWT secret matches
echo $JWT_SECRET

# Generate new token
curl -X POST http://localhost:8001/api/v1/auth/refresh \
  -d '{"refresh_token": "eyJhbG..."}'
```

### SSO Not Working
```bash
# Check SAML/OIDC config
cat config/saml.yml
cat config/oidc.yml

# Test SSO flow
curl -v http://localhost:8001/api/v1/auth/saml/login?organization=acme-corp

# Check logs
docker logs auth-service | grep "saml\|oidc"
```

## Next Steps

- [RBAC & SSO Deep Dive](../security/rbac-sso.md)
- [Organization Service](./org-service.md)
- [Admin Service](./admin-service.md)
- [Security Best Practices](../guides/security-best-practices.md)
