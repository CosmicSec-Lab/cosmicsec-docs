# API Gateway

## Overview

The API Gateway (`cosmicsec-core/api_gateway/`) is the central entry point for all client traffic, providing routing, authentication, rate limiting, and service discovery.

## Technology Stack

- **Framework**: FastAPI
- **GraphQL**: Strawberry GraphQL federation
- **Protocols**: REST, GraphQL, WebSocket
- **Security**: JWT validation, RBAC enforcement, mTLS

## Core Features

### 1. Hybrid Router

```python
# core/routers/ -> Hybrid REST + GraphQL
/api/v1/...          # REST endpoints
/graphql             # GraphQL federation
/websocket           # WebSocket real-time
```

### 2. Service Discovery

Dynamic service registry with health checks:

```python
# core/service_discovery.py
_services = {
    "auth": "http://auth:8001",
    "scan": "http://scan:8002",
    "ai": "http://ai:8003",
    "recon": "http://recon:8004",
}
```

### 3. Circuit Breaker

Pattern: 10 failures / 60s window → OPEN → HALF_OPEN after 30s

```python
# core/circuit_breaker.py
class CircuitBreaker:
    FAILURE_THRESHOLD = 10
    RECOVERY_TIMEOUT = 30  # seconds
    STATE_OPEN = "OPEN"
    STATE_HALF_OPEN = "HALF_OPEN"
    STATE_CLOSED = "CLOSED"
```

### 4. Rate Limiting

Per-service configurable limits:

| Service | Limit |
|---------|-------|
| Auth | 2000 req/min |
| Scan | 1000 req/min |
| AI | 500 req/min |
| Recon | 500 req/min |

### 5. Security

#### JWT Validation

```python
# core/middleware/jwt_validation.py
async def validate_jwt(token: str) -> JWTPayload:
    # Validate signature, expiration, claims
    # Extract user_id, roles, permissions
```

#### RBAC Enforcement

```python
# core/middleware/rbac.py
async def enforce_rbac(user: User, resource: str, action: str) -> bool:
    # Check user's roles against required permissions
    # 8 roles × 20+ permissions matrix
```

## API Endpoints

### REST Endpoints

```
POST   /api/v1/auth/login          # User login
POST   /api/v1/auth/register       # User registration
POST   /api/v1/auth/refresh        # Refresh token

GET    /api/v1/scans              # List scans
POST   /api/v1/scans              # Create scan
GET    /api/v1/scans/{id}         # Get scan details

GET    /api/v1/findings           # List findings
POST   /api/v1/findings/mask      # Mask sensitive data

WS     /api/v1/collab/room/{id}   # WebSocket collaboration
```

### GraphQL Schema

```graphql
type Query {
    scans(limit: Int, offset: Int): [Scan]
    findings(scanId: ID, severity: FindingSeverity): [Finding]
    me: User
}

type Mutation {
    createScan(input: ScanInput!): Scan
    createFinding(input: FindingInput!): Finding
}
```

## Middleware Chain

```
Request → CORS → JWT Validation → RBAC → Rate Limit → Circuit Breaker → Route
```

## Configuration

```yaml
# cosmicsec-core/config/gateway.yaml
server:
  host: "0.0.0.0"
  port: 8000

services:
  auth: "http://auth:8001"
  scan: "http://scan:8002"
  ai: "http://ai:8003"

circuit_breaker:
  failure_threshold: 10
  recovery_timeout: 30

rate_limiting:
  enabled: true
  default: 1000
  per_service:
    auth: 2000
    scan: 1000
```

## Health Check

```
GET /health
```

Returns:

```json
{
  "status": "healthy",
  "services": {
    "auth": "up",
    "scan": "up",
    "ai": "up"
  },
  "timestamp": "2026-05-12T00:00:00Z"
}
```

## Plugin System

Extensible middleware plugins:

```python
class GatewayPlugin:
    name: str
    priority: int
    async def on_request(self, request: Request) -> Optional[Response]: ...
    async def on_response(self, response: Response) -> Optional[Response]: ...

# Built-in plugins
CorsPlugin          # CORS headers
LoggingPlugin       # Request/response logging
RateLimitPlugin     # Per-client rate limiting
MetricsPlugin       # Prometheus metrics
```

## Related Documentation

- [Service Discovery](./service-discovery.md)
- [RBAC & SSO](../security/rbac-sso.md)
- [Circuit Breaker Patterns](../services/resilience.md)