# 🌐 DeepIntel PRO — Dark Web Intelligence

## Overview

**DeepIntel PRO** is the world's most comprehensive dark web monitoring platform. It monitors 17+ dark web networks, crawls hidden services, and provides actionable threat intelligence.

**Location:** `cosmicsec-deepintel/`  
**Port:** 8032  
**Framework:** FastAPI + Scrapy + Tor/I2P clients

## Features

### 1. 17+ Dark Web Networks Monitored
- **Tor** — .onion hidden services
- **I2P** — Invisible Internet Project
- **IPFS** — InterPlanetary File System
- **ZeroNet** — Decentralized websites
- **Freenet** — Anonymous publishing
- **GNUnet** — P2P framework
- **Lokinet** — Mixnet protocol
- **Yggdrasil** — Mesh networking
- **RetroShare** — Friend-to-friend sharing
- **Cjdns** — Encrypted IPv6 mesh
- **Hyperboria** — Mesh network
- **AnoNet** — Anonymous overlay
- **Namecoin** — Decentralized DNS
- **Nym** — Privacy mixnet
- **Session** — Encrypted messaging
- **BitMessage** — P2P communications
- **Arweave** — Permanent storage
- **ENS** — Ethereum Name Service
- **Privacy Chains** — Monero, Zcash tracking

### 2. Advanced Crawling
- **Recursive Crawler** — BFS traversal of dark web
- **Proxy Rotator** — Weighted rotation across 50+ proxies
- **Stealth Mode** — Rate limiting, user-agent rotation
- **CAPTCHA Handling** — Automatic detection + solving
- **JavaScript Rendering** — Headless browser support

### 3. Data Processing
- **STIX Export** — Industry-standard threat intel format
- **Entity Extraction** — IPs, domains, CVEs, emails
- **NLP Analysis** — Sentiment, intent, categorization
- **Deduplication** — Avoid processing same content twice
- **Real-Time Indexing** — Elasticsearch integration

### 4. Alerting & Notifications
- Keywords monitoring
- Threshold-based alerts
- Email/Slack/Webhook notifications
- Threat score ranking

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                   DeepIntel PRO (Port 8032)               │
├─────────────────────────────────────────────────────────────┤
│  API Layer (FastAPI)                                       │
│  • /intel/search, /intel/alerts, /intel/export             │
├─────────────────────────────────────────────────────────────┤
│  Crawler Engine (Scrapy)                                   │
│  • Tor Crawler (.onion)                                    │
│  • I2P Crawler (.i2p)                                      │
│  • IPFS Crawler                                            │
│  • ZeroNet Crawler                                         │
│  • ... 17+ network crawlers                                │
├─────────────────────────────────────────────────────────────┤
│  Proxy Layer                                               │
│  • Tor SOCKS5 (9050)                                       │
│  • I2P SOCKS (4447)                                        │
│  • Proxy Rotator (weighted)                                │
├─────────────────────────────────────────────────────────────┤
│  Processing Layer                                          │
│  • Entity Extraction (Regex + NLP)                         │
│  • STIX Formatter                                          │
│  • Deduplication (SimHash)                                 │
├─────────────────────────────────────────────────────────────┤
│  Storage Layer                                             │
│  • PostgreSQL (metadata)                                    │
│  • Elasticsearch (full-text search)                        │
│  • MinIO (raw content, screenshots)                       │
└─────────────────────────────────────────────────────────────┘
```

## API Endpoints

### Search Intelligence
```http
POST /api/v1/intel/search
```

**Request:**
```json
{
  "query": "ransomware",
  "networks": ["tor", "i2p"],
  "severity": "high",
  "date_range": {
    "start": "2026-01-01",
    "end": "2026-05-05"
  },
  "limit": 100
}
```

**Response:**
```json
{
  "results": [
    {
      "id": "intel_001",
      "source": "tor",
      "url": "http://abcdefghijklm.onion/post/123",
      "title": "New Ransomware Variant Targeting Healthcare",
      "content": "A new ransomware variant...",
      "entities": {
        "ips": ["192.168.1.100"],
        "domains": ["evil.com"],
        "cves": ["CVE-2024-12345"]
      },
      "severity": "critical",
      "timestamp": "2026-05-05T10:00:00Z",
      "threat_score": 0.95
    }
  ],
  "total": 1500,
  "query_time_ms": 250
}
```

### Get Alert Rules
```http
GET /api/v1/intel/alerts
```

### Create Alert Rule
```http
POST /api/v1/intel/alerts
```

**Request:**
```json
{
  "name": "Ransomware Alerts",
  "keywords": ["ransomware", "encrypt", "payment"],
  "networks": ["tor", "i2p"],
  "severity_threshold": "high",
  "notify_channels": ["email", "slack"],
  "enabled": true
}
```

### Export to STIX
```http
POST /api/v1/intel/export/stix
```

**Request:**
```json
{
  "intel_ids": ["intel_001", "intel_002"],
  "format": "json"  // json or xml
}
```

**Response:** STIX 2.1 formatted threat intelligence.

### Get Network Status
```http
GET /api/v1/intel/networks/status
```

**Response:**
```json
{
  "networks": [
    {
      "name": "tor",
      "status": "operational",
      "last_crawl": "2026-05-05T22:00:00Z",
      "proxies_available": 50,
      "content_indexed": 150000
    },
    {
      "name": "i2p",
      "status": "operational",
      "last_crawl": "2026-05-05T21:30:00Z",
      "proxies_available": 30,
      "content_indexed": 75000
    }
  ]
}
```

## Crawler Configuration

### Tor Crawler
```python
# deepintel/crawlers/tor_crawler.py
class TorCrawler:
    def __init__(self):
        self.socks_proxy = "socks5://tor:9050"
        self.user_agents = [
            "Mozilla/5.0 (Windows NT 10.0; Win64; x64)...",
            "Mozilla/5.0 (Macintosh; Intel Mac OS X...)..."
        ]
    
    async def crawl(self, url: str):
        async with aiohttp.ClientSession(
            connector=ProxyConnector(socks_proxy=self.socks_proxy)
        ) as session:
            async with session.get(url) as response:
                return await response.text()
