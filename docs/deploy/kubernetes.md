# Kubernetes#

**Production Orchestration** — K8s deployment.

## Overview#

Deploy CosmicSec to Kubernetes for production-grade scalability.

## Prerequisites#

- **Kubernetes** 1.24+ cluster#
- **kubectl** 1.24+#
- **Helm** 3.0+ (optional)#

## Deployment Steps#

```bash#
# Apply all manifests#
kubectl apply -f cosmicsec-deploy/k8s/#

# Check deployment status#
kubectl get pods -n cosmicsec#

# Check services#
kubectl get svc -n cosmicsec#
```

## Namespaces#

```bash#
# Create namespace#
kubectl create namespace cosmicsec#

# Set context#
kubectl config set-context --current --namespace=cosmicsec#
```

## Services Deployed#

- **API Gateway** — main entry point (3 replicas)#
- **Auth Service** — authentication (2 replicas)#
- **Scan Service** — vulnerability scanning (5 replicas)#
- **AI Service** — AI analysis (3 replicas)#
- **DeepIntel** — dark web intelligence (2 replicas)#
- **Frontend** — web UI (3 replicas)#

## Configuration#

```bash#
# Create secrets#
kubectl create secret generic cosmicsec-secrets \
  --from-literal=postgres-password='your-password' \
  --from-literal=redis-password='your-password' \
  --from-literal=mongo-password='your-password' \
  --from-literal=jwt-secret-key='your-secret-key' \
  -n cosmicsec#

# Apply secrets#
kubectl apply -f cosmicsec-deploy/k8s/secrets.yaml#
```

## Scaling#

```bash#
# Scale a service#
kubectl scale deployment api-gateway --replicas=5 -n cosmicsec#

# Auto-scaling#
kubectl apply -f cosmicsec-deploy/k8s/hpa.yaml#
```

## Monitoring#

```bash#
# Install Prometheus & Grafana#
kubectl apply -f cosmicsec-deploy/k8s/monitoring/#

# Access Grafana#
kubectl port-forward svc/grafana 3000:3000 -n cosmicsec#
```
