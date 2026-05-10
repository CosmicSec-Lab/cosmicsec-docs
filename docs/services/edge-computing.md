# 🌐 Edge Computing Service#

## Overview#

The **Edge Computing Service** delivers fog computing architecture with 1000+ edge nodes, real-time latency optimization, and offline-capable security scanning at the edge.

**Location:** `cosmicsec-services/services/edge_computing/`  
**Port:** 8027  
**Framework:** FastAPI + K3s + KubeEdge

## Premium Features#

### 1. Edge Node Management (1000+ Nodes)
- **Hierarchical Topology** — Cloud → Fog → Edge → Endpoint
- **Automatic Discovery** — Zeroconf/mDNS detection
- **Health Monitoring** — CPU, memory, disk, network
- **Auto-Scaling** — Scale nodes based on demand
- **Node Grouping** — Geographic, functional, security zones

### 2. Fog Computing Architecture
```
Cloud (Central)
    ↓
Fog Nodes (Regional - AWS Wavelength, Azure Stack)
    ↓
Edge Nodes (Local - 5G base stations, IoT gateways)
    ↓
Endpoints (Sensors, cameras, industrial devices)
```

### 3. Latency Optimization
- **Nearest Node Selection** — GeoDNS + latency probing
- **Content Caching** — Cache scan results at edge
- **Request Routing** — Route to optimal node
- **Load Balancing** — Consistent hashing, round-robin
- **P2P Mesh** — Nodes share load via WebRTC

### 4. Offline Capability
- **Local Scanning** — Scan continues without cloud
- **Queue & Sync** — Results queued, synced when online
- **Local AI Models** — Ollama at the edge
- **Disconect Detection** — Automatic failover
- **Data Compression** — Minimize sync bandwidth

### 5. Edge Security
- **Edge Firewall** — iptables/nftables management
- **Intrusion Detection** — Suricata at the edge
- **TLS Termination** — At edge (reduce cloud load)
- **Secret Management** — HashiCorp Vault at edge
- **Secure Boot** — TPM 2.0, Secure Enclave

## API Endpoints#

### Register Edge Node
```http
POST /api/v1/edge/nodes
```

**Request:**
```json
{
  "node_id": "edge-5g-cell-001",
  "location": {
    "latitude": 40.7128,
    "longitude": -74.0060,
    "region": "us-east-1",
    "zone": "nyc-5g-001"
  },
  "capabilities": {
    "cpu_cores": 8,
    "memory_gb": 16,
    "storage_gb": 256,
    "network_mbps": 1000,
    "supports": ["nmap", "nuclei", "nuclei"]
  },
  "tier": "5g_base_station",  // 5g_base_station, iot_gateway, industrial_pc
  "organic_cluster": "k3s-cluster-001"
}
```

**Response:**
```json
{
  "node_id": "edge-5g-cell-001",
  "status": "registered",
  "tier": "5g_base_station",
  "websocket_url": "wss://edge-5g-cell-001.company.com:8027/ws",
  "certificate": "-----BEGIN CERTIFICATE-----\n...",
  "created_at": "2026-05-05T22:00:00Z"
}
```

### List Edge Nodes
```http
GET /api/v1/edge/nodes?status=online&tier=5g_base_station&limit=100
```

**Response:**
```json
{
  "nodes": [
    {
      "node_id": "edge-5g-cell-001",
      "tier": "5g_base_station",
      "status": "online",
      "location": {"region": "us-east-1", "zone": "nyc-5g-001"},
      "metrics": {
        "cpu_usage": 45.2,
        "memory_usage": 62.8,
        "disk_usage": 34.5,
        "network_latency_ms": 12,
        "active_scans": 3,
        "queued_scans": 0
      },
      "capabilities": {"cpu_cores": 8, "memory_gb": 16},
      "last_seen": "2026-05-05T22:00:00Z",
      "uptime_seconds": 259200
    }
  ],
  "total": 150,
  "online": 147,
  "offline": 3
}
```

### Get Node Details
```http
GET /api/v1/edge/nodes/{node_id}
```

### Update Node Configuration
```http
PUT /api/v1/edge/nodes/{node_id}
```

**Request:**
```json
{
  "capabilities": {
    "supports": ["nmap", "nuclei", "nikto", "zap"]
  },
  "config": {
    "max_concurrent_scans": 5,
    "scan_timeout": 3600,
    "offline_mode": false
  }
}
```

### Deploy to Edge
```http
POST /api/v1/edge/deploy
```