```

### Proxy Rotator
```python
# deepintel/core/proxy_rotator.py
class ProxyRotator:
    def __init__(self):
        self.proxies = [
            {"url": "socks5://tor1:9050", "weight": 10},
            {"url": "socks5://tor2:9050", "weight": 8},
            {"url": "socks5://i2p:4447", "weight": 5},
        ]
        self.current = 0
    
    def get_proxy(self):
        # Weighted random selection
        total_weight = sum(p["weight"] for p in self.proxies)
        r = random.uniform(0, total_weight)
        cumulative = 0
        for proxy in self.proxies:
            cumulative += proxy["weight"]
            if r <= cumulative:
                return proxy["url"]
```

### Recursive Crawler (BFS)
```python
# deepintel/crawlers/recursive_crawler.py
class RecursiveCrawler:
    def __init__(self, max_depth=3, max_pages=1000):
        self.max_depth = max_depth
        self.max_pages = max_pages
        self.visited = set()
    
    async def crawl_bfs(self, start_url: str):
        queue = [(start_url, 0)]  # (url, depth)
        
        while queue and len(self.visited) < self.max_pages:
            url, depth = queue.pop(0)
            
            if url in self.visited or depth > self.max_depth:
                continue
            
            self.visited.add(url)
            content = await self.fetch(url)
            
            # Extract links
            links = self.extract_links(content)
            for link in links:
                if link not in self.visited:
                    queue.append((link, depth + 1))
```

## STIX Export

### STIX 2.1 Format
```json
{
  "type": "bundle",
  "id": "bundle--12345",
  "objects": [
    {
      "type": "indicator",
      "id": "indicator--67890",
      "created": "2026-05-05T22:00:00Z",
      "modified": "2026-05-05T22:00:00Z",
      "name": "Ransomware C2 Server",
      "pattern": "[ipv4-addr:value = '192.168.1.100']",
      "pattern_type": "stix",
      "valid_from": "2026-05-05T22:00:00Z"
    },
    {
      "type": "malware",
      "id": "malware--abcde",
      "name": "Ransomware Variant X",
      "is_family": false,
      "malware_types": ["ransomware"]
    }
  ]
}
```

## Entity Extraction

### Extracted Entities
- **IP Addresses** — IPv4 and IPv6
- **Domains** — FQDN extraction
- **URLs** — Full and partial URLs
- **CVEs** — CVE-YYYY-NNNNN format
- **Emails** — Email addresses
- **Bitcoin Addresses** — BTC wallet addresses
- **File Hashes** — MD5, SHA1, SHA256
- **MITRE ATT&CK IDs** — T1234 format

### NLP Analysis
```python
# deepintel/nlp/analyzer.py
import spacy

