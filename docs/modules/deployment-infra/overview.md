# Deployment & Infrastructure (cosmicsec-deploy)

**Cloud-Native Foundation for CosmicSec.**

The `cosmicsec-deploy` repository contains all the configuration, scripts, and manifests required to deploy and manage the CosmicSec ecosystem in various environments, from local development to production-scale Kubernetes clusters.

## 🚀 Key Features

*   **Docker Compose**: Streamlined setup for local development and testing.
*   **Kubernetes Manifests**: Production-ready manifests for multi-zone deployment.
*   **CI/CD Pipelines**: Automated building, testing, and deployment via GitHub Actions.
*   **Observability Stack**: Pre-configured Prometheus, Grafana, and Alertmanager configurations.
*   **Infrastructure-as-Code**: Automated provisioning of networks, databases, and message brokers.

## 🏗️ Deployment Architectures

### 1. Local Development (Docker Compose)
Best for developers and small-scale testing. All services run as Docker containers on a single machine.
*   **File**: `docker-compose.yml`
*   **Pre-requisites**: Docker, Docker Compose, Python 3.11+.

### 2. Enterprise Production (Kubernetes)
Designed for high availability and scalability.
*   **Location**: `k8s/` directory.
*   **Components**: Individual deployment files for each service (e.g., `scan-service-deployment.yaml`, `gateway-deployment.yaml`), Ingress controllers, and persistent volume claims.
*   **Observability**: Integrated Prometheus operator for real-time monitoring.

## 📊 Observability & Monitoring

Located in `infrastructure/`:

*   **Prometheus**: Scrapes metrics from all microservices and the API Gateway.
*   **Grafana**: Provides pre-configured dashboards for SOC performance, service health, and resource usage.
*   **Alertmanager**: Routes critical alerts to the CosmicSec Notification Service and external channels (Slack, PagerDuty).
*   **Jaeger**: Distributed tracing to identify performance bottlenecks across microservices.

## 🔄 CI/CD Workflow

1.  **Code Commit**: Developer pushes to any module repository.
2.  **Validation**: GitHub Actions triggers unit tests and linting.
3.  **Build**: Docker images are built and pushed to the CosmicSec Container Registry (ACR/GHCR).
4.  **Security Scan**: Built images are scanned for vulnerabilities.
5.  **Deploy**: Updated manifests are applied to the staging/production cluster (ArgoCD/Flux).

## 🛠️ Quick Start (Docker)

```bash
# Clone the repository
git clone https://github.com/cosmicsec/cosmicsec-deploy.git
cd cosmicsec-deploy

# Initialize local environment
./scripts/setup_env.sh

# Start the ecosystem
docker-compose up -d
```

## 📂 File Structure

```text
cosmicsec-deploy/
├── infrastructure/     # Observability and database configs
│   ├── grafana/
│   ├── prometheus.yml
│   └── init-db.sql
├── k8s/               # Kubernetes manifests
├── scripts/           # Deployment and maintenance scripts
└── docker-compose.yml # Local development config
```
