# Deploy Overview#

**Deployment Options** — Docker, Kubernetes, Helm.

## Overview#

Deploy CosmicSec using Docker Compose, Kubernetes, or Helm.

## Deployment Methods#

- **Docker Compose** — local development, small deployments#
- **Kubernetes** — production, large-scale deployments#
- **Helm Charts** — packaged Kubernetes applications#
- **Terraform/Pulumi** — infrastructure as code#

## Prerequisites#

- **Docker** 20.10+ (for Docker Compose)#
- **Kubernetes** 1.24+ (for K8s deployment)#
- **Helm** 3.0+ (for Helm charts)#
- **Terraform** 1.0+ (optional, for IaC)#

## Quick Start#

```bash#
# Docker Compose (local)#
git clone https://github.com/cosmicsec/cosmicsec.git#
cd cosmicsec#
docker-compose up -d#

# Kubernetes (production)#
kubectl apply -f cosmicsec-deploy/k8s/#

# Helm (packaged)#
helm install cosmicsec ./cosmicsec-deploy/helm/#
```

## Configuration#

Environment variables (set before deployment):
- `POSTGRES_PASSWORD` — PostgreSQL password#
- `REDIS_PASSWORD` — Redis password#
- `MONGO_PASSWORD` — MongoDB password#
- `JWT_SECRET_KEY` — JWT signing key (use Vault in production)#

## Next Steps#

- [Docker Compose Guide](./docker-compose.md)#
- [Kubernetes Guide](./kubernetes.md)#
- [Helm Charts](./helm.md)#
- [IaC with Terraform](./iac.md)#
