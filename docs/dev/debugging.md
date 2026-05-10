# Debugging Guide#

**Troubleshooting CosmicSec Issues.**

## Overview#

Learn how to debug and troubleshoot CosmicSec platform issues.

## Common Issues#

### Service Won't Start#

```bash#
# Check logs#
docker logs cosmicsec-scan-service#

# Check port conflicts#
netstat -an | grep 8002#
```

### Database Connection Issues#

```bash#
# Test PostgreSQL connection#
psql -h localhost -U cosmicsec -d cosmicsec#

# Test Redis connection#
redis-cli ping#
```

### AI Service Errors#

```python#
import logging#
logging.basicConfig(level=logging.DEBUG)#

from cosmicsec_ai import HelixEngine#
engine = HelixEngine()  # Now see debug output#
```

## Debug Tools#

- **Structured logging** — JSON-formatted logs#
- **Distributed tracing** — OpenTelemetry integration#
- **Health checks** — `/health` endpoints#
- **Metrics** — Prometheus endpoints#

## Enabling Debug Mode#

```bash#
# Set environment variable#
export COSMICSEC_DEBUG=true#
export LOG_LEVEL=DEBUG#

# Restart services#
docker-compose restart#
```

## Getting Help#

- **Discord**: [Join our community](https://discord.gg/cosmicsec)#
- **GitHub Issues**: [Report bugs](https://github.com/cosmicsec/cosmicsec/issues)#
- **Documentation**: [docs.cosmicsec.com](https://docs.cosmicsec.com)#
