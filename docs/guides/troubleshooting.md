# 🔧 Troubleshooting & FAQ

## Common Issues & Solutions

---

## 🔐 Authentication Issues

### Problem: "Invalid JWT token"
**Symptoms:**
```json
{
  "error": {
    "code": "AUTHENTICATION_ERROR",
    "message": "Invalid or expired token"
  }
}
```

**Solutions:**
1. Check token hasn't expired:
```bash
# Decode JWT (without verification)
echo "eyJhbG..." | cut -d'.' -f2 | base64 -d | jq .

# Check "exp" field (Unix timestamp)
```

2. Refresh token:
```bash
curl -X POST http://localhost:8001/api/v1/auth/refresh \
  -H "Content-Type: application/json" \
  -d '{"refresh_token": "eyJhbG..."}'
```

3. Re-login:
```bash
cosmicsec auth login --email user@company.com
```

---

### Problem: "Permission denied" (403 Forbidden)
**Symptoms:**
```json
{
  "error": {
    "code": "PERMISSION_DENIED",
    "message": "Insufficient permissions. Required: scan:create"
  }
}
```

**Solutions:**
1. Check user role and permissions:
```bash
curl http://localhost:8001/api/v1/auth/profile \
  -H "Authorization: Bearer <token>" | jq .permissions
```

2. Request admin to grant permission:
```bash
# Admin only
curl -X PUT http://localhost:8001/api/v1/admin/users/user_12345/role \
  -H "Authorization: Bearer <admin_token>" \
  -d '{"role": "analyst"}'
```

3. Use API key with correct permissions:
```bash
curl http://localhost:8000/api/v1/scans \
  -H "Authorization: ApiKey cs_live_..."  # API key with scan:create
```

---

## 🔍 Scan Issues

### Problem: Scan stuck at 0%
**Symptoms:**
- Scan status shows "in_progress" but progress remains 0%
- No logs being generated

**Solutions:**
1. Check scan service logs:
```bash
docker logs scan-service --tail 50 | grep scan_12345
```

2. Verify tool is installed:
```bash
docker exec scan-service nmap --version
docker exec scan-service nuclei --version
```

3. Check worker availability:
```bash
docker ps | grep scan-worker
# If no workers, scale up:
docker-compose up -d --scale scan-worker=3
```

4. Check Redis queue:
```bash
docker exec -it redis redis-cli
> LLEN scan_queue
> LRANGE scan_queue 0 -1
```

---

### Problem: Tool not found
**Symptoms:**
```json
{
  "error": {
    "code": "TOOL_NOT_FOUND",
    "message": "nuclei: command not found"
  }
}
```

**Solutions:**
1. Install missing tool:
```bash
# In docker-compose.yml, ensure tool is installed
# Or install manually in container:
docker exec -it scan-service bash
apt-get update && apt-get install -y nmap nuclei
```

2. Check PATH in container:
```bash
docker exec scan-service echo $PATH
docker exec scan-service which nmap
```

---

## 🤖 AI Service Issues

### Problem: AI request timeout
**Symptoms:**
```json
{
  "error": {
    "code": "TIMEOUT",
    "message": "AI request timed out after 30 seconds"
  }
}
```

**Solutions:**
1. Check AI provider status:
```bash
curl http://localhost:8003/ai/models
```

2. Increase timeout:
```bash
# In AI service environment
export AI_REQUEST_TIMEOUT=60
```

3. Switch to faster model:
```python
# Use Llama (local) instead of GPT-4o (API)
client.ai.analyze(
    content="...",
    models=["llama-3.1-70b"]  # Local, faster
)
```

---

### Problem: "Model not available"
**Solutions:**
1. Check Ollama (for local models):
```bash
curl http://localhost:11434/api/tags
# Should list available models
```

2. Pull model if missing:
```bash
docker exec ollama ollama pull llama3.1:70b
```

3. Verify API keys for cloud models:
```bash
# Check environment variables
docker exec ai-service env | grep API_KEY
```

---

## 🌐 Frontend Issues

### Problem: "Cannot connect to API"
**Symptoms:**
- Frontend shows "API Unavailable"
- Browser console shows CORS errors

**Solutions:**
1. Check API gateway is running:
```bash
curl http://localhost:8000/health
```

2. Verify CORS settings:
```bash
# In API gateway config
CORS_ORIGINS=http://localhost:3000,https://your-domain.com
```

3. Check browser console (F12):
- Network tab: Check failed requests
- Console tab: Look for CORS errors

---

### Problem: 3D visualization not rendering
**Symptoms:**
- Blank canvas where 3D viz should be
- WebGL errors in console

**Solutions:**
1. Check WebGL support:
```javascript
// In browser console
const canvas = document.createElement('canvas');
const gl = canvas.getContext('webgl') || canvas.getContext('experimental-webgl');
console.log('WebGL supported:', !!gl);
```

2. Update graphics drivers
3. Try different browser (Chrome recommended for WebGL)

---

## 🖥️ CLI Issues

### Problem: "cosmicsec: command not found"
**Solutions:**
1. Reinstall CLI:
```bash
pip install --upgrade cosmicsec-cli
```

