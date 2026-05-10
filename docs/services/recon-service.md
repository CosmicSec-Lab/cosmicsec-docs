# 🌐 Recon Service

## Overview

The **Recon Service** performs reconnaissance and OSINT (Open Source Intelligence) gathering on target organizations. It automates the discovery of attack surface, subdomains, services, and exposed assets.

**Location:** `cosmicsec-services/services/recon_service/`  
**Port:** 8004  
**Framework:** FastAPI

## Features

### 1. Subdomain Enumeration
- **DNS Enumeration** — A, AAAA, CNAME, MX, TXT, NS records
- **Certificate Transparency** — Query CT logs for subdomains
- **Brute-Force** — Common subdomain dictionary
- **DNS Zone Transfer** — Attempt AXFR (if misconfigured)
- **VirusTotal** — Query for known subdomains

### 2. Port Scanning
- **Service Detection** — Identify services running on ports
- **Banner Grabbing** — Extract service banners
- **TLS/SSL Analysis** — Certificate details, weak ciphers
- **HTTP/HTTPS Probing** — Check for web services

### 3. Technology Stack Identification
- **Web Technologies** — Detect frameworks (React, Angular, etc.)
- **CMS Detection** — WordPress, Drupal, Joomla
- **JavaScript Libraries** — jQuery, Bootstrap versions
- **Server Headers** — Apache, Nginx, IIS versions

### 4. Email & Social Recon
- **Email Harvesting** — Extract emails from website
- **Social Media** — LinkedIn, Twitter, GitHub profiles
- **Employee Enumeration** — LinkedIn employee scraping
- **Breach Data** — Check HaveIBeenPwned for domain

### 5. Cloud & Infrastructure
- **Cloud Provider Detection** — AWS, Azure, GCP, DigitalOcean
- **CDN Identification** — Cloudflare, Akamai, Fastly
- **WAF Detection** — Cloudflare, Imperva, F5
- **IP Geolocation** — Physical location of servers

## API Endpoints

### Start Recon
```http
POST /api/v1/recon
```

**Request:**
```json
{
  "target": "example.com",
  "modules": ["subdomains", "ports", "technologies", "emails", "cloud"],
  "options": {
    "subdomain_wordlist": "common",
    "port_range": "1-65535",
    "timeout": 300,
    "threads": 10
  }
}
```

**Response:**
```json
{
  "recon_id": "recon_12345",
  "status": "in_progress",
  "target": "example.com",
  "created_at": "2026-05-05T22:00:00Z",
  "estimated_duration": 600
}
```

### Get Recon Status
```http
GET /api/v1/recon/{recon_id}
```

**Response:**
```json
{
  "recon_id": "recon_12345",
  "status": "in_progress",
  "progress": 65,
  "target": "example.com",
  "modules_completed": ["subdomains", "ports"],
  "modules_running": ["technologies"],
  "modules_pending": ["emails", "cloud"]
}
```

### Get Recon Results
```http
GET /api/v1/recon/{recon_id}/results
```

**Response:**
```json
{
  "recon_id": "recon_12345",
  "target": "example.com",
  "summary": {
    "subdomains_found": 45,
    "live_hosts": 38,
    "open_ports": 156,
    "technologies": 12,
    "emails_found": 23
  },
  "subdomains": [
    {
      "subdomain": "www.example.com",
      "ip": "93.184.216.34",
      "ports": [80, 443],
      "technologies": ["Nginx", "React", "Bootstrap"]
    },
    {
      "subdomain": "mail.example.com",
      "ip": "93.184.216.35",
      "ports": [25, 465, 993],
      "technologies": ["Postfix", "Dovecot"]
    }
  ],
  "ports": [
    {
      "host": "www.example.com",
      "port": 80,
      "service": "http",
      "banner": "nginx/1.18.0"
    },
    {
      "host": "www.example.com",
      "port": 443,
      "service": "https",
      "tls": {
        "certificate": {
          "issuer": "Let's Encrypt",
          "valid_until": "2026-08-05",
          "weak_ciphers": false
        }
      }
    }
  ],
  "technologies": [
    {"name": "Nginx", "version": "1.18.0", "category": "web_server"},
    {"name": "React", "version": "18.2.0", "category": "frontend"},
    {"name": "PostgreSQL", "version": "14.5", "category": "database"}
  ],
  "emails": [
    {"email": "admin@example.com", "source": "website"},
    {"email": "support@example.com", "source": "LinkedIn"}
  ],
  "cloud": {
    "provider": "AWS",
    "services": ["EC2", "S3", "CloudFront"],
    "regions": ["us-east-1", "eu-west-1"]
  }
}
```

### List Recon Tasks
```http
GET /api/v1/recon?target=example.com&limit=10
```

### Cancel Recon
```http
DELETE /api/v1/recon/{recon_id}
```

## Modules

### Subdomain Enumeration
```python
# modules/subdomains.py
class SubdomainEnumerator:
    def __init__(self, target: str):
        self.target = target
        self.subdomains = set()
    
    async def enumerate(self):
        # DNS enumeration
        self._dns_enumeration()
        
        # Certificate Transparency
        self._ct_logs()
        
        # Brute-force
        self._bruteforce()
        
        return list(self.subdomains)
    
    def _ct_logs(self):
        # Query crt.sh
        url = f"https://crt.sh/?q=%.{self.target}&output=json"
        response = requests.get(url)
        for entry in response.json():
            self.subdomains.add(entry['name_value'])
```

