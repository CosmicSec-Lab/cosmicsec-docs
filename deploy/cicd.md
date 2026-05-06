# CI/CD Pipelines#

**GitHub Actions & ArgoCD** — automated deployment.

## Overview#

Automate building, testing, and deployment with CI/CD.

## GitHub Actions#

### Workflow Structure#

```
.github/workflows/
├── ci.yml          # Build & test
├── cd.yml          # Deploy to prod
├── security.yml    # Security scans
└── release.yml     # Create releases
```

### Example CI Workflow#

```yaml#
# .github/workflows/ci.yml#
name: CI#

on:#
  push:#
    branches: [ main, develop ]#
  pull_request:#
    branches: [ main ]#

jobs:#
  test:#
    runs-on: ubuntu-latest#
    steps:#
      - uses: actions/checkout@v3#
      - name: Set up Python#
        uses: actions/setup-python@v4#
        with:#
          python-version: '3.11'#
      - name: Install dependencies#
        run: |#
          pip install -r requirements.txt#
          pip install -r requirements-dev.txt#
      - name: Run tests#
        run: pytest --cov=services#
      - name: Lint#
        run: |#
          flake8 services/#
          mypy services/#
```

## ArgoCD#

### Installation#

```bash#
kubectl create namespace argocd#
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml#
```

### Application Configuration#

```yaml#
# cosmicsec-application.yaml#
apiVersion: argoproj.io/v1alpha1#
kind: Application#
metadata:#
  name: cosmicsec#
  namespace: argocd#
spec:#
  project: default#
  source:#
    repoURL: https://github.com/cosmicsec/cosmicsec.git#
    targetRevision: HEAD#
    path: cosmicsec-deploy/k8s#
  destination:#
    server: https://kubernetes.default.svc#
    namespace: cosmicsec#
  syncPolicy:#
    automated:#
      prune: true#
      selfHeal: true#
```

## Deployment Checklist#

- [ ] All tests pass#
- [ ] Lint checks pass#
- [ ] Type hints added#
- [ ] Docstrings added#
- [ ] Security scan clean#
- [ ] Docker images built & pushed#
- [ ] Kubernetes manifests validated#
- [ ] Prometheus queries validated#
- [ ] SQL syntax checked#
- [ ] Documentation updated#

## Automated Checks#

- **Docker Compose validation** — `docker-compose config`#
- **Kubernetes manifest validation** — `kubectl apply --dry-run=client`#
- **Prometheus query validation** — `promtool check rules`#
- **SQL syntax checking** — `sqlfluff lint`#
