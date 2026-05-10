# 🛠️ Developer Guides

## Development Setup

### Prerequisites
- **Docker Desktop** 4.0+ (for containerized dev)
- **Python** 3.11+ (for local Python services)
- **Node.js** 20+ (for frontend)
- **Go** 1.21+ (for Go SDK)
- **Rust** 1.70+ (for Rust accelerators)
- **PostgreSQL** 15+ (or use Docker)
- **Redis** 7+ (or use Docker)

### Local Environment Setup

#### 1. Clone Repository
```bash
git clone https://github.com/cosmicsec/cosmicsec.git
cd cosmicsec
```

#### 2. Start Infrastructure Services
```bash
# Start databases, cache, message queue
docker-compose -f docker-compose.infra.yml up -d

# Verify services are running
docker-compose -f docker-compose.infra.yml ps
```

#### 3. Setup Python Environment
```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # Linux/Mac
# or
venv\Scripts\activate  # Windows

# Install dependencies for all Python services
cd cosmicsec-core
pip install -r requirements.txt

cd ../cosmicsec-services
pip install -r requirements.txt

cd ../cosmicsec-ai
pip install -r requirements/runtime.txt
```

#### 4. Setup Frontend
```bash
cd cosmicsec-web/frontend
npm install
npm run dev
# Frontend running at http://localhost:3000
```

#### 5. Setup CLI
```bash
cd cosmicsec-cli
pip install -e ".[dev,rust]"
cosmicsec --version
```

#### 6. Setup SDKs (Optional)
```bash
# Python SDK
cd cosmicsec-sdk/sdk/python
pip install -e ".[dev]"

# Go SDK
cd cosmicsec-sdk/sdk/go
go build ./...

# TypeScript SDK
cd cosmicsec-sdk/sdk/typescript
npm install
npm run build
```

### IDE Setup

#### VS Code
Install recommended extensions:
```bash
# .vscode/extensions.json
{
  "recommendations": [
    "ms-python.python",
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "bradlc.vscode-tailwindcss",
    "rust-lang.rust-analyzer"
  ]
}
```

Settings:
```json
// .vscode/settings.json
{
  "python.linting.enabled": true,
  "python.linting.pylintEnabled": true,
  "python.formatting.provider": "black",
  "editor.formatOnSave": true,
  "typescript.preferences.preferTypeOnlyAutoImports": true
}
```

#### PyCharm
- Enable type checking (Preferences → Editor → Inspections → Python → Type Checker)
- Configure black formatter (Preferences → Tools → Black)

---

## Running Tests

### Python Tests
```bash
# Run all tests
cd cosmicsec-core
pytest

# Run with coverage
pytest --cov=cosmicsec_platform --cov-report=html

# Run specific test file
pytest tests/test_gateway.py -v

# Run specific test
pytest tests/test_auth.py::test_login_success -v
```

### Frontend Tests
```bash
cd cosmicsec-web/frontend

# Unit tests
npm run test

# Component tests
npm run test:components

# E2E tests
npm run test:e2e

# Coverage
npm run test:coverage
```

### CLI Tests
```bash
cd cosmicsec-cli
pytest

# Test with Rust accelerators
pytest --run-rust
```

### Integration Tests
```bash
# Start all services
docker-compose up -d

# Run integration tests
pytest tests/integration/ -v

# Test specific service
pytest tests/integration/test_scan_service.py -v
```

---

## Contributing Guidelines

### Branching Strategy
```bash
# Create feature branch
git checkout -b feature/add-new-parser

# Create bugfix branch
git checkout -b fix/scan-service-timeout

# Create docs branch
git checkout -b docs/update-api-docs
```

### Commit Messages
Follow Conventional Commits:
```bash
feat(scan-service): add support for custom wordlists
fix(auth-service): resolve JWT expiry issue
docs(readme): update installation instructions
test(cli): add tests for Rust parsers
refactor(ai-service): optimize model ensemble logic
```