### Port Scanning
```python
# modules/port_scan.py
import asyncio
from nmap import PortScanner

class PortScanner:
    def __init__(self, target: str, ports: str = "1-65535"):
        self.target = target
        self.ports = ports
    
    async def scan(self):
        loop = asyncio.get_event_loop()
        return await loop.run_in_executor(
            None, self._nmap_scan
        )
    
    def _nmap_scan(self):
        nm = PortScanner()
        nm.scan(self.target, self.ports, arguments='-sV -sC')
        return nm[self.target]
```

### Technology Detection
```python
# modules/tech_detect.py
import requests
from bs4 import BeautifulSoup

class TechDetector:
    def __init__(self, url: str):
        self.url = url
        self.technologies = []
    
    def detect(self):
        response = requests.get(self.url, timeout=10)
        
        # Check headers
        self._check_headers(response.headers)
        
        # Parse HTML
        soup = BeautifulSoup(response.text, 'html.parser')
        self._check_html(soup)
        
        # Check JavaScript
        self._check_js(soup)
        
        return self.technologies
```

## Data Models

### ReconTask
```python
class ReconTask:
    recon_id: str
    target: str
    status: str  # queued, in_progress, completed, failed, cancelled
    modules: List[str]
    options: Dict
    created_by: str
    created_at: datetime
    started_at: Optional[datetime]
    completed_at: Optional[datetime]
    progress: int  # 0-100
```

### ReconResult
```python
class ReconResult:
    recon_id: str
    target: str
    subdomains: List[Subdomain]
    ports: List[Port]
    technologies: List[Technology]
    emails: List[Email]
    cloud_info: Optional[CloudInfo]
    summary: Dict[str, int]
```

## CLI Usage

```bash
# Start reconnaissance
cosmicsec recon start --target example.com --modules subdomains,ports

# Check status
cosmicsec recon status recon_12345

# Get results
cosmicsec recon results recon_12345 --format json

# List all recon tasks
cosmicsec recon list --target example.com
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Start recon
recon = client.recon.start(
    target="example.com",
    modules=["subdomains", "ports", "technologies"]
)

# Wait for completion
recon.wait()

# Get results
results = recon.get_results()
print(f"Found {len(results.subdomains)} subdomains")
print(f"Found {len(results.ports)} open ports")

# Print live hosts
for subdomain in results.subdomains:
    if subdomain.get('ports'):
        print(f"  {subdomain['subdomain']} - {subdomain['ip']}")
```

## Configuration

### Environment Variables
```bash
# Service
RECON_SERVICE_PORT=8004
RECON_SERVICE_HOST=0.0.0.0

# External APIs
VIRUSTOTAL_API_KEY=...
SHODAN_API_KEY=...
HIBP_API_KEY=...  # HaveIBeenPwned

# Tools
NMAP_PATH=/usr/bin/nmap
MASSCAN_PATH=/usr/bin/masscan

# Performance
MAX_CONCURRENT_SCANS=5
REQUEST_TIMEOUT=300
THREADS=10

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec
```

## Wordlists

### Subdomain Wordlists
```bash
# Location: data/wordlists/
- common.txt       # 1000 common subdomains
- top10000.txt     # 10000 most common
- custom.txt       # Your custom list
```

### Add Custom Wordlist
```bash
# Copy your wordlist
cp my_words.txt data/wordlists/

# Use in recon
curl -X POST http://localhost:8004/api/v1/recon \
  -d '{
    "target": "example.com",
    "options": {
      "subdomain_wordlist": "my_words"
    }
  }'
```

## Security Considerations

1. **Rate Limiting** — Respect target's rate limits
2. **Legal Compliance** — Only scan systems you own/have permission
3. **Stealth Mode** — Random delays, user-agent rotation
4. **Data Sensitivity** — Encrypt stored recon data
5. **Audit Logging** — Log all reconnaissance activities

## Monitoring

### Metrics
- `recon_tasks_total` — Total recon tasks by status
- `recon_duration_seconds` — Task duration
- `subdomains_found_total` — Subdomains discovered
- `ports_found_total` — Open ports discovered

### Alerts
- Recon task stuck (no progress for 60+ minutes)
- Rate limit exceeded (429 responses)
- External API quota exceeded

## Troubleshooting

### Recon Stuck
```bash
# Check recon status
curl http://localhost:8004/api/v1/recon/recon_12345

# Check logs
docker logs recon-service | grep recon_12345
```

### Subdomains Not Found
```bash
# Verify target is correct
nslookup example.com

# Check CT logs manually
curl "https://crt.sh/?q=%.example.com&output=json"

# Try different wordlist
# Use "top10000" instead of "common"
```

### External API Errors
```bash
# Test VirusTotal API
curl -H "x-apikey: $VIRUSTOTAL_API_KEY" \
  https://www.virustotal.com/vtapi/v2/domain/report?domain=example.com

# Check API quotas
# Each service has different rate limits
```

## Next Steps

- [Scan Service](./scan-service.md)
- [Report Service](./report-service.md)
- [AI-Powered Recon Analysis](../ai/overview.md)
- [CLI Recon Commands](../cli/commands.md#recon)
- [Python SDK Recon](../sdks/python-sdk.md#recon)
