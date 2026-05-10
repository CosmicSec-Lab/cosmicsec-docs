# 🌐 DeepIntel PRO — Premium Deep Web Intelligence#

## Overview#

**DeepIntel PRO** is the world's most advanced dark web monitoring platform with 17+ dark web networks, AI-powered analysis, and premium threat intelligence export capabilities.

**Location:** `cosmicsec-deepintel/`  
**Port:** 8032  
**Framework:** FastAPI + Scrapy + Tor/I2P clients + NLP#

## Premium Features#

### 1. 17+ Dark Web Networks (World's Most Comprehensive)#
| Network | Status | Protocol | Use Case |
|---------|--------|----------|----------|
| **Tor** | ✅ Operational | .onion | Traditional dark web |
| **I2P** | ✅ Operational | .i2p | Invisible Internet Project |
| **IPFS** | ✅ Operational | P2P | Decentralized file storage |
| **ZeroNet** | ✅ Operational | .bit | Decentralized websites |
| **Freenet** | ✅ Operational | FCP | Anonymous publishing |
| **GNUnet** | ✅ Operational | gnunet | P2P framework |
| **Lokinet** | ✅ Operational | .loki | Mixnet protocol |
| **Yggdrasil** | ✅ Operational | ygg | Mesh networking |
| **RetroShare** | ✅ Operational | P2P | Friend-to-friend sharing |
| **Cjdns** | ✅ Operational | .kcpsp | Encrypted IPv6 mesh |
| **Hyperboria** | ✅ Operational | cjdns | Mesh network |
| **AnoNet** | ✅ Operational | .anon | Anonymous overlay |
| **Namecoin** | ✅ Operational | .bit | Decentralized DNS |
| **Nym** | ✅ Operational | Mixnet | Privacy mixnet |
| **Session** | ✅ Operational | Signal | Encrypted messaging |
| **BitMessage** | ✅ Operational | P2P | Anonymous comms |
| **Arweave** | ✅ Operational | permaweb | Permanent storage |
| **ENS** | ✅ Operational | .eth | Ethereum Name Service |
| **Privacy Chains** | ✅ Operational | - | Monero, Zcash tracking |

### 2. AI-Powered Intelligence (Premium)#
- **Entity Extraction** — IPs, domains, CVEs, emails, BTC addresses#
- **NLP Analysis** — Sentiment, intent, categorization#
- **Threat Scoring** — AI calculates threat level (0-1)#
- **Pattern Recognition** — Identify attack campaigns#
- **Predictive Intelligence** — Forecast emerging threats#
- **Automated Pivoting** — Automatically find related intel#

### 3. Advanced Crawling#
- **Recursive Crawler** — BFS traversal of dark web#
- **Proxy Rotator** — Weighted rotation across 50+ proxies#
- **Stealth Mode** — Rate limiting, user-agent rotation, cookie isolation#
- **CAPTCHA Solving** — AI-powered (95%+ success)#
- **JavaScript Rendering** — Headless browser support#
- **Breadth-First Search** — BFS up to 3 levels deep#

### 4. STIX 2.1 Export (Premium)#
- **Industry Standard** — Share intel with partners#
- **TAXII Support** — Connect to TIPs (Threat Intel Platforms)#
- **OpenIOC** — Legacy support#
- **Custom Formats** — MISP, OTX, custom JSON#
- **Automated Sharing** — Push to 5+ platforms automatically#

### 5. Alerting & Notifications#
- **Keyword Monitoring** — "Acme Corp", "ransomware", etc.#
- **Threshold-Based** — Alert when >5 mentions in 1 hour#
- **Real-Time** — WebSocket push notifications#
- **Multi-Channel** — Email, Slack, webhook, Telegram#
- **Threat Score Ranking** — Only alert on high-value intel#

## API Endpoints#

### Search Intelligence#
```http#
POST /api/v1/intel/search#
```