**Request:**
```json
{
  "target_nodes": ["edge-5g-cell-001", "edge-5g-cell-002"],
  "deployment": {
    "type": "scan_worker",
    "image": "cosmicsec/scan-worker:latest",
    "replicas": 3,
    "resources": {
      "cpu": "2",
      "memory": "4Gi"
    }
  },
  "offline_policy": "queue",  // queue, discard, fail
  "sync_interval": 300
}
```

### Get Edge Metrics
```http
GET /api/v1/edge/metrics?timeframe=1h&aggregation=avg
```

**Response:**
```json
{
  "timeframe": "1h",
  "aggregation": "avg",
  "metrics": {
    "total_nodes": 150,
    "online_nodes": 147,
    "avg_cpu_usage": 42.3,
    "avg_memory_usage": 58.7,
    "avg_latency_ms": 15.2,
    "total_scans_processed": 1500,
    "offline_nodes_count": 3,
    "p2p_mesh_links": 450
  },
  "by_tier": {
    "5g_base_station": {"count": 50, "avg_latency_ms": 8.5},
    "iot_gateway": {"count": 80, "avg_latency_ms": 18.2},
    "industrial_pc": {"count": 20, "avg_latency_ms": 12.1}
  }
}
```

### Get Fog Topology
```http
GET /api/v1/edge/topology
```

**Response:**
```json
{
  "topology_id": "fog-topo-001",
  "nodes": [
    {
      "id": "cloud-001",
      "tier": "cloud",
      "connections": ["fog-nyc-001", "fog-lon-001"]
    },
    {
      "id": "fog-nyc-001",
      "tier": "fog",
      "connections": ["edge-5g-cell-001", "edge-5g-cell-002"]
    },
    {
      "id": "edge-5g-cell-001",
      "tier": "edge",
      "connections": ["sensor-001", "sensor-002"],
      "protocol": "5g"
    }
  ]
}
```

## Edge Deployment Strategies#

### 5G Edge (Ultra-Low Latency)
```yaml
# k3s deployment for 5G base station
apiVersion: apps/v1
kind: Deployment
metadata:
  name: scan-worker-5g
spec:
  replicas: 3
  template:
    spec:
      nodeSelector:
        tier: "5g_base_station"
      containers:
      - name: scan-worker
        image: cosmicsec/scan-worker:latest
        resources:
          limits:
            cpu: "2"
            memory: "4Gi"
        env:
        - name: EDGE_MODE
          value: "5g"
        - name: OFFLINE_QUEUE
          value: "redis://localhost:6379"
```

### IoT Gateway (Medium Latency)
```yaml
# Raspberry Pi / Industrial PC
apiVersion: v1
kind: Pod
spec:
  nodeSelector:
    tier: "iot_gateway"
  containers:
  - name: edge-agent
    image: cosmicsec/edge-agent:arm64
    volumeMounts:
    - name: docker-socket
      mountPath: /var/run/docker.sock
  volumes:
  - name: docker-socket
    hostPath:
      path: /var/run/docker.sock
```

## Offline Mode#

### Queue & Sync Architecture
```mermaid
sequenceDiagram
    participant E as 🖥️ Edge Node
    participant Q as 📦 Redis Queue
    participant C as ☁️ Cloud
    
    Note over E,C: Online Mode
    E->>C: Send scan results
    C->>E: ACK
    
    Note over E,C: Disconnect
    E->>Q: Queue results (offline)
    Note over E: Continue scanning
    
    Note over E,C: Reconnect
    Q->>C: Sync queued results
    C->>Q: ACK & Clear queue
```

### Offline Configuration
```json
{
  "offline_mode": true,
  "offline_policy": "queue",  // queue, discard, fail
  "queue_max_size": 10000,
  "sync_interval": 300,
  "compression": "gzip",  // gzip, lz4, zstd
  "encryption": true
}
```

## P2P Mesh Networking#

### WebRTC Data Channels
```javascript
// Edge node P2P mesh
const mesh = new EdgeMesh('edge-001');

// Discover peers
mesh.on('peer_discovered', (peer) => {
  console.log(`New peer: ${peer.id}`);
  mesh.connect(peer.id);
});

// Share scan load
mesh.on('scan_request', (request) => {
  if (currentLoad < maxLoad) {
    processScan(request);
  } else {
    mesh.forward('edge-002', request);
  }
});
```

## CLI Usage#

