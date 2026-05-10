# 🔐 API Gateway & Service Mesh

## Overview

The **API Gateway** is the central entry point for all client requests. It handles routing, authentication, rate limiting, circuit breaking, and service discovery.

**Location:** `cosmicsec-core/cosmicsec_platform/`  
**Port:** 8000  
**Framework:** FastAPI with hybrid REST + GraphQL

## Features

### 1. Hybrid Router (REST + GraphQL)
- **REST API:** Traditional endpoints for simple operations
- **GraphQL:** Federation across all microservices
- **Single Entry Point:** Clients only need one URL

### 2. Service Discovery
- Dynamic service registry
- Health check monitoring
- Automatic failover

### 3. Circuit Breaker
- Prevents cascade failures
- 10 failures in 60 seconds → circuit opens
- Automatic retry after timeout

### 4. Rate Limiting
- Per-service configurable limits
- 100-2000 requests/minute
- Token bucket algorithm

### 5. Authentication & Authorization
- JWT token validation
- RBAC enforcement
- SSO integration (SAML, OIDC, OAuth2)

### 6. Request Routing
- Path-based routing to microservices
- Load balancing across replicas
- Request/response transformation

## API Endpoints

### Health & Monitoring
```http
GET /health
```
Returns gateway health status.

```http
GET /api/v1/gateway/services
```
Lists all registered services and their health.

```http
GET /metrics
```
Prometheus metrics endpoint.

### GraphQL Endpoint
```http
POST /graphql
```
GraphQL federation endpoint for complex queries.

**Example Query:**
```graphql
query {
  scans {
    id
    target
    status
    vulnerabilities {
      id
      severity
      description
    }
  }
  threats {
    id
    type
    severity
    source
  }
}
```

### REST API Proxy
All REST requests are proxied to appropriate microservices:

```http
/api/v1/auth/* → Auth Service (8001)
/api/v1/scans/* → Scan Service (8002)
/api/v1/ai/* → AI Service (8003)
/api/v1/recon/* → Recon Service (8004)
...
```

## Service Discovery

### Registration
Services register themselves on startup:

```python
# In each microservice
await register_service(
    name="scan-service",
    url="http://scan-service:8002",
    health_endpoint="/health"
)
```

### Health Monitoring
Gateway polls `/health` endpoint every 30 seconds:
- ✅ Healthy: Route traffic
- ⚠️ Degraded: Reduce traffic
- ❌ Unhealthy: Stop routing, alert

### Service Registry (service_discovery.py)
```python
SERVICES = {
    "auth-service": ServiceConfig(port=8001, ...),
    "scan-service": ServiceConfig(port=8002, ...),
    "ai-service": ServiceConfig(port=8003, ...),
    # ... 25+ services
}
```

## Circuit Breaker Implementation

```python
class CircuitBreaker:
    def __init__(self, failure_threshold=10, recovery_timeout=60):
        self.failure_count = 0
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.state = "CLOSED"  # CLOSED | OPEN | HALF_OPEN
    
    async def call(self, func):
        if self.state == "OPEN":
            if time_since_open > self.recovery_timeout:
                self.state = "HALF_OPEN"
            else:
                raise CircuitBreakerOpen("Service unavailable")
        
        try:
            result = await func()
            self._reset()
            return result
        except Exception as e:
            self._record_failure()
            raise e
```

## Rate Limiting

### Configuration
```python
RATE_LIMITS = {
    "auth-service": RateLimit(requests=2000, window=60),
    "scan-service": RateLimit(requests=500, window=60),
    "ai-service": RateLimit(requests=1000, window=60),
    # ... per-service limits
}
```

### Headers
Rate limit info in response headers:
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1620000000
```

## Authentication Flow

```
1. User sends credentials → POST /api/v1/auth/login
2. Auth Service validates → Returns JWT token
3. Client includes token in Authorization header:
   Authorization: Bearer <token>
4. Gateway validates JWT → Extracts user info + permissions
5. Gateway checks RBAC → Allows/denies request
6. Request forwarded to microservice with user context
```

### JWT Structure
```json
{
  "sub": "user_id",
  "email": "user@company.com",
  "role": "analyst",
  "permissions": ["scan:create", "report:read"],
  "exp": 1620000000
}
```

## GraphQL Federation

### Schema Stitching
Each service contributes a GraphQL schema:

```python
# scan-service schema
type Scan {
  id: ID!
  target: String!
  status: ScanStatus!
  vulnerabilities: [Vulnerability!]!
}