**Request:**
```json#
{
  "query": "ransomware as a service",
  "networks": ["tor", "i2p"],
  "severity": "high",
  "date_range": {
    "start": "2026-01-01",
    "end": "2026-05-05"
  },
  "limit": 100,
  "ai_analysis": true
}
```

**Response:**
```json#
{
  "results": [
    {
      "intel_id": "intel_001",
      "source": "tor",
      "url": "http://abcdefghijklm.onion/post/123",
      "title": "New Ransomware Variant Targeting Healthcare",
      "content": "A new ransomware variant called 'DarkVault'...",
      "entities": {
        "ips": ["192.168.1.100", "10.0.0.1"],
        "domains": ["evil.com", "c2server.onion"],
        "cves": ["CVE-2024-12345"],
        "emails": ["contact@darkvault.onion"],
        "btc_addresses": ["1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa"]
      },
      "severity": "critical",
      "threat_score": 0.95,
      "timestamp": "2026-05-05T10:00:00Z",
      "ai_analysis": {
        "summary": "Highly sophisticated ransomware operation targeting healthcare sector...",
        "campaign_identified": "DarkVault",
        "ttp": ["T1486", "T1041", "T1490"],
        "predicted_impact": "Severe data encryption, $2M+ ransom demands",
        "confidence": 0.92
      }
    }
  ],
  "total": 1500,
  "query_time_ms": 250
}
```

### Get Alert Rules#
```http#
GET /api/v1/intel/alerts#
```

**Response:**
```json#
{
  "alerts": [
    {
      "alert_id": "alert_001",
      "name": "Ransomware Alerts",
      "keywords": ["ransomware", "encrypt", "payment"],
      "networks": ["tor", "i2p"],
      "severity_threshold": "high",
      "notify_channels": ["email", "slack"],
      "enabled": true,
      "triggered_count": 15
    }
  ],
  "total": 5
}
```

### Create Alert Rule#
```http#
POST /api/v1/intel/alerts#
```

**Request:**
```json#
{
  "name": "Data Breach Alerts",
  "keywords": ["breach", "leak", "database", "dump"],
  "networks": ["tor", "i2p", "ipfs"],
  "severity_threshold": "critical",
  "notify_channels": ["email", "slack", "webhook"],
  "enabled": true
}
```

### Export to STIX#
```http#
POST /api/v1/intel/export/stix#
```

**Request:**
```json#
{
  "intel_ids": ["intel_001", "intel_002"],
  "format": "json"  // json or xml
}
```

**Response:** STIX 2.1 formatted threat intelligence ready for sharing.#

### Get Network Status#
```http#
GET /api/v1/intel/networks/status#
```

**Response:**
```json#
{
  "networks": [
    {
      "name": "tor",
      "status": "operational",
      "last_crawl": "2026-05-05T22:00:00Z",
      "proxies_available": 50,
      "content_indexed": 150000,
      "uptime_percentage": 99.9
    },
    {
      "name": "i2p",
      "status": "operational",
      "last_crawl": "2026-05-05T21:30:00Z",
      "proxies_available": 30,
      "content_indexed": 75000,
      "uptime_percentage": 99.5
    }
  ]
}
```

## AI-Powered Entity Extraction#

### Extracted Entities
```python#
# AI extracts and enriches entities#
{
  "entities": {
    "ips": {
      "values": ["192.168.1.100", "10.0.0.1"],
      "enrichment": [
        {
          "ip": "192.168.1.100",
          "geoip": {"country": "US", "city": "Norwalk"},
          "asn": "AS13335 Cloudflare",
          "reputation": "clean"
        }
      ]
    },
    "cves": {
      "values": ["CVE-2024-12345"],
      "enrichment": [
        {
          "cve": "CVE-2024-12345",
          "cvss_score": 9.8,
          "description": "Critical RCE vulnerability...",
          "exploit_available": true
        }
      ]
    },
    "btc_addresses": {
      "values": ["1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa"],
      "enrichment": [
        {
          "address": "1A1zP1...",
          "balance_btc": 15.5,
          "tx_count": 150,
          "reputation": "ransomware_related"
        }
      ]
    }
  }
}
```

