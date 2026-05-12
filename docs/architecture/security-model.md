# Security Model

CosmicSec implements a defense-in-depth strategy, combining zero-trust principles with cutting-edge cryptographic standards.

## 1. Zero-Trust Architecture

We operate on the principle of "Never Trust, Always Verify."

*   **Identity-Based Access**: Every request must be authenticated via JWT, verified by the Auth Service.
*   **mTLS (Mutual TLS)**: All internal communication between microservices is encrypted and authenticated using mTLS.
*   **Continuous Authentication**: Session tokens are short-lived and subject to continuous validation.
*   **Device Posture**: (Planned) Verification of the client machine's security state before granting access.

## 2. RBAC (Role-Based Access Control)

The platform supports a granular RBAC system with 8 predefined roles:

| Role | Description |
| :--- | :--- |
| `super_admin` | Full system control and configuration. |
| `admin` | Organization-level management. |
| `soc_analyst` | Standard security operations and incident handling. |
| `threat_hunter` | Advanced analysis and darknet investigation access. |
| `auditor` | Read-only access to audit logs and reports. |
| `analyst` | Limited access to specific scan results. |
| `viewer` | Read-only access to dashboards. |
| `api_user` | Programmatic access with scoped permissions. |

## 3. Quantum-Ready Cryptography <a name="quantum-crypto"></a>

To protect against future quantum computing threats, CosmicSec supports Post-Quantum Cryptography (PQC) standards:

*   **Kyber (ML-KEM)**: Used for secure key encapsulation.
*   **Dilithium (ML-DSA)**: Used for digital signatures.
*   **Hybrid Schemes**: We use a combination of classical (ECC/RSA) and PQC algorithms to ensure compatibility and maximum security.

## 4. API Security

*   **Rate Limiting**: Protected against brute-force and DoS attacks at the Gateway level (100-2000 req/min).
*   **Circuit Breakers**: Prevents cascading failures by tripping after 10 failures within 60 seconds.
*   **Input Validation**: All APIs use Pydantic/Zod for strict schema validation.
*   **Token Blacklisting**: Real-time revocation of compromised tokens via Redis.

## 5. Data Encryption

*   **At Rest**: All databases (PostgreSQL, MongoDB) use AES-256 encryption.
*   **In Transit**: TLS 1.3 enforced for all external connections.
*   **Secrets Management**: Environment variables and sensitive keys are managed via secure vaults (e.g., HashiCorp Vault).
