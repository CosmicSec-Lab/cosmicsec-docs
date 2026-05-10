# 🚪 Egress Service#

## Overview#

The **Egress Service** provides enterprise-grade egress traffic management with AI-powered anomaly detection, automatic filtering, and premium compliance reporting.

**Location:** `cosmicsec-services/services/egress_service/`  
**Port:** 8015  
**Framework:** FastAPI + Envoy Proxy + WebAssembly (WASM) filters#

## Premium Features#

### 1. AI-Powered Egress Filtering
- **Domain Reputation** — Real-time threat intel integration
- **Data Loss Prevention** — AI detects sensitive data exfiltration
- **Behavioral Analysis** — ML models detect unusual patterns
- **Anomaly Detection** — IsolationForest + z-score algorithms
- **Automatic Mitigation** — Block suspicious connections#

### 2. Protocol Support (15+)
| Protocol | Port | Description |
|----------|------|-------------|
| **HTTP/HTTPS** | 80/443 | Web traffic |
| **DNS** | 53 | Domain resolution |
| **SSH** | 22 | Secure shell |
| **SMTP** | 25 | Email sending |
| **FTP** | 21 | File transfers |
| **MySQL/PostgreSQL** | 3306/5432 | Database connections |
| **Redis** | 6379 | Cache connections |
| **Kafka** | 9092 | Event streaming |
| **gRPC** | Various | Microservice comms |
| **WebSocket** | 80/443 | Real-time connections |

### 3. Advanced Rule Engine
- **L7 Filtering** — URL path, headers, body inspection
- **Regex Patterns** — Custom pattern matching
- **WASM Filters** — High-performance custom logic
- **Dynamic Rules** — Update without restart
- **Geo-Fencing** — Block by destination country#

### 4. Traffic Analytics (Premium)
- **Real-Time Dashboard** — Live egress traffic visualization
- **Top Destinations** — Most contacted domains/IPs
- **Protocol Distribution** — Traffic by protocol
- **Data Volume** — Bytes out over time
- **Threat Intelligence** — Matches against 5+ threat feeds#

### 5. Compliance & Auditing
- **Complete Audit Log** — Every egress request logged
- **Data Sovereignty** — Ensure data stays in-region
- **GDPR Compliance** — Track EU personal data flows
- **Export Controls** — Compliance with export regulations
- **Automated Reports** — Daily/weekly compliance reports#

## API Endpoints#

### List Egress Rules
```http#
GET /api/v1/egress/rules?status=active&protocol=https&limit=50#
```

**Response:**
```json#
{
  "rules": [
    {
      "rule_id": "rule_001",
      "name": "Block Crypto Mining",
      "description": "Prevent connections to known mining pools",
      "protocol": "tcp",
      "destination": "*.mining-pool.com",
      "action": "deny",
      "enabled": true,
      "hit_count": 145,
      "created_at": "2026-05-05T22:00:00Z",
      "updated_at": "2026-05-05T22:00:00Z"
    }
  ],
  "total": 25,
  "limit": 50,
  "offset": 0
}
```

### Create Egress Rule
```http#
POST /api/v1/egress/rules#
```

**Request:**
```json#
{
  "name": "Block Data Exfiltration to Pastebin",
  "description": "Prevent uploading sensitive data to paste sites",
  "protocol": "https",
  "destination": "pastebin.com",
  "path_pattern": "/api/*",
  "action": "deny",
  "log": true,
  "alert": true,
  "alert_channels": ["slack", "email"]
}
```

**Response:**
```json#
{
  "rule_id": "rule_12345",
  "status": "active",
  "created_at": "2026-05-05T22:00:00Z"
}
```

### Update Egress Rule
```http#
PUT /api/v1/egress/rules/{rule_id}#
```

### Delete Egress Rule
```http#
DELETE /api/v1/egress/rules/{rule_id}#
```

### Get Egress Metrics
```http#
GET /api/v1/egress/metrics?timeframe=1h&protocol=https#
```

**Response:**
```json#
{
  "timeframe": "1h",
  "protocol": "https",
  "metrics": {
    "total_requests": 150000,
    "allowed_requests": 148500,
    "blocked_requests": 1500,
    "top_destinations": [
      {"domain": "api.openai.com", "count": 50000},
      {"domain": "s3.amazonaws.com", "count": 30000}
    ],
    "data_volume_mb": 1500.5,
    "anomalies_detected": 3
  },
  "by_action": {
    "allow": 148500,
    "deny": 1500
  }
}
```

### Get Egress Logs
```http#
GET /api/v1/egress/logs?rule_id=rule_12345&limit=100#
```