## Proxy Rotator (Premium)#

### Weighted Rotation Algorithm
```python#
# core/proxy_rotator.py#
class WeightedProxyRotator:#
    def __init__(self):#
        self.proxies = [#
            {"url": "socks5://tor1:9050", "weight": 10, "success_rate": 0.98},#
            {"url": "socks5://tor2:9050", "weight": 8, "success_rate": 0.95},#
            {"url": "socks5://i2p:4447", "weight": 5, "success_rate": 0.92},#
        ]#
        self.current = 0#
        
    def get_proxy(self, network: str = None) -> str:#
        """Weighted random selection with success rate factor"""#
        candidates = [p for p in self.proxies if not network or network in p['url']]#
        
        # Combine weight + success rate#
        weights = [p['weight'] * p['success_rate'] for p in candidates]#
        total = sum(weights)#
        
        r = random.uniform(0, total)#
        cumulative = 0#
        for proxy, weight in zip(candidates, weights):#
            cumulative += weight#
            if r <= cumulative:#
                return proxy['url']#
        
        return candidates[0]['url']#
        
    def report_success(self, proxy_url: str, success: bool):#
        """Adjust weights based on success/failure"""#
        for proxy in self.proxies:#
            if proxy['url'] == proxy_url:#
                # Exponential moving average#
                alpha = 0.1#
                new_rate = alpha * (1.0 if success else 0.0) + (1 - alpha) * proxy['success_rate']#
                proxy['success_rate'] = new_rate#
                break#
```

## CLI Usage#

```bash#
# Search dark web#
cosmicsec deepintel search \#
  --query "ransomware as a service" \#
  --networks tor,i2p \#
  --severity high \#
  --limit 100;#

# Get alert rules#
cosmicsec deepintel alerts;#

# Create alert rule#
cosmicsec deepintel alert create \#
  --name "Ransomware Alerts" \#
  --keywords "ransomware,encrypt" \#
  --networks tor,i2p \#
  --notify email,slack;#

# Export to STIX#
cosmicsec deepintel export stix \#
  --intel-ids intel_001,intel_002 \#
  --format json \#
  --output threat_intel.json;#

# Check network status#
cosmicsec deepintel networks status;#

# Manually trigger crawl#
cosmicsec deepintel crawl \#
  --network tor \#
  --max-depth 3 \#
  --max-pages 1000;#

# Test proxy rotation#
cosmicsec deepintel proxy test \#
  --network tor \#
  --iterations 100#
```

## Python SDK Usage#

```python#
from cosmicsec import CosmicSecClient#

client = CosmicSecClient(api_key="cs_live_...")#

# Search intelligence#
results = client.deepintel.search(#
    query="ransomware as a service",#
    networks=["tor", "i2p"],#
    severity="high",#
    limit=100,#
    ai_analysis=True#
)#print(f"Found {len(results.results)} intelligence items")#
print(f"Query time: {results.query_time_ms}ms")#

# Print high-threat items#
for item in results.results:#
    if item.threat_score > 0.8:#
        print(f"[CRITICAL] {item.title}")#
        print(f"  Threat Score: {item.threat_score}")#
        print(f"  Source: {item.source}")#
        if item.ai_analysis:#
            print(f"  AI Summary: {item.ai_analysis['summary']}")#

# Create alert rule#
alert = client.deepintel.create_alert(#
    name="Data Breach Alerts",#
    keywords=["breach", "leak", "database"],#
    networks=["tor", "i2p"],#
    severity_threshold="critical",#
    notify_channels=["email", "slack"]#
)#print(f"Alert rule created: {alert.alert_id}")#

# Export to STIX#
stix_data = client.deepintel.export_stix(#
    intel_ids=["intel_001", "intel_002"],#
    format="json"#
)#with open('threat_intel.stix.json', 'w') as f:#
    f.write(stix_data)#
print("Exported to threat_intel.stix.json")#

# Get network status#
status = client.deepintel.get_network_status()#
print("\nNetwork Status:")#
for net in status.networks:#
    status = "✅" if net.status == 'operational' else "❌"#
    print(f"{status} {net.name}: {net.content_indexed} indexed")#
```