type Query {
  scans: [Scan!]!
  scan(id: ID!): Scan
}
```

Gateway combines all schemas into unified endpoint.

### Resolvers
```python
@strawberry.type
class Query:
    @strawberry.field
    async def scans(self) -> List[Scan]:
        # Fetch from scan-service via REST or gRPC
        response = await http_client.get(
            "http://scan-service:8002/api/v1/scans"
        )
        return response.json()
```

## Error Handling

### Standard Error Response
```json
{
  "error": {
    "code": "SERVICE_UNAVAILABLE",
    "message": "Scan service is currently unavailable",
    "details": {
      "service": "scan-service",
      "circuit_breaker_state": "OPEN"
    }
  }
}
```

### HTTP Status Codes
- **200** — Success
- **400** — Bad Request (validation error)
- **401** — Unauthorized (invalid/missing token)
- **403** — Forbidden (insufficient permissions)
- **404** — Not Found (resource doesn't exist)
- **429** — Too Many Requests (rate limit exceeded)
- **500** — Internal Server Error
- **503** — Service Unavailable (circuit breaker open)

## Configuration

### Environment Variables
```bash
# Gateway
GATEWAY_PORT=8000
GATEWAY_HOST=0.0.0.0

# Service Discovery
SERVICE_REGISTRY_TTL=300
HEALTH_CHECK_INTERVAL=30

# Circuit Breaker
CIRCUIT_BREAKER_FAILURE_THRESHOLD=10
CIRCUIT_BREAKER_RECOVERY_TIMEOUT=60

# Rate Limiting
RATE_LIMIT_ENABLED=true
REDIS_URL=redis://redis:6379

# Auth
JWT_SECRET=your-secret-key
JWT_ALGORITHM=HS256
JWT_EXPIRY_HOURS=24

# GraphQL
GRAPHQL_ENABLED=true
GRAPHQL_PATH=/graphql
```

## Monitoring

### Metrics Exposed
- `gateway_requests_total` — Total requests by service
- `gateway_request_duration_seconds` — Request latency
- `gateway_circuit_breaker_state` — Circuit state (0=closed, 1=open)
- `gateway_rate_limit_exceeded_total` — Rate limit violations
- `gateway_service_health` — Service health status (0=unhealthy, 1=healthy)

### Grafana Dashboard
Pre-built dashboard shows:
- Request volume by service
- Error rates
- Latency percentiles (p50, p95, p99)
- Circuit breaker states
- Rate limit violations

## Deployment

### Docker
```yaml
services:
  api-gateway:
    build: ./cosmicsec-core
    ports:
      - "8000:8000"
    environment:
      - REDIS_URL=redis://redis:6379
      - JWT_SECRET=${JWT_SECRET}
    depends_on:
      - redis
```

### Kubernetes
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-gateway
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: api-gateway
        image: cosmicsec/api-gateway:latest
        ports:
        - containerPort: 8000
        env:
        - name: REDIS_URL
          value: "redis://redis-service:6379"
```

## Testing

### Unit Tests
```bash
cd cosmicsec-core
pytest tests/test_gateway.py -v
```

### Integration Tests
```bash
# Start all services
docker-compose up -d

# Run integration tests
pytest tests/integration/test_api_gateway.py -v
```

### Load Testing
```bash
# Using wrk
wrk -t4 -c100 -d30s http://localhost:8000/health

# Using artillery
artillery run load-tests/gateway.yml
```

## Troubleshooting

### Circuit Breaker Stuck Open
```bash
# Check circuit breaker state
curl http://localhost:8000/api/v1/gateway/services

# Manually reset (admin only)
curl -X POST http://localhost:8000/api/v1/admin/circuit-breaker/reset \
  -H "Authorization: Bearer <admin_token>" \
  -H "Content-Type: application/json" \
  -d '{"service": "scan-service"}'
```

### Service Not Discovering
```bash
# Check service health
curl http://localhost:8002/health

# Check gateway logs
docker logs api-gateway | grep "service_discovery"

# Manually register
curl -X POST http://localhost:8000/api/v1/admin/services/register \
  -H "Authorization: Bearer <admin_token>" \
  -d '{"name": "scan-service", "url": "http://scan-service:8002"}'
```

### Rate Limit Issues
```bash
# Check current rate limit
curl -i http://localhost:8000/api/v1/scans \
  -H "Authorization: Bearer <token>"

# Look for X-RateLimit-* headers
```

## Next Steps

- [Service Discovery Details](./service-discovery.md)
- [RBAC & SSO Configuration](../security/rbac-sso.md)
- [Rate Limiting Guide](../guides/api-integration.md)
- [GraphQL Schema Reference](../dev/api-docs.md)