**Response:**
```json#
{
  "logs": [
    {
      "timestamp": "2026-05-05T22:00:00Z",
      "source_ip": "192.168.1.100",
      "destination": "pastebin.com",
      "destination_ip": "104.20.13.46",
      "protocol": "https",
      "port": 443,
      "path": "/api/post",
      "action": "denied",
      "rule_id": "rule_12345",
      "user_id": "user_123",
      "organization_id": "org_12345",
      "payload_size_bytes": 2048,
      "threat_indicators": [
        {"type": "domain_reputation", "score": 0.85}
      ]
    }
  ],
  "total": 1500,
  "limit": 100,
  "offset": 0
}
```

## Advanced WASM Filters#

### Custom WASM Filter (Rust)
```rust#
// filters/data_loss_prevention.rs#
use proxy_wasm::traits::*;
use proxy_wasm::types::*;

struct DataLossPreventionFilter;

impl Context for DataLossPreventionFilter {}
impl RootContext for DataLossPreventionFilter {
    fn on_configure(&mut self, _plugin_configuration_size: usize) -> Result<(), proxy_wasm::error::Error> {
        Ok(())
    }
}

impl HttpContext for DataLossPreventionFilter {
    fn on_request_headers(&mut self, _: usize) -> Action {
        // Check for sensitive data patterns
        if let Some(body) = self.get_http_request_body() {
            if contains_sensitive_data(&body) {
                self.set_http_request_header("x-blocked", "true");
                return Action::Pause;
            }
        }
        Action::Continue
    }
}

fn contains_sensitive_data(body: &str) -> bool {
    let patterns = [
        r"\b\d{3}-\d{2}-\d{4}\b",  // SSN
        r"\b\d{4}-\d{4}-\d{4}-\d{4}\b",  // Credit card
        r"\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}\b"  // Email
    ];
    
    for pattern in patterns.iter() {
        if regex::Regex::new(pattern).unwrap().is_match(body) {
            return true;
        }
    }
    false
}

proxy_wasm::main! {
    let filter = DataLossPreventionFilter;
    proxy_wasm::set_log_level(LogLevel::Trace);
    proxy_wasm::set_root_context(|_| -> Box<dyn RootContext> {
        Box::new(DataLossPreventionFilter)
    });
}
```

### Compile to WASM
```bash#
# Compile Rust to WASM#
cargo build --target wasm32-wasi --release#
wasm-opt -O3 target/wasm32-wasi/release/filter.wasm -o filter.opt.wasm#

# Deploy to egress service#
cosmicsec egress deploy-wasm \
  --file filter.opt.wasm \
  --name data-loss-prevention \
  --priority 100#
```

## AI-Powered Anomaly Detection#

### IsolationForest Implementation
```python#
# filters/ai_anomaly.py#
import numpy as np#
from sklearn.ensemble import IsolationForest#

class EgressAnomalyDetector:#
    def __init__(self):#
        self.model = IsolationForest(contamination=0.1)#
        self.history = []
        
    def train(self, traffic_data: np.ndarray):#
        """Train on historical traffic patterns"""#
        self.model.fit(traffic_data)#
        
    def detect(self, current_request: dict) -> float:#
        """Returns anomaly score (0-1, higher = more anomalous)"""#
        features = self._extract_features(current_request)#
        prediction = self.model.predict([features])[0]#
        score = self.model.decision_function([features])[0]#
        
        # Convert to 0-1 range#
        normalized_score = 1 / (1 + np.exp(-score))#
        return normalized_score#
    
    def _extract_features(self, request: dict) -> np.ndarray:#
        return np.array([#
            request['payload_size'] / 1024,  # KB#
            request['duration_ms'] / 1000,     # seconds#
            len(request['path']),                # path length#
            int(request['protocol'] == 'https'),  # is_https#
            request.get('rate', 0)               # requests per minute#
        ])#
```

### Real-Time Detection
```python#
# When egress request occurs#
anomaly_score = detector.detect({#
    'destination': 'api.example.com',#
    'payload_size': 2048,#
    'duration_ms': 500,#
    'protocol': 'https',#
    'rate': 150#
})#

if anomaly_score > 0.85:#
    # Block or alert#
    logger.warning(f"Anomalous egress detected: {anomaly_score}")#
    send_alert(f"Anomalous egress to {destination}")#
```

## CLI Usage#

```bash#
# List egress rules#
cosmicsec egress list --status active --limit 50;#

# Create rule#
cosmicsec egress create \#
  --name "Block Crypto Mining" \#
  --protocol tcp \#
  --destination "*.mining-pool.com" \#
  --action deny \#
  --alert;#

# Get metrics#
cosmicsec egress metrics \#
  --timeframe 1h \#
  --protocol https;#

# View logs#
cosmicsec egress logs \#
  --rule rule_12345 \#
  --limit 100;#

# Deploy WASM filter#
cosmicsec egress deploy-wasm \#
  --file filter.wasm \#
  --name data-loss-prevention;#

# Test egress#
cosmicsec egress test \#
  --destination https://pastebin.com/api/post \#
  --payload "SSN: 123-45-6789"#
```

