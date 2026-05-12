# Deployment Guide

## Overview

CosmicSec-Lab supports **multiple deployment environments** from local Docker Compose to production Kubernetes with high availability.

---

## 1. Local Development (Docker Compose)

### Quick Start

```bash
# Clone repository
git clone https://github.com/your-org/cosmicsec-lab.git
cd cosmicsec-lab

# Start all services
docker-compose up -d

# Verify health
curl http://localhost:8000/health
```

### Services Started

| Service | Port | Description |
|---------|------|-------------|
| API Gateway | 8000 | Main entry point |
| Auth Service | 8001 | Authentication |
| Scan Service | 8002 | Vulnerability scanning |
| AI Service | 8003 | Helix AI engine |
| Recon Service | 8004 | OSINT reconnaissance |
| DeepIntel PRO | 8032 | Dark web intelligence |

### Environment Variables

```bash
# .env.example
DATABASE_URL=postgresql://user:pass@localhost:5432/cosmicsec
REDIS_URL=redis://localhost:6379
ELASTICSEARCH_URL=http://localhost:9200
MINIO_ENDPOINT=localhost:9000
KAFKA_BROKERS=localhost:9092

# Auth
JWT_SECRET=your-super-secret-jwt-key
SSO_ENABLED=false

# AI Provider
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
OLLAMA_BASE_URL=http://localhost:11434
```

---

## 2. Production Kubernetes

### Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Load Balancer                         │
└───────────────────────┬─────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────┐
│               API Gateway (3 replicas)                    │
│            (HPA based on CPU/memory)                     │
└───────┬───────────┬───────────┬─────────────────────────┘
        │           │           │
┌───────▼──┐ ┌──────▼──┐ ┌─────▼────┐ ┌───────────────────┐
│ Auth Svc │ │ Scan Svc│ │ AI Svc   │ │ Recon/Collab/...   │
│(3 rep)  │ │(3 rep)  │ │(3 rep)   │ │    (3 rep each)    │
└──────────┘ └──────────┘ └──────────┘ └───────────────────┘
        │           │           │
        └───────────┴───────────┘
                    │
┌───────────────────┴───────────────────────────────────┐
│                    Data Layer                           │
│  PostgreSQL │ Redis │ ES │ Kafka │ MinIO (per-service) │
└───────────────────────────────────────────────────────┘
```

### Helm Charts

```bash
# Install API Gateway
helm install api-gateway cosmicsec/api-gateway \
  --set replicaCount=3 \
  --set resources.limits.cpu=1000m \
  --set resources.limits.memory=2Gi

# Install AI Service
helm install ai-service cosmicsec/ai-service \
  --set openai.apiKey=$OPENAI_API_KEY \
  --set anthropic.apiKey=$ANTHROPIC_API_KEY
```

### HPA Configuration

```yaml
# kubernetes/hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-gateway-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api-gateway
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

---

## 3. Service Mesh

### Istio Configuration

```yaml
# kubernetes/service-mesh/virtual-service.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: auth-service
spec:
  hosts:
  - auth-service
  http:
  - route:
    - destination:
        host: auth-service
        subset: v1
      weight: 100
    retries:
      attempts: 3
      perTryTimeout: 5s
    timeout: 10s
```

### Circuit Breaker

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: auth-service-cb
spec:
  host: auth-service
  trafficPolicy:
    connectionPool:
      http:
        h2UpgradePolicy: UPGRADE
    outlierDetection:
      consecutiveGatewayErrors: 10
      interval: 60s
      baseEjectionTime: 30s
```

---

## 4. CI/CD Pipeline

### GitHub Actions

```yaml
# .github/workflows/deploy.yml
name: Deploy to Kubernetes

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build Docker images
        run: |
          docker-compose build

      - name: Push to ACR
        run: |
          az acr build \
            --registry ${{ secrets.ACR_REGISTRY }} \
            --image cosmicsec:${{ github.sha }} .

  deploy-staging:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Helm Deploy to Staging
        run: |
          helm upgrade staging cosmicsec/api-gateway \
            --set image.tag=${{ github.sha }}
        env:
          KUBECONFIG: ${{ secrets.KUBE_CONFIG_STAGING }}

  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment:
      name: production
    steps:
      - uses: actions/checkout@v4
      - name: Helm Deploy to Production
        run: |
          helm upgrade production cosmicsec/api-gateway \
            --set image.tag=${{ github.sha }}
        env:
          KUBECONFIG: ${{ secrets.KUBE_CONFIG_PRODUCTION }}
```

---

## 5. Observability Stack

### Prometheus Metrics

```yaml
# kubernetes/monitoring/prometheus.yaml
apiVersion: monitoring.coreos.com/v1
kind: Prometheus
metadata:
  name: cosmicsec
spec:
  serviceAccountName: prometheus
  serviceMonitorSelector:
    matchLabels:
      team: cosmicsec
  resources:
    requests:
      storage: 30Gi
```

### Grafana Dashboards

| Dashboard | Description |
|-----------|-------------|
| **Service Health** | Uptime, latency, errors |
| **Security Alerts** | RBAC violations, rate limits |
| **AI Usage** | Token consumption, model costs |
| **Business Metrics** | Scans created, findings detected |

### Jaeger Tracing

```yaml
# kubernetes/tracing/jaeger.yaml
apiVersion: jaegertracing.io/v1
kind: Jaeger
metadata:
  name: cosmicsec-tracing
spec:
  strategy: allInOne
  allInOne:
    resources:
      limits:
        memory: 2Gi
```

---

## 6. Deployment Checklist

### Pre-Deployment
- [ ] PostgreSQL migrated (latest migration)
- [ ] Redis cache warmed
- [ ] AI model registry populated
- [ ] DNS configured for all services
- [ ] TLS certificates issued
- [ ] SSO IdP configured (if enabled)

### Post-Deployment
- [ ] Health checks passing
- [ ] Circuit breaker states verified
- [ ] Rate limits configured
- [ ] Prometheus scraping active
- [ ] Grafana dashboards operational
- [ ] Slack/Teams alerting configured

### Rollback Procedure

```bash
# Rollback to previous version
helm rollback api-gateway 1

# Verify rollback
kubectl rollout status deployment/api-gateway
```

---

## Related Links

- [Architecture Overview](../architecture/overview.md)
- [API Gateway](../architecture/gateway.md)
- [Security & RBAC](../security/overview.md)