2. Check PATH:
```bash
echo $PATH
which cosmicsec
```

3. Use python -m:
```bash
python -m cosmicsec_cli.main scan list
```

---

### Problem: Rust accelerators not working
**Symptoms:**
```bash
cosmicsec scan parse nmap --file scan.xml --rust
# Error: Rust accelerators not installed
```

**Solutions:**
1. Install Rust:
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```

2. Build Rust parsers:
```bash
cd cosmicsec-cli/cli/agent/rust_parsers
cargo build --release
```

3. Reinstall Python package:
```bash
pip install -e ".[rust]"
```

---

## 📦 Docker Issues

### Problem: "Port already in use"
**Symptoms:**
```bash
Error: Bind for 0.0.0.0:8000 failed: port is already allocated
```

**Solutions:**
1. Find process using port:
```bash
# Linux/Mac
lsof -i :8000

# Windows
netstat -ano | findstr :8000
```

2. Stop conflicting container:
```bash
docker ps  # Find container using port
docker stop <container_id>
```

3. Change port mapping in docker-compose.yml:
```yaml
services:
  api-gateway:
    ports:
      - "8001:8000"  # Map host port 8001 to container 8000
```

---

### Problem: Containers can't communicate
**Symptoms:**
- API gateway can't reach auth service
- "Connection refused" errors

**Solutions:**
1. Use service names (not localhost) in docker-compose:
```yaml
# Wrong:
url: "http://localhost:8001"

# Correct:
url: "http://auth-service:8001"
```

2. Check Docker network:
```bash
docker network ls
docker network inspect cosmicsec_default
```

3. Restart all services:
```bash
docker-compose down && docker-compose up -d
```

---

## 📊 Database Issues

### Problem: "Database connection failed"
**Solutions:**
1. Check PostgreSQL is running:
```bash
docker ps | grep postgres
```

2. Test connection:
```bash
docker exec -it postgres psql -U cosmicsec -d cosmicsec -c "SELECT 1"
```

3. Check connection string:
```bash
echo $DATABASE_URL
# Should be: postgresql://cosmicsec:password@postgres:5432/cosmicsec
```

---

### Problem: "Migration pending"
**Solutions:**
1. Run migrations:
```bash
cd cosmicsec-core
alembic upgrade head
```

2. Check migration status:
```bash
alembic current
alembic history
```

---

## 🔧 FAQ

### Q: How do I reset my password?
**A:** Use the password reset endpoint:
```bash
curl -X POST http://localhost:8001/api/v1/auth/password-reset \
  -d '{"email": "user@company.com"}'
# Check email for reset link
```

---

### Q: Can I run scans without the web UI?
**A:** Yes! Use the CLI or SDK:
```bash
# CLI
cosmicsec scan create --target example.com --type web_app

# Python
from cosmicsec import CosmicSecClient
client = CosmicSecClient(api_key="...")
scan = client.scans.create(target="example.com", scan_type="web_app")
```

---

### Q: How do I add custom themes?
**A:** Use the Theme Builder at `/theme-builder` or import JSON:
```bash
curl -X POST http://localhost:8029/api/v1/themes/import \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d @my-theme.json
```

---

### Q: Can I use CosmicSec offline?
**A:** Partially. You can use:
- Local AI models (Ollama + Llama)
- Local tool installations (Nmap, Nuclei)
- Cached scan results

But features requiring external APIs (OpenAI, VirusTotal) won't work.

---

### Q: How do I backup my data?
**A:** Backup PostgreSQL and MinIO:
```bash
# PostgreSQL
docker exec postgres pg_dump -U cosmicsec cosmicsec > backup.sql

# MinIO
mc mirror --overwrite /data minio/cosmicsec /backup/cosmicsec
```

---

### Q: How do I scale for high traffic?
**A:** Scale horizontally:
```bash
# Docker Compose
docker-compose up -d --scale scan-service=5

# Kubernetes
kubectl scale deployment scan-service --replicas=10
```

---

### Q: Is my data encrypted?
**A:** Yes, at multiple levels:
- **Transit:** TLS/HTTPS for all API calls
- **Rest:** PostgreSQL encryption at rest
- **Quantum-Ready:** Kyber/Dilithium for sensitive data

---

### Q: Can I integrate with my CI/CD?
**A:** Yes! Use the CLI or SDKs:
```yaml
# GitHub Actions example
- name: Run CosmicSec Scan
  run: |
    cosmicsec scan create \
      --target ${{ steps.get_url.outputs.url }} \
      --type web_app \
      --wait
```

---

### Q: How do I get support?
**A:** Multiple channels:
- **Discord:** [Join our community](https://discord.gg/cosmicsec)
- **GitHub Issues:** [Report bugs](https://github.com/cosmicsec/cosmicsec/issues)
- **Email:** support@cosmicsec.ai
- **Documentation:** [docs.cosmicsec.ai](https://docs.cosmicsec.ai)

---

## Next Steps

- [Quick Start Guide](../guides/quickstart.md)
- [Developer Setup](../dev/setup.md)
- [API Reference](./api-docs.md)
- [Architecture Overview](../architecture/overview.md)