nlp = spacy.load("en_core_web_sm")

def analyze_content(text: str):
    doc = nlp(text)
    
    # Extract named entities
    entities = [(ent.text, ent.label_) for ent in doc.ents]
    
    # Sentiment analysis
    sentiment = doc._.sentiment
    
    # Intent classification
    intent = classify_intent(text)
    
    return {
        "entities": entities,
        "sentiment": sentiment,
        "intent": intent
    }
```

## Alerting

### Alert Rules
```python
# deepintel/alerts/rule_engine.py
class AlertRule:
    def __init__(self, keywords, severity_threshold, networks):
        self.keywords = keywords
        self.severity_threshold = severity_threshold
        self.networks = networks
    
    def matches(self, intel_item):
        # Check keywords
        for keyword in self.keywords:
            if keyword.lower() in intel_item.content.lower():
                # Check severity
                if self._severity_meets_threshold(intel_item.severity):
                    return True
        return False
```

### Notification Channels
- **Email** — SMTP integration
- **Slack** — Webhook integration
- **Webhook** — Custom HTTP endpoint
- **Telegram** — Bot API
- **Discord** — Webhook integration

## Privacy & Ethics

### Responsible Crawling
- Respect robots.txt when applicable
- Rate limiting to avoid overload
- No personal data indexing without consent
- Data retention policies (auto-delete after X days)

### Legal Compliance
- Follow local laws regarding dark web access
- No illegal content downloading
- Metadata-only collection option
- Audit logging for all accesses

## Configuration

### Environment Variables
```bash
# Service
DEEPINTEL_PORT=8032
DEEPINTEL_HOST=0.0.0.0

# Networks
TOR_ENABLED=true
TOR_SOCKS_PROXY=socks5://tor:9050
I2P_ENABLED=true
I2P_SOCKS_PROXY=socks5://i2p:4447

# Crawling
MAX_CRAWL_DEPTH=3
MAX_PAGES_PER_CRAWL=1000
CRAWL_DELAY=1.0
RESPECT_ROBOTS_TXT=true

# Storage
ELASTICSEARCH_URL=http://elasticsearch:9200
POSTGRES_URL=postgresql://user:pass@postgres:5432/deepintel
MINIO_URL=http://minio:9000

# Notifications
SMTP_HOST=smtp.gmail.com
SLACK_WEBHOOK_URL=https://hooks.slack.com/...

# Security
ENCRYPTION_KEY=your-32-byte-key-here
```

## Monitoring

### Metrics
- `deepintel_crawl_total` — Total crawl requests by network
- `deepintel_content_indexed_total` — Content indexed by network
- `deepintel_alerts_triggered_total` — Alert rules triggered
- `deepintel_proxy_health` — Proxy availability (0=down, 1=up)

### Dashboards
Pre-built Grafana dashboard shows:
- Crawl volume by network
- Content indexed over time
- Alert rule effectiveness
- Proxy health status

## Troubleshooting

### Tor Not Connecting
```bash
# Check Tor service
docker exec -it tor torify curl -s https://check.torproject.org

# Check SOCKS proxy
curl --socks5 tor:9050 https://check.torproject.org

# View Tor logs
docker logs tor
```

### I2P Not Working
```bash
# Check I2P router
docker exec -it i2p curl http://localhost:7657

# Check SOCKS proxy
curl --socks5 i2p:4447 http://example.i2p

# View I2P logs
docker logs i2p
```

### No Content Indexed
```bash
# Check network status
curl http://localhost:8032/api/v1/intel/networks/status

# Check Elasticsearch
curl http://elasticsearch:9200/_cat/indices

# Check crawler logs
docker logs deepintel | grep "crawler"
```

## Next Steps

- [Tor Network Configuration](../deepintel/tor.md)
- [I2P Network Configuration](../deepintel/i2p.md)
- [STIX Export Guide](../deepintel/stix-export.md)
- [Proxy Rotator Setup](../deepintel/proxy-rotator.md)
- [Alert Rules Configuration](../deepintel/alerts.md)