## Configuration#

### Environment Variables
```bash#
# Service#
DEEPINTEL_PORT=8032#
DEEPINTEL_HOST=0.0.0.0#

# Networks#
TOR_ENABLED=true#
TOR_SOCKS_PROXY=socks5://tor:9050#
I2P_ENABLED=true#
I2P_SOCKS_PROXY=socks5://i2p:4447#
IPFS_ENABLED=true#
ZERONET_ENABLED=true#
# ... (all 17+ networks)#

# Crawling#
MAX_CRAWL_DEPTH=3#
MAX_PAGES_PER_CRAWL=1000#
CRAWL_DELAY=1.0#
RESPECT_ROBOTS_TXT=true#
STEALTH_MODE=true#

# Proxy Rotator#
PROXY_ROTATOR_ENABLED=true#
MIN_PROXY_SUCCESS_RATE=0.8#
AUTO_ADJUST_WEIGHTS=true#

# AI Analysis#
AI_SERVICE_URL=http://ai-service:8003#
AI_ENTITY_EXTRACTION_ENABLED=true#
AI_THREAT_SCORING_ENABLED=true#
AI_PREDICTIVE_ENABLED=true#

# Export#
STIX_EXPORT_ENABLED=true#
TAXII_ENABLED=true#
MISP_INTEGRATION_ENABLED=true#
OTX_INTEGRATION_ENABLED=true#

# Storage#
ELASTICSEARCH_URL=http://elasticsearch:9200#
POSTGRES_URL=postgresql://user:pass@postgres:5432/deepintel#
MINIO_URL=http://minio:9000#

# Notifications#
SMTP_HOST=smtp.gmail.com#
SLACK_WEBHOOK_URL=https://hooks.slack.com/...#
WEBHOOK_URL=https://company.com/webhook#
```

## Premium Monitoring#

### Grafana Dashboard#
Pre-built dashboard shows:#
- **Dark Web Coverage** — 17+ networks status#
- **Intel by Severity** — Critical/high/medium/low breakdown#
- **Crawl Volume** — Pages crawled over time#
- **Proxy Health** — Success rates by proxy#
- **AI Threat Scores** — Distribution histogram#
- **Alert Triggers** — Timeline of alerts#

### Prometheus Metrics#
- `deepintel_crawl_total` — Total crawls by network#
- `deepintel_content_indexed_total` — Content by network#
- `deepintel_alerts_triggered_total` — Alerts by severity#
- `deepintel_proxy_health` — Proxy availability (0=down, 1=up)#
- `deepintel_ai_threat_score` — Threat score gauge#
- `deepintel_stix_export_total` — STIX exports#

## Troubleshooting#

### Tor Not Connecting#
```bash#
# Check Tor service#
docker exec -it tor torify curl -s https://check.torproject.org#

# Check SOCKS proxy#
curl --socks5 tor:9050 https://check.torproject.org#

# View Tor logs#
docker logs tor#

# Restart Tor#
docker restart tor#
```

### I2P Not Working#
```bash#
# Check I2P router#
docker exec -it i2p curl http://localhost:7657#

# Test SOCKS#
curl --socks5 i2p:4447 http://example.i2p#

# View I2P logs#
docker logs i2p#
```

### No Content Indexed#
```bash#
# Check network status#
curl http://localhost:8032/api/v1/intel/networks/status#

# Check Elasticsearch#
curl http://elasticsearch:9200/_cat/indices#

# Check crawler logs#
docker logs deepintel | grep "crawler"#
```

## Next Steps#

- [Egress Service](./egress-service.md)#
- [Ingest Service](./ingest-service.md)#
- [Threat Intel Guide](../guides/threat-intel.md)#
- [STIX Export Guide](../guides/stix-export.md)#
- [Dark Web Monitoring Best Practices](../guides/dark-web-monitoring.md)#
