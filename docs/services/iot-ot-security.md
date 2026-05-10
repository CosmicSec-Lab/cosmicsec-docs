# 📡 IoT/OT Security

## Overview

The **IoT/OT Security Service** monitors and secures Internet of Things (IoT) and Operational Technology (OT) environments. It supports 5G, Modbus, BACnet, Zigbee, and other industrial protocols.

**Location:** `cosmicsec-services/services/iot_ot_security/`  
**Port:** 8020  
**Framework:** FastAPI

## Features

### 1. Protocol Support
- **5G/4G/LTE** — Cellular IoT device monitoring
- **Modbus TCP/RTU** — Industrial PLC communication
- **BACnet** — Building automation systems
- **Zigbee** — Wireless mesh networking
- **Z-Wave** — Home automation
- **MQTT** — IoT messaging protocol
- **CoAP** — Constrained Application Protocol
- **OPC UA** — Industrial interoperability
- **DNP3** — Electric utility communications
- **IEC 61850** — Substation automation

### 2. Device Discovery
- **Network Scanning** — Discover IoT devices on network
- **Fingerprinting** — Identify device types, firmware
- **Asset Inventory** — Track all IoT/OT assets
- **Topology Mapping** — Visual network map

### 3. Vulnerability Assessment
- **CVE Matching** — Match devices to known CVEs
- **Default Credential Check** — Test common username/passwords
- **Firmware Analysis** — Check for outdated firmware
- **Configuration Audit** — Check insecure configurations

### 4. Traffic Monitoring
- **Protocol Analysis** — Decode industrial protocols
- **Anomaly Detection** — Unusual traffic patterns
- **Command Logging** — Log all OT commands
- **Real-Time Alerts** — Immediate threat notification

### 5. Compliance
- **IEC 62443** — Industrial security standard
- **NIST Cybersecurity Framework** — Comprehensive framework
- **NERC CIP** — Energy sector compliance

## API Endpoints

### Discover Devices
```http
POST /api/v1/iot/discover
```

**Request:**
```json
{
  "network": "192.168.1.0/24",
  "protocols": ["modbus", "bacnet", "mqtt"],
  "options": {
    "scan_speed": "comprehensive",
    "credential_check": true
  }
}
```

**Response:**
```json
{
  "discovery_id": "disc_12345",
  "status": "in_progress",
  "network": "192.168.1.0/24",
  "estimated_devices": 50
}
```

### Get Discovery Results
```http
GET /api/v1/iot/discover/{discovery_id}/results
```

**Response:**
```json
{
  "discovery_id": "disc_12345",
  "devices": [
    {
      "ip": "192.168.1.100",
      "mac": "00:14:22:01:23:45",
      "device_type": "PLC",
      "manufacturer": "Siemens",
      "model": "S7-1200",
      "firmware": "4.2.1",
      "protocols": ["modbus-tcp"],
      "open_ports": [102, 502],
      "vulnerabilities": [
        {
          "cve": "CVE-2024-1234",
          "severity": "high",
          "description": "Authentication bypass..."
        }
      ]
    },
    {
      "ip": "192.168.1.101",
      "device_type": "HMI",
      "manufacturer": "Allen-Bradley",
      "model": "PanelView Plus",
      "firmware": "10.0",
      "protocols": ["ethernet-ip"],
      "vulnerabilities": []
    }
  ],
  "summary": {
    "total_devices": 45,
    "vulnerable_devices": 12,
    "critical_issues": 3
  }
}
```

### Monitor Traffic
```http
POST /api/v1/iot/monitor
```

**Request:**
```json
{
  "device_ip": "192.168.1.100",
  "protocol": "modbus",
  "duration": 300,
  "capture_packets": true
}
```

### Get Traffic Analysis
```http
GET /api/v1/iot/monitor/{monitor_id}/analysis
```

**Response:**
```json
{
  "monitor_id": "mon_12345",
  "device": "192.168.1.100",
  "protocol": "modbus",
  "duration": 300,
  "packets_captured": 15000,
  "anomalies_detected": 3,
  "commands_logged": [
    {
      "timestamp": "2026-05-05T22:00:00Z",
      "function_code": 6,
      "register": 40001,
      "value": 1,
      "description": "Write single register"
    }
  ],
  "alerts": [
    {
      "type": "unauthorized_command",
      "severity": "critical",
      "description": "Unexpected write to register 40001"
    }
  ]
}
```

### Run Compliance Check
```http
POST /api/v1/iot/compliance
```

**Request:**
```json
{
  "standard": "iec_62443",
  "devices": ["192.168.1.100", "192.168.1.101"]
}
```

**Response:**
```json
{
  "compliance_id": "comp_12345",
  "standard": "iec_62443",
  "overall_score": 65,
  "status": "non_compliant",
  "findings": [
    {
      "control": "SR 1.1",
      "description": "Human readable audit trails",
      "status": "fail",
      "remediation": "Enable audit logging on all devices"
    }
  ]
}
```

## Protocol Implementations

### Modbus TCP
```python
# protocols/modbus.py
from pymodbus.client import ModbusTcpClient

class ModbusScanner:
    def __init__(self, host: str, port: int = 502):
        self.host = host
        self.port = port
    
    def scan(self):
        client = ModbusTcpClient(self.host, port=self.port)
        
        # Read holding registers
        result = client.read_holding_registers(0, 10)
        
        # Check for common vulnerabilities
        vulns = self._check_vulnerabilities(client)
        
        client.close()
        return vulns
```

