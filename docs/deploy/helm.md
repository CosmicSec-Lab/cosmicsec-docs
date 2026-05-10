# Helm Charts#

**Packaged Kubernetes Applications** — easy deployment.

## Overview#

Deploy CosmicSec using Helm charts for production.

## Prerequisites#

- **Kubernetes** 1.24+ cluster#
- **Helm** 3.0+#
- **kubectl** 1.24+#

## Installation#

```bash#
# Add CosmicSec Helm repo#
helm repo add cosmicsec https://charts.cosmicsec.com#
helm repo update#

# Install CosmicSec#
helm install cosmicsec ./cosmicsec-deploy/helm/ \
  --namespace cosmicsec \
  --set postgres.password='your-password' \
  --set redis.password='your-password' \
  --set mongo.password='your-password'#
```

## Configuration#

```bash#
# Custom values file#
helm install cosmicsec ./cosmicsec-deploy/helm/ \
  -f my-values.yaml \
  --namespace cosmicsec#
```

Example `my-values.yaml`:
```yaml#
replicaCount: 3#

postgres:
  password: "secure-password"#

redis:
  password: "secure-password"#

mongo:
  password: "secure-password"#

ingress:
  enabled: true
  hosts:
    - cosmicsec.example.com#
```

## Upgrading#

```bash#
# Upgrade to new version#
helm upgrade cosmicsec ./cosmicsec-deploy/helm/ \
  --namespace cosmicsec \
  --set image.tag="2.0.0"#
```

## Rollback#

```bash#
# Rollback to previous release#
helm rollback cosmicsec 1 -n cosmicsec#
```

## Uninstall#

```bash#
# Remove CosmicSec#
helm uninstall cosmicsec -n cosmicsec#
```