### Pull Request Process
1. Fork the repository
2. Create your feature branch
3. Make your changes
4. Add/update tests
5. Update documentation
6. Ensure all tests pass
7. Submit pull request

### Code Review Checklist
- [ ] Code follows style guidelines
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No sensitive data committed
- [ ] All checks pass

---

## API Documentation

### OpenAPI/Swagger
Access interactive API docs at:
- **API Gateway:** http://localhost:8000/docs
- **Auth Service:** http://localhost:8001/docs
- **Scan Service:** http://localhost:8002/docs
- **AI Service:** http://localhost:8003/docs
- (All services expose `/docs` endpoint)

### GraphQL Playground
Access GraphQL IDE at:
- **GraphQL Endpoint:** http://localhost:8000/graphql

**Example Query:**
```graphql
query {
  scans {
    scanId
    target
    status
    vulnerabilities {
      title
      severity
    }
  }
}
```

### Postman Collection
Import the Postman collection:
```bash
# Download collection
curl -O https://raw.githubusercontent.com/cosmicsec/cosmicsec/main/docs/postman/CosmicSec.postman_collection.json

# Import into Postman
# File → Import → Select downloaded file
```

---

## Architecture Deep Dive

### Service Communication
```
Frontend → API Gateway → Microservices → Data Layer
                ↓
          Service Discovery
                ↓
          Circuit Breaker
                ↓
          Rate Limiter
```

### Adding a New Service
1. Create service directory
2. Implement FastAPI app with health endpoint
3. Add to service_discovery.py
4. Add to docker-compose.yml
5. Create Dockerfile
6. Add to Kubernetes manifests
7. Update API gateway routing
8. Add tests
9. Update documentation

### Database Migrations
```bash
# Generate migration
alembic revision --autogenerate -m "Add new table"

# Apply migrations
alembic upgrade head

# Rollback
alembic downgrade -1
```

---

## Debugging

### Service Logs
```bash
# View logs for specific service
docker logs -f scan-service

# Follow logs with grep
docker logs api-gateway 2>&1 | grep "ERROR"

# View last 100 lines
docker logs --tail 100 scan-service
```

### Debugging Python with VS Code
```json
// .vscode/launch.json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Python: FastAPI",
      "type": "python",
      "request": "launch",
      "module": "uvicorn",
      "args": [
        "cosmicsec_platform.main:app",
        "--reload",
        "--port", "8000"
      ],
      "jinja": true
    }
  ]
}
```

### Debugging Frontend
```bash
# Start with source maps
npm run dev -- --sourcemap

# Open Chrome DevTools
# F12 → Sources tab → Set breakpoints
```

### Common Issues

#### Service Not Starting
```bash
# Check logs
docker logs <service-name>

# Check environment variables
docker exec <service-name> env

# Check dependencies
docker exec <service-name> pip list
```

#### Database Connection Error
```bash
# Check PostgreSQL is running
docker ps | grep postgres

# Test connection
docker exec -it postgres psql -U cosmicsec -d cosmicsec -c "SELECT 1"

# Check connection string
echo $DATABASE_URL
```

#### Frontend Can't Connect to API
```bash
# Check API is running
curl http://localhost:8000/health

# Check CORS settings
# Ensure frontend URL is in allowed origins

# Check browser console for errors
# F12 → Console tab
```

---

## Performance Profiling

### Python Profiling
```python
import cProfile
import pstats

def profile_scan():
    profiler = cProfile.Profile()
    profiler.enable()
    
    # Run scan
    ...
    
    profiler.disable()
    stats = pstats.Stats(profiler)
    stats.sort_stats('cumtime')
    stats.print_stats(10)
```

### Frontend Profiling
```bash
# Lighthouse audit
npm run build
npx lighthouse http://localhost:3000 --view

# React DevTools Profiler
# F12 → React tab → Profiler → Start recording
```

---

## Next Steps

- [Complete API Reference](./api-docs.md)
- [Service Discovery](../architecture/service-discovery.md)
- [Deployment Guide](../deploy/overview.md)
- [Contributing to Docs](./contributing.md#documentation)