### MQTT
```python
# protocols/mqtt.py
import paho.mqtt.client as mqtt

class MQTTMonitor:
    def __init__(self, broker: str, port: int = 1883):
        self.broker = broker
        self.port = port
        self.client = mqtt.Client()
        self.client.on_message = self._on_message
    
    def monitor(self, topics: List[str] = ["#"]):
        for topic in topics:
            self.client.subscribe(topic)
        self.client.connect(self.broker, self.port)
        self.client.loop_forever()
    
    def _on_message(self, client, userdata, msg):
        print(f"Topic: {msg.topic}, Payload: {msg.payload}")
        # Analyze for anomalies
        self._analyze_traffic(msg)
```

### BACnet
```python
# protocols/bacnet.py
from bacpypes.apdu import ReadPropertyRequest
from bacpypes.core import run

class BACnetScanner:
    def scan(self, address: str):
        # Discover BACnet devices
        devices = self._who_is()
        
        for device in devices:
            # Read device properties
            props = self._read_properties(device)
            # Check for vulnerabilities
            self._check_security(props)
```

## Data Models

### IoTDevice
```python
class IoTDevice:
    device_id: str
    ip: str
    mac: Optional[str]
    device_type: str  # PLC, HMI, Sensor, Gateway, etc.
    manufacturer: Optional[str]
    model: Optional[str]
    firmware: Optional[str]
    protocols: List[str]
    open_ports: List[int]
    vulnerabilities: List[Vulnerability]
    last_seen: datetime
```

### TrafficAnalysis
```python
class TrafficAnalysis:
    monitor_id: str
    device_id: str
    protocol: str
    duration: int
    packets_captured: int
    anomalies_detected: int
    commands_logged: List[Command]
    alerts: List[Alert]
```

## 3D Visualization

The IoT/OT service integrates with the **3D Visualization** frontend:

```typescript
// Frontend integration
const iotData = await api.getIoTDevices();

// Pass to Three.js visualization
<ThreeJSVisualization
  nodes={iotData.devices.map(d => ({
    id: d.device_id,
    label: d.device_type,
    ip: d.ip,
    severity: d.vulnerabilities.length > 0 ? 'high' : 'low'
  }))}
  edges={iotData.connections}
/>
```

## CLI Usage

```bash
# Discover devices
cosmicsec iot discover --network 192.168.1.0/24 --protocols modbus,bacnet

# Monitor traffic
cosmicsec iot monitor --device 192.168.1.100 --protocol modbus

# Check compliance
cosmicsec iot compliance --standard iec_62443

# List devices
cosmicsec iot list-devices --network 192.168.1.0/24
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Discover devices
discovery = client.iot.discover(
    network="192.168.1.0/24",
    protocols=["modbus", "bacnet", "mqtt"]
)

# Wait for completion
discovery.wait()

# Get results
results = discovery.get_results()
print(f"Found {len(results.devices)} devices")
print(f"Vulnerable devices: {results.summary['vulnerable_devices']}")

# Monitor traffic
monitor = client.iot.monitor(
    device_ip="192.168.1.100",
    protocol="modbus",
    duration=300
)

analysis = monitor.get_analysis()
for alert in analysis.alerts:
    print(f"[{alert['severity'].upper()}] {alert['description']}")
```

## Configuration

### Environment Variables
```bash
# Service
IOT_SERVICE_PORT=8020
IOT_SERVICE_HOST=0.0.0.0

# Protocols
MODBUS_ENABLED=true
BACNET_ENABLED=true
MQTT_ENABLED=true
ZIGBEE_ENABLED=false

# Discovery
SCAN_THREADS=10
DEVICE_TIMEOUT=5
CREDENTIAL_CHECK=true

# Compliance
IEC_62443_ENABLED=true
NIST_ENABLED=true
NERC_CIP_ENABLED=true

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec
```

## Security Considerations

1. **OT Safety** — Read-only mode by default, don't disrupt operations
2. **Protocol Authentication** — Many OT protocols lack auth, add it
3. **Network Segmentation** — Isolate OT networks from IT
4. **Firmware Updates** — Keep device firmware updated
5. **Physical Security** — Secure physical access to devices

## Monitoring

### Metrics
- `iot_devices_discovered_total` — Devices by type
- `iot_vulnerabilities_found_total` — Vulns by severity
- `iot_traffic_packets_total` — Packets captured
- `iot_compliance_score` — Compliance score by standard

### Alerts
- Critical vulnerability found on OT device
- Unauthorized command detected
- Device goes offline
- Compliance score drops below threshold

## Troubleshooting

### Devices Not Found
```bash
# Check network connectivity
ping 192.168.1.100

# Verify protocol is running
nmap -p 502 192.168.1.100  # Modbus

# Check service logs
docker logs iot-ot-service | grep "discovery"
```

### Protocol Connection Failed
```bash
# Test Modbus connectivity
pip install pymodbus
python -c "from pymodbus.client import ModbusTcpClient; \
  c = ModbusTcpClient('192.168.1.100'); print(c.read_holding_registers(0, 1))"

# Check firewall rules
# OT protocols often blocked by default
```

### Compliance Check Failing
```bash
# Review compliance report
curl http://localhost:8020/api/v1/iot/compliance/comp_12345

# Focus on failed controls
# Each finding has remediation steps
```

## Next Steps

- [3D Visualization Service](./3d-visualization.md)
- [DDoS Protection](./ddos-protection.md)
- [ZTNA (Zero-Trust)](./ztna.md)
- [Frontend IoT Dashboard](../frontend/pages.md#iot-security)
- [Protocol Security Guide](../guides/iot-security.md)
