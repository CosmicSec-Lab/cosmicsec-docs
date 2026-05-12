# Security & RBAC

## Overview

CosmicSec-Lab implements **enterprise-grade security** with Zero-Trust architecture, quantum-ready cryptography, and comprehensive RBAC.

---

## 1. Zero-Trust Model

### Core Principles
- **Never trust, always verify**
- mTLS between all services
- Continuous authentication
- Device posture checks

### Implementation

```python
# core/zero_trust.py
class ZeroTrustEngine:
    async def authenticate_request(
        token: str,
        device_info: DeviceInfo
    ) -> AuthResult:
        # Validate JWT + device posture
        # Check continuous auth
        # Enforce device trust score
```

### Device Trust Score
```python
@dataclass
class DeviceTrustScore:
    certificate_valid: bool      # mTLS certificate
    os_patch_level: int         # Patched OS
    edr_installed: bool         # EDR present
    compliance_score: float     # 0.0-1.0
```

---

## 2. Authentication Flow

```
┌─────────────────────────────────────────────────────┐
│                    User Login                        │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│                 API Gateway                          │
│             (JWT Validation)                         │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│               Auth Service                           │
│    ┌─────────────────────────────────────────┐     │
│    │  JWT Creation + RBAC Enforcement         │     │
│    │  SSO (SAML/OIDC) + 2FA (TOTP)           │     │
│    └─────────────────────────────────────────┘     │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│            Subsequent Requests                       │
│      (JWT in Authorization header)                   │
└─────────────────────────────────────────────────────┘
```

---

## 3. RBAC System

### Roles (8 total)

| Role | Description |
|------|-------------|
| `super_admin` | Full system access |
| `admin` | User management, org settings |
| `analyst` | Scan management, findings |
| `auditor` | Read-only audit logs |
| `viewer` | Read-only basic access |
| `api_user` | API-only access |
| `soc_analyst` | SOC dashboard, hunting |
| `threat_hunter` | Advanced hunting, AI |

### Permissions Matrix

| Permission | super_admin | admin | analyst | auditor | viewer | api_user | soc_analyst | threat_hunter |
|------------|-------------|-------|---------|---------|--------|---------|-------------|---------------|
| `scan:create` | Y | Y | Y | N | N | Y | Y | Y |
| `scan:read` | Y | Y | Y | Y | Y | Y | Y | Y |
| `scan:delete` | Y | Y | N | N | N | N | N | N |
| `finding:read` | Y | Y | Y | Y | Y | Y | Y | Y |
| `finding:mask` | Y | Y | Y | N | N | N | Y | Y |
| `ai:use` | Y | Y | Y | N | N | N | Y | Y |
| `admin:users` | Y | Y | N | N | N | N | N | N |
| `audit:read` | Y | Y | Y | Y | N | N | Y | Y |
| `*:all` | Y | N | N | N | N | N | N | N |

### Implementation

```python
# core/rbac.py
class RBACManager:
    async def check_permission(
        user: User,
        resource: str,
        action: str
    ) -> bool:
        # Lookup user's role
        # Check permission matrix
        # Return True/False

    async def assign_role(
        user: User,
        role: Role,
        org: Organization
    ) -> None:
        # Create role assignment
        # Invalidate cached permissions
```

---

## 4. SSO Integration

### Supported Protocols
- **SAML 2.0** - Enterprise SSO
- **OIDC** - Modern OAuth2
- **OAuth2** - Third-party login
- **TOTP 2FA** - Time-based OTP

### TOTP 2FA Flow

```python
# services/auth_service/2fa_totp.py
class TOTPService:
    async def setup_2fa(user: User) -> str:
        # Generate Fernet-encrypted secret
        # Return QR code URL for authenticator

    async def verify_2fa(
        user: User,
        token: str
    ) -> bool:
        # Decrypt Fernet secret
        # Validate TOTP token
        # Allow/resend rate limiting
```

---

## 5. Quantum-Ready Cryptography

### Algorithms

| Algorithm | Type | Purpose |
|-----------|------|---------|
| **Kyber (ML-KEM)** | Key Encapsulation | PQC key exchange |
| **Dilithium (ML-DSA)** | Digital Signatures | PQC signatures |
| **SPHINCS+** | Hash-based | Long-term security |

### Hybrid Scheme

```python
# core/crypto/quantum.py
class QuantumReadyCrypto:
    def hybrid_encrypt(
        plaintext: bytes,
        classical_pubkey: RSA,
        pqc_pubkey: KyberPublicKey
    ) -> HybridCiphertext:
        # Encrypt with both
        # Combine ciphertexts

    def hybrid_decrypt(
        ciphertext: HybridCiphertext,
        classical_privkey: RSA,
        pqc_privkey: KyberPrivateKey
    ) -> bytes:
        # Decrypt both
        # Verify classical
        # Verify PQC
        # Return combined
```

---

## 6. Security Features

### Multi-Level Caching Security
```python
@dataclass
class CacheSecurity:
    encrypt_at_rest: bool      # Fernet encryption
    tls_in_transit: bool       # mTLS to Redis
    key_rotation_days: int     # Key rotation
```

### Audit Logging
```python
# core/common/audit_logger.py
@dataclass
class AuditEvent:
    user_id: str
    action: str
    resource: str
    outcome: Literal["SUCCESS", "FAILURE"]
    ip_address: str
    user_agent: str
    timestamp: datetime
```

---

## 7. Rate Limiting Security

### Per-Network Rate Limiting
```python
# deepintel/network_rate_limiter.py
class NetworkRateLimiter:
    """Per-network rate limiting for dark web monitoring"""
    rate_limits = {
        "tor": 10,      # req/min
        "i2p": 5,
        "ipfs": 20,
    }
```

### Login Rate Limiter
```python
# services/auth_service/rate_limiter.py
class LoginRateLimiter:
    MAX_ATTEMPTS = 5
    LOCKOUT_DURATION = 15  # minutes
    progressive_delay = True
```

---

## 8. Security Observability

### Metrics
- Failed auth attempts
- RBAC violations
- Rate limit exceeded
- Circuit breaker opens

### Alerting
- Anomaly detection on auth patterns
- Real-time SOC dashboard
- MITRE ATT&CK correlation

---

## Related Links

- [API Gateway](../architecture/gateway.md)
- [Services Overview](../services/overview.md)
- [Deployment Security](../deployment/)