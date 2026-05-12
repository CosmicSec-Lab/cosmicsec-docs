# Core Platform (cosmicsec-core)

**The Foundation of the CosmicSec Ecosystem.**

The Core Platform provides the essential infrastructure and shared components that power all other modules. It includes the API Gateway, shared libraries, security middleware, and platform-level orchestration.

## 🚀 Key Features

*   **API Gateway**: A high-performance FastAPI entry point with GraphQL federation support.
*   **Security Middleware**: Hybrid routing, policy registration, and static profile management.
*   **Service Discovery**: Real-time registry of all active microservices.
*   **RBAC & SSO**: Centralized identity management with role-based access control and Single Sign-On support.
*   **Post-Quantum Cryptography**: Integration of Kyber and Dilithium algorithms into the platform core.
*   **Platform Orchestration**: Modules for GitOps, WASM execution, and cross-repo data residency.

## 🏗️ Internal Architecture

The Core Platform is divided into two primary sections:

### 1. `cosmicsec_platform/` (Shared Library)
A canonical package installed as a dependency by other services.
*   **`config.py`**: Centralized configuration and environment management.
*   **`service_discovery.py`**: Client for the internal service registry.
*   **`middleware/`**: Shared FastAPI/Starlette middleware (Policy registry, hybrid router).
*   **`three_js_viz.py`**: Shared logic for 3D visualizations used by the web frontend.

### 2. `api_gateway/` (Service)
The external-facing entry point.
*   **`main.py`**: Entry point for the REST and GraphQL APIs.
*   **`routers/`**: Core and platform-specific routing logic.
*   **`ingest_bridge.py`**: Integration layer for high-throughput data ingestion via gRPC.

### 3. `platform/` (Infrastructure Modules)
*   **`rbac/`**: Core RBAC models and interfaces.
*   **`sso/`**: SSO integration (SAML, OIDC).
*   **`quantum_crypto/`**: Implementation of PQC algorithms.
*   **`wasm/`**: Sandboxed execution environment for custom plugins.

## 🔄 Core Services

| Service | Description |
| :--- | :--- |
| **API Gateway** | Routing, Auth, Rate Limiting, Circuit Breaking. |
| **RBAC Manager** | Permission management and role assignment. |
| **SSO Provider** | Integration with external identity providers. |
| **Discovery Service** | Service health and location registry. |

## 🛠️ Configuration Example

```python
# config.yaml (Centralized Core Config)
platform:
  version: "2.5.0"
  security:
    pqc_enabled: true
    mtls_strict: true
  gateway:
    rate_limit: 1000  # req/min
    circuit_breaker:
      threshold: 10
      timeout: 60
```

## 📂 File Structure

```text
cosmicsec-core/
├── cosmicsec_platform/  # Canonical shared package
│   ├── config.py
│   ├── middleware/
│   └── service_discovery.py
├── api_gateway/         # Gateway service
│   ├── routers/
│   └── main.py
├── platform/            # Platform modules
│   ├── rbac/
│   ├── sso/
│   ├── quantum_crypto/
│   └── wasm/
└── ...
```
