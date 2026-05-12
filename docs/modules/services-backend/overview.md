# Services Backend (cosmicsec-services)

**The Engine Room of the CosmicSec Ecosystem.**

`cosmicsec-services` is a collection of high-performance microservices that handle the core business logic of the platform. From authentication and scanning to real-time collaboration and threat hunting, these services work together to deliver a comprehensive security operations experience.

## 🚀 Key Features

*   **Microservices Architecture**: 35+ specialized services covering the full spectrum of security operations.
*   **Shared Common Library**: A centralized `src/common/` package providing standardized auth, DB access, and observability.
*   **High-Throughput Ingestion**: A specialized Rust-based ingestion engine for processing massive volumes of security data.
*   **Real-time Collaboration**: Multi-user SOC dashboard with live state synchronization.
*   **Extensive Tool Integration**: Unified interface for interacting with 17+ third-party security tools.

## 🏗️ Internal Architecture

The repository is organized for high modularity:

### 1. `src/common/` (Shared Internals)
Common logic used by all microservices to ensure consistency and security.
*   **`auth_middleware.py`**: JWT verification and scope checking.
*   **`db.py` / `models.py`**: Shared database schemas and connection pooling.
*   **`observability.py`**: Standardized logging and Prometheus metric export.
*   **`egress.py`**: Secure external request management.

### 2. `src/services/` (Microservices)
Individual services grouped by functionality. Key services include:
*   **`auth_service`**: Manages user identity, 2FA, and SSO.
*   **`scan_service`**: Orchestrates SAST/DAST/SCA scans.
*   **`recon_service`**: Performs automated OSINT and infrastructure discovery.
*   **`soc_service`**: Powers the main dashboard and alert management.
*   **`collab_service`**: Handles real-time collaboration via WebSockets.

### 3. `ingest/` (Rust Engine)
A high-performance ingestion service written in Rust, designed to handle millions of security events per second and pipe them into the platform via gRPC.

## 🔄 Core Workflows

### Vulnerability Scan Workflow
1.  **Request**: `scan_service` receives a scan request via the API Gateway.
2.  **Orchestration**: It selects the appropriate tools (e.g., Nuclei, Nmap) and triggers their execution.
3.  **Ingestion**: Tool outputs are sent to the `ingest` service.
4.  **Parsing**: The `ingest` service normalizes the data and emits events to Kafka.
5.  **Processing**: The `soc_service` and `ai_helix` consume the events for analysis and display.

## 📊 Monitoring & Health

Every service exports a standardized `/health` endpoint and Prometheus metrics (`/metrics`). The `observability` module automatically tracks:
*   Request latency and error rates.
*   Database connection pool status.
*   Kafka message lag.
*   System resource utilization.

## 📂 File Structure

```text
cosmicsec-services/
├── src/
│   ├── common/          # Shared service library
│   └── services/        # 35+ Microservices
├── ingest/              # Rust-based ingest engine
├── alembic/             # Database migrations
└── ...
```
