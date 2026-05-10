# Deployment Checklist

**Complete checklist for deploying CosmicSec to production.**

## Pre-Deployment

### Code Quality
- [ ] All tests pass (`pytest`, `npm test`)#
- [ ] Lint checks pass (`flake8`, `mypy`, `eslint`)#
- [ ] Type hints added to all public functions#
- [ ] Docstrings added to all public functions#
- [ ] No `print()` statements (use `logger` instead)#
- [ ] No `pass` statements (use `raise NotImplementedError()`)#
- [ ] No `verify=False` in SSL contexts#

### Security
- [ ] No hardcoded secrets (use env vars or Vault)#
- [ ] Input validation implemented for user-supplied targets#
- [ ] Output sanitized to prevent XSS in reports#
- [ ] `dataclasses` typo fixed (214 files)#
- [ ] `redis.asyncio` imports fixed#
- [ ] Empty modules implemented (7 cosmicsec-core modules)#

### Documentation
- [ ] All placeholder doc files expanded with actual content#
- [ ] API documentation complete (`/docs` endpoint)#
- [ ] Architecture diagrams created#
- [ ] Link checker passes (no broken links)#

### Infrastructure
- [ ] Dockerfiles created for all 24 services#
- [ ] Docker images built and pushed to registry#
- [ ] Kubernetes manifests validated (`kubectl apply --dry-run=client`)#
- [ ] Prometheus queries validated (`promtool check rules`)#
- [ ] SQL syntax checked (`sqlfluff lint`)#

## Deployment Steps

### 1. Build All Docker Images
```bash#
# Build each service image#
docker build -t cosmicsec/api-gateway:2.0.0 cosmicsec-core/#
docker build -t cosmicsec/auth-service:2.0.0 cosmicsec-services/services/auth_service/#
docker build -t cosmicsec/scan-service:2.0.0 cosmicsec-services/services/scan_service/#
docker build -t cosmicsec/ai-service:2.0.0 cosmicsec-ai/services/ai_service/#
docker build -t cosmicsec/deepintel:2.0.0 cosmicsec-deepintel/#
docker build -t cosmicsec/frontend:2.0.0 cosmicsec-web/frontend/#

# Push to registry#
docker push cosmicsec/api-gateway:2.0.0#
docker push cosmicsec/auth-service:2.0.0#
docker push cosmicsec/scan-service:2.0.0#
docker push cosmicsec/ai-service:2.0.0#
docker push cosmicsec/deepintel:2.0.0#
docker push cosmicsec/frontend:2.0.0#
```

### 2. Create Kubernetes Secrets
```bash#
kubectl create namespace cosmicsec#

kubectl create secret generic cosmicsec-secrets \
  --from-literal=postgres-password='YOUR_POSTGRES_PASSWORD' \
  --from-literal=redis-password='YOUR_REDIS_PASSWORD' \
  --from-literal=mongo-password='YOUR_MONGO_PASSWORD' \
  --from-literal=jwt-secret-key='YOUR_JWT_SECRET' \
  -n cosmicsec#
```

### 3. Deploy with Helm
```bash#
helm install cosmicsec ./cosmicsec-deploy/helm/ \
  --namespace cosmicsec \
  --set postgres.password='YOUR_POSTGRES_PASSWORD' \
  --set redis.password='YOUR_REDIS_PASSWORD' \
  --set mongo.password='YOUR_MONGO_PASSWORD' \
  --set image.tag="2.0.0"#
```

### 4. Verify Deployment
```bash#
# Check pods#
kubectl get pods -n cosmicsec#

# Check services#
kubectl get svc -n cosmicsec#

# Check ingress#
kubectl get ingress -n cosmicsec#

# Port-forward to access#
kubectl port-forward svc/api-gateway 8000:8000 -n cosmicsec#
```

### 5. Run Smoke Tests
```bash#
# Test API endpoint#
curl http://localhost:8000/health#

# Test frontend#
curl http://localhost:3000/#

# Test AI service#
curl http://localhost:8003/health#
```

## Post-Deployment

### Monitoring
- [ ] Grafana dashboards imported#
- [ ] Prometheus targets visible#
- [ ] Alertmanager configured#
- [ ] Log aggregation setup#

### Security
- [ ] SSL/TLS certificates valid#
- [ ] Firewall rules configured#
- [ ] Secrets manager integration tested#
- [ ] Penetration test performed#

### Backup
- [ ] Database backup configured#
- [ ] Configuration backup configured#
- [ ] Disaster recovery plan tested#

## Rollback Plan
```bash#
# Helm rollback#
helm rollback cosmicsec 1 -n cosmicsec#

# Or uninstall and redeploy#
helm uninstall cosmicsec -n cosmicsec#
helm install cosmicsec ./cosmicsec-deploy/helm/ --namespace cosmicsec#
```

## Support
- **Documentation**: [docs.cosmicsec.com](https://docs.cosmicsec.com)#
- **Issues**: [GitHub Issues](https://github.com/cosmicsec/cosmicsec/issues)#
- **Discord**: [Join our community](https://discord.gg/cosmicsec)#