```bash
# Register edge node
cosmicsec edge register \
  --node-id edge-5g-cell-001 \
  --tier 5g_base_station \
  --location "40.7128,-74.0060" \
  --region us-east-1;

# List nodes
cosmicsec edge list --status online --limit 100;

# Deploy scan worker
cosmicsec edge deploy \
  --nodes edge-5g-cell-001,edge-5g-cell-002 \
  --image cosmicsec/scan-worker:latest \
  --replicas 3;

# Get metrics
cosmicsec edge metrics --timeframe 1h --aggregation avg;

# View topology
cosmicsec edge topology --format json;

# Drain node (maintenance)
cosmicsec edge drain edge-5g-cell-001 --grace-period 300s
```

## Python SDK Usage#

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Register edge node
node = client.edge.register(
    node_id="edge-5g-cell-001",
    location={
        "latitude": 40.7128,
        "longitude": -74.0060,
        "region": "us-east-1"
    },
    capabilities={
        "cpu_cores": 8,
        "memory_gb": 16,
        "supports": ["nmap", "nuclei"]
    },
    tier="5g_base_station"
)
print(f"Node registered: {node.node_id}")

# List nodes
nodes = client.edge.list(status="online", limit=100)
print(f"Online nodes: {len(nodes.nodes)}")

# Get metrics
metrics = client.edge.get_metrics(
    timeframe="1h",
    aggregation="avg"
)
print(f"Average latency: {metrics.metrics['avg_latency_ms']}ms")

# Deploy to edge
deployment = client.edge.deploy(
    target_nodes=["edge-5g-cell-001"],
    deployment={
        "type": "scan_worker",
        "image": "cosmicsec/scan-worker:latest",
        "replicas": 3
    }
)
print(f"Deployed with {deployment.replicas} replicas")

# Monitor offline nodes
offline = [n for n in nodes.nodes if n.status == 'offline']
print(f"Offline nodes: {len(offline)}")
```

## Configuration#

### Environment Variables
```bash
# Service
EDGE_SERVICE_PORT=8027
EDGE_SERVICE_HOST=0.0.0.0

# K3s / Kubernetes
K3S_TOKEN=...
K3S_URL=https://localhost:6443
KUBECONFIG=/etc/rancher/k3s/k3s.yaml

# Node Discovery
ZEROCONF_ENABLED=true
MDNS_DOMAIN=local.
DISCOVERY_INTERVAL=60

# Offline Mode
OFFLINE_QUEUE_URL=redis://redis:6379
OFFLINE_MAX_QUEUE_SIZE=10000
SYNC_INTERVAL=300

# P2P Mesh
WEBRTC_ENABLED=true
STUN_SERVERS=stun:stun.l.google.com:19302
TURN_SERVER=turn:turn.cosmicsec.com:3478

# Security
EDGE_FIREWALL_ENABLED=true
SURICATA_ENABLED=true
VAULT_URL=https://vault:8200

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec
```

## Monitoring#

### Grafana Dashboard
Pre-built dashboard shows:
- **Node Health** — Online/offline status (heatmap)
- **Latency Map** — Geographic latency visualization
- **Resource Usage** — CPU/memory/disk per tier
- **Scan Distribution** — Load balancing across nodes
- **Offline Events** — Disconnect/reconnect timeline

### Prometheus Metrics
- `edge_nodes_total` — Total nodes by tier
- `edge_nodes_online` — Online node count
- `edge_latency_ms` — Latency histogram
- `edge_offline_events_total` — Disconnect events
- `edge_scans_processed_total` — Scans by node
- `edge_p2p_mesh_links` — Active P2P connections

## Troubleshooting#

### Node Not Registering
```bash
# Check K3s status
kubectl get nodes

# Check node logs
docker logs edge-agent

# Test connectivity
ping edge-5g-cell-001.company.com

# Check zeroconf
avahi-browse -a
```

### High Latency
```bash
# Check network path
traceroute edge-5g-cell-001.company.com

# Check node load
kubectl top node edge-5g-cell-001

# Verify 5G connection
curl http://localhost:8027/metrics | grep latency
```

### Offline Sync Fails
```bash
# Check Redis queue
redis-cli -h redis LLEN offline_queue

# Check sync logs
docker logs edge-service | grep "sync"

# Test cloud connectivity
curl -I https://api.cosmicsec.com/health
```

## Next Steps#

- [SLA Manager](./sla-manager.md)
- [Theme Builder](./theme-builder.md)
- [Onboarding Wizard](./onboarding-wizard.md)
- [IoT/OT Security](./iot-ot-security.md)
- [Edge Computing Guide](../guides/edge-computing.md)