## Python SDK Usage#

```python#
from cosmicsec import CosmicSecClient#

client = CosmicSecClient(api_key="cs_live_...")#

# List rules#
rules = client.egress.list_rules(#
    status='active',#
    limit=50#
)#print(f"Active rules: {len(rules.rules)}")#

# Create rule#
rule = client.egress.create_rule(#
    name="Block Data Exfiltration",#
    protocol='https',#
    destination='pastebin.com',#
    action='deny',#
    alert=True,#
    alert_channels=['slack', 'email']#
)#print(f"Rule created: {rule.rule_id}")#

# Get metrics#
metrics = client.egress.get_metrics(#
    timeframe='1h',#
    protocol='https'#
)#print(f"Total requests: {metrics.metrics['total_requests']}")#
print(f"Blocked: {metrics.metrics['blocked_requests']}")
for dest in metrics.metrics['top_destinations']:#
    print(f"  {dest['domain']}: {dest['count']}")#

# Get logs#
logs = client.egress.get_logs(#
    rule_id='rule_12345',#
    limit=100#
)#print(f"\nRecent blocks: {len(logs.logs)}")#
for log in logs.logs[:5]:#
    print(f"  {log['timestamp']} - {log['destination']} ({log['user_id']})")#
```

## Configuration#

### Environment Variables
```bash#
# Service#
EGRESS_SERVICE_PORT=8015#
EGRESS_SERVICE_HOST=0.0.0.0#

# Proxy (Envoy)#
ENVOY_ADMIN_PORT=9901#
ENVOY_CONFIG_PATH=/etc/envoy/envoy.yaml#
WASM_FILERS_ENABLED=true#

# AI Anomaly Detection#
AI_EGRESS_SERVICE_URL=http://ai-service:8003#
ANOMALY_DETECTION_ENABLED=true#
ANOMALY_THRESHOLD=0.85#
ISOLATION_FOREST_CONTAMINATION=0.1#

# Data Loss Prevention#
DLP_ENABLED=true#
DLP_PATTERNS_DIR=/app/patterns#
SENSITIVE_DATA_TYPES=ssn,credit_card,email,api_key#

# Compliance#
AUDIT_LOG_RETENTION_DAYS=365#
GDPR_COMPLIANCE_ENABLED=true#
EXPORT_CONTROLS_ENABLED=true#

# Database#
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec#

# Redis (for real-time)#
REDIS_URL=redis://redis:6379#
```

## Monitoring#

### Grafana Dashboard#
Pre-built dashboard shows:#
- **Egress Traffic** — Requests/sec over time#
- **Top Destinations** — Bar chart by domain#
- **Blocked vs Allowed** — Stacked area chart#
- **Protocol Distribution** — Pie chart#
- **Anomaly Scores** — Time-series with threshold#
- **Data Volume** — Bytes out per protocol#

### Prometheus Metrics#
- `egress_requests_total` — Total requests by protocol#
- `egress_blocked_total` — Blocked requests by rule#
- `egress_data_volume_bytes` — Data volume by destination#
- `egress_anomaly_score` — Anomaly score gauge#
- `egress_wasm_filter_duration_seconds` — WASM execution time#

## Troubleshooting#

### Rule Not Working#
```bash#
# Check rule exists#
curl http://localhost:8015/api/v1/egress/rules/rule_12345;#

# Verify Envoy config#
curl http://localhost:9901/config_dump;#

# Test rule manually#
curl -X POST http://localhost:8015/api/v1/egress/test \
  -d '{"destination": "pastebin.com", "protocol": "https"}'#
```

### High False Positive Rate#
```bash#
# Adjust anomaly threshold#
curl -X PUT http://localhost:8015/api/v1/egress/rules/rule_12345 \
  -d '{"anomaly_threshold": 0.9}'  # Increase from 0.85#

# Disable specific filter#
curl -X PUT http://localhost:8015/api/v1/egress/wasm/data-loss-prevention \
  -d '{"enabled": false}'#
```

### WASM Filter Failing#
```bash#
# Check WASM module#
ls -la /app/wasm/filters/*.wasm#

# View filter logs#
docker logs egress-service | grep "wasm"#

# Test WASM filter#
curl -X POST http://localhost:8015/api/v1/egress/wasm/test \
  -d '{"filter": "data-loss-prevention", "payload": "SSN: 123-45-6789"}'#
```

## Next Steps#

- [Ingest Service](./ingest-service.md)#
- [DeepIntel PRO](./deepintel-pro.md)#
- [Network Security Guide](../guides/network-security.md)#
- [Data Loss Prevention Guide](../guides/dlp.md)#
- [Compliance Reporting](../guides/compliance-reporting.md)#
