# Self-Hosting Guide

This guide covers the requirements and steps for self-hosting the CosmicSec platform on your own infrastructure, ensuring maximum privacy and control over your security data.

## 📋 Infrastructure Requirements

For a standard enterprise deployment, we recommend the following minimum specifications:

| Component | CPU | RAM | Storage |
| :--- | :--- | :--- | :--- |
| **API Gateway & Core** | 2 vCPU | 4 GB | 20 GB |
| **Microservices Cluster** | 8 vCPU | 16 GB | 100 GB |
| **AI Helix Engine** | 4 vCPU | 16 GB | 50 GB (SSD) |
| **Databases (Postgres, Redis)**| 4 vCPU | 8 GB | 200 GB (NVMe) |
| **Elasticsearch & Kafka** | 4 vCPU | 16 GB | 500 GB |

*Note: AI Helix performance significantly improves with NVIDIA GPUs (CUDA support).*

## 🏗️ Deployment Options

### 1. Bare Metal / VM (Docker Compose)
Ideal for small organizations or isolated lab environments.
*   **Best for**: < 10 concurrent analysts.
*   **Guide**: See [Quick Start](./quick-start.md).

### 2. Private Cloud (Kubernetes)
The recommended approach for enterprise environments. Supports auto-scaling and high availability.
*   **Platform Support**: AWS (EKS), Azure (AKS), GCP (GKE), and on-premise OpenShift/Rancher.
*   **Installation**: Use the provided Helm charts in `cosmicsec-deploy/k8s/charts/`.

## ⚙️ Configuration Checklist

When self-hosting, ensure you configure the following critical parameters in your `.env` or Kubernetes ConfigMaps:

1.  **Security Keys**: Generate unique `SECRET_KEY`, `JWT_ALGORITHM`, and `ENCRYPTION_KEY`.
2.  **External URLs**: Set `API_BASE_URL` and `FRONTEND_URL` to your specific domains.
3.  **Email/SMTP**: Configure your mail server for notifications and 2FA.
4.  **Persistence**: Configure automated backups for PostgreSQL and MongoDB.
5.  **SSL/TLS**: Use a reverse proxy (Traefik/Nginx) with valid certificates (Let's Encrypt or Corporate CA).

## 🛡️ Hardening Recommendations

*   **Isolated Network**: Deploy the platform within a private VPC/Subnet.
*   **mTLS Enforcement**: Ensure `MTLS_STRICT=true` is set in the Core configuration.
*   **VPN Access**: Only expose the Web Frontend and API Gateway through a secure VPN or ZTNA solution.
*   **Audit Logging**: Forward platform logs to a centralized, write-once SIEM for auditing.

## 🔄 Updates & Maintenance

1.  **Backup**: Always perform a full database snapshot before updating.
2.  **Pull Images**: Update your local image tags.
3.  **Apply Migrations**: Run the Alembic migrations in `cosmicsec-services` and `cosmicsec-core`.
4.  **Restart**: Rolling restart of services to apply changes.

```bash
# Example update command
docker-compose pull
docker-compose run --rm core alembic upgrade head
docker-compose up -d
```

## 🆘 Support for Self-Hosting

For enterprise support and customized installation assistance, please contact `support@cosmicsec.com`.
