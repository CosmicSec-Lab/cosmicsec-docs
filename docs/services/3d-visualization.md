# 🎨 3D Visualization Service

## Overview

The **3D Visualization Service** delivers cinematic-quality, interactive 3D network visualizations powered by Three.js and WebGL. Features real-time data streaming, physics simulations, and VR/AR support.

**Location:** `cosmicsec-services/services/3d_visualization/`  
**Port:** 8025  
**Framework:** FastAPI + Three.js + WebGL + WebSockets

## Premium Features

### 1. Cinematic Visualization Engine
- **WebGL 2.0** — Hardware-accelerated 3D rendering
- **Physically-Based Rendering (PBR)** — Realistic materials and lighting
- **Raytracing Support** — Real-time ray-traced shadows (WebGPU)
- **Post-Processing** — Bloom, depth-of-field, ambient occlusion
- **Particle Systems** — 10K+ particles for traffic flows
- **LOD (Level of Detail)** — Automatic quality adjustment

### 2. Interactive Network Topology
- **Force-Directed Graphs** — Physics-based layout (d3-force)
- **Node-Link Diagrams** — Interactive nodes with custom icons
- **Hierarchical Views** — Drill-down from global to local
- **Clustering** — Automatic grouping by subnet/type
- **Path Highlighting** — Trace attack paths visually
- **Timeline Scrubber** — Animate network changes over time

### 3. Threat Heatmaps (Premium)
- **Real-Time Heatmaps** — Threat intensity as 3D heat clouds
- **Animated Attacks** — Visualize attack propagation
- **Severity-Based Coloring** — Color-coded by threat level
- **Geospatial Overlay** — Map-based visualization
- **Risk Terrain** — 3D surface representing risk levels

### 4. Node Types & Icons (20+)
| Node Type | Icon | Description |
|----------|------|-------------|
| **Server** | 🖥️ | Web servers, API servers |
| **Database** | 🗄️ | SQL, NoSQL databases |
| **Firewall** | 🛡️ | Network security devices |
| **Router** | 📡 | Network infrastructure |
| **IoT Device** | 📱 | IoT/OT devices |
| **Cloud** | ☁️ | Cloud services (AWS, Azure) |
| **User** | 👤 | End users, employees |
| **Attacker** | 🎯 | Threat actors, attackers |
| **C2 Server** | 💀 | Command & control servers |
| **Malware** | 🦠️ | Malicious software |

### 5. Real-Time Data Streaming
- **WebSocket Updates** — Live node/edge updates
- **Throttle Control** — Prevent UI overload
- **Diff-Based Updates** — Only send changes
- **Buffering** — Smooth animation interpolation
- **Prediction** — Client-side position prediction

### 6. VR/AR Support (Cutting-Edge)
- **WebXR** — VR headsets (Oculus, HTC Vive)
- **AR Mode** — Mobile AR visualization
- **Immersive Incident Response** — Walk through attack in VR
- **Spatial Audio** — 3D positional audio cues

### 7. Export & Sharing
- **glTF/GLB** — 3D model export (for Blender, Maya)
- **PNG/JPEG** — High-resolution screenshots (4K+)
- **MP4** — Animated video export
- **Interactive HTML** — Standalone HTML with embedded 3D
- **Shareable Links** — Persistent scene URLs

## API Endpoints

### Get Network Topology
```http
GET /api/v1/visualization/topology?scan_id=scan_12345
```

**Response:**
```json
{
  "topology_id": "topo_12345",
  "scan_id": "scan_12345",
  "nodes": [
    {
      "id": "node_001",
      "label": "Web Server",
      "type": "server",
      "ip": "192.168.1.10",
      "severity": "critical",
      "status": "infected",
      "position": {"x": 10, "y": 5, "z": 0},
      "connections": ["node_002", "node_003"],
      "metadata": {
        "os": "Ubuntu 22.04",
        "services": ["nginx", "ssh"],
        "vulnerabilities": 5
      },
      "visual": {
        "color": "#ef4444",
        "pulse": true,
        "icon": "server"
      }
    }
  ],
  "edges": [
    {
      "source": "node_001",
      "target": "node_002",
      "protocol": "https",
      "traffic_volume": 1500,
      "animated": true,
      "color": "#10b981"
    }
  ],
  "clusters": [
    {
      "id": "cluster_001",
      "label": "DMZ",
      "nodes": ["node_001", "node_002"],
      "color": "rgba(255, 0, 0, 0.2)"
    }
  ]
}
```

### Apply Layout Algorithm
```http
POST /api/v1/visualization/layout
```

**Request:**
```json
{
  "topology_id": "topo_12345",
  "algorithm": "force_directed",  // force_directed, hierarchical, circular, random
  "options": {
    "repulsion": 300,
    "attraction": 0.1,
    "gravity": 0.1,
    "iterations": 300
  }
}
```

### Add Node (Real-Time)
```http
POST /api/v1/visualization/node
```

**Request:**
```json
{
  "topology_id": "topo_12345",
  "node": {
    "id": "node_999",
    "label": "New IoT Device",
    "type": "iot",
    "position": {"x": 20, "y": 10, "z": 5}
  }
}
```

### Update Node
```http
PUT /api/v1/visualization/node/{node_id}
```

### Remove Node
```http
DELETE /api/v1/visualization/node/{node_id}
```

### Get Edges
```http
GET /api/v1/visualization/edges?topology_id=topo_12345
```

### Export Scene
```http
POST /api/v1/visualization/export
```

**Request:**
```json
{
  "topology_id": "topo_12345",
  "format": "gltf",  // gltf, png, mp4, html
  "resolution": "4k",
  "camera_angle": "isometric",
  "include_animations": true
}
```

## Frontend Integration

### Three.js Component (React)
```tsx
import { useEffect, useRef } from 'react';
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';

export function ThreeJSVisualization({ nodes, edges, onNodeClick }) {
  const mountRef = useRef<HTMLDivElement>(null);
  const sceneRef = useRef<THREE.Scene>();
  
  useEffect(() => {
    if (!mountRef.current) return;
    
    // Scene setup
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x0a0a2a);
    scene.fog = new THREE.Fog(0x0a0a2a, 50, 200);
    sceneRef.current = scene;
    
    // Camera
    const camera = new THREE.PerspectiveCamera(
      75, 
      mountRef.current.clientWidth / mountRef.current.clientHeight,
      0.1, 
      1000
    );
    camera.position.set(50, 50, 50);
    
    // Renderer
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(mountRef.current.clientWidth, mountRef.current.clientHeight);
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;
    mountRef.current.appendChild(renderer.domElement);
    
    // Controls
    const controls = new OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;
    
    // Lights
    const ambientLight = new THREE.AmbientLight(0x404040, 2);
    scene.add(ambientLight);
    
    const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
    directionalLight.position.set(50, 100, 50);
    directionalLight.castShadow = true;
    scene.add(directionalLight);
    
    // Create nodes
    nodes.forEach(node => {
      const geometry = new THREE.SphereGeometry(2, 32, 32);
      const material = new THREE.MeshStandardMaterial({
        color: getSeverityColor(node.severity),
        metalness: 0.5,
        roughness: 0.5,
        emissive: getSeverityColor(node.severity),
        emissiveIntensity: node.status === 'infected' ? 0.5 : 0
      });
      const sphere = new THREE.Mesh(geometry, material);
      sphere.position.set(node.position.x, node.position.y, node.position.z);
      sphere.castShadow = true;
      sphere.receiveShadow = true;
      sphere.userData = { nodeId: node.id };
      scene.add(sphere);
      
      // Add label (HTML overlay)
      addLabel(node);
    });
    
    // Create edges (with animated particles)
    edges.forEach(edge => {
      const points = getEdgePoints(edge);
      const geometry = new THREE.BufferGeometry().setFromPoints(points);
      const material = new THREE.LineBasicMaterial({
        color: new THREE.Color(edge.color),
        transparent: true,
        opacity: 0.6
      });
      const line = new THREE.Line(geometry, material);
      scene.add(line);
      
      // Animated particles along edge
      if (edge.animated) {
        animateParticles(edge, scene);
      }
    });
    
    // Animation loop
    const animate = () => {
      requestAnimationFrame(animate);
      controls.update();
      renderer.render(scene, camera);
    };
    animate();
    
    return () => {
      mountRef.current?.removeChild(renderer.domElement);
    };
  }, [nodes, edges]);
  
  return (
    <div className="relative w-full h-full">
      <div ref={mountRef} className="w-full h-full" />
      <canvas className="absolute top-0 left-0 pointer-events-none" />
    </div>
  );
}

function getSeverityColor(severity: string): number {
  const colors = {
    'critical': 0xef4444,
    'high': 0xf59e0b,
    'medium': 0x3b82f6,
    'low': 0x10b981
  };
  return colors[severity] || 0x6b7280;
}

function animateParticles(edge: Edge, scene: THREE.Scene) {
  const particleCount = 50;
  const geometry = new THREE.BufferGeometry();
  const positions = new Float32Array(particleCount * 3);
  geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
  
  const material = new THREE.PointsMaterial({
    color: 0x00ff00,
    size: 0.5,
    transparent: true,
    opacity: 0.8
  });
  
  const particles = new THREE.Points(geometry, material);
  scene.add(particles);
  
  // Animate along path
  let t = 0;
  const animate = () => {
    t += 0.01;
    if (t > 1) t = 0;
    // Update particle positions along edge path
    // ...
    requestAnimationFrame(animate);
  };
  animate();
}
```

### Heatmap Overlay
```tsx
import { HeatmapLayer } from './HeatmapLayer';

export function ThreatHeatmap({ threats }) {
  const heatmapData = threats.map(t => ({
    x: t.location.x,
    y: t.location.y,
    intensity: getSeverityScore(t.severity)
  }));
  
  return (
    <Canvas>
      <HeatmapLayer data={heatmapData} />
    </Canvas>
  );
}
```

## WebSocket Real-Time Updates

```typescript
import { WebSocket } from 'ws';

const ws = new WebSocket('ws://localhost:8025/api/v1/visualization/ws');

ws.onmessage = (event) => {
  const message = JSON.parse(event.data);
  
  switch (message.type) {
    case 'node_added':
      addNodeToScene(message.node);
      break;
    case 'node_updated':
      updateNodeInScene(message.node_id, message.updates);
      break;
    case 'edge_animated':
      animateEdge(message.edge_id);
      break;
    case 'threat_detected':
      showThreatPulse(message.location);
      break;
  }
};

// Send camera position for shared views
ws.send(JSON.stringify({
  type: 'camera_update',
  position: camera.position,
  rotation: camera.rotation
}));
```

## CLI Usage

```bash
# Export topology as glTF
cosmicsec viz export \
  --topology topo_12345 \
  --format gltf \
  --resolution 4k;

# Generate screenshot
cosmicsec viz screenshot \
  --topology topo_12345 \
  --angle isometric \
  --output network.png;

# Apply layout
cosmicsec viz layout \
  --topology topo_12345 \
  --algorithm force-directed \
  --repulsion 300;

# Animate attack path
cosmicsec viz animate \
  --topology topo_12345 \
  --path attack_path_123 \
  --output attack.mp4
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Get topology
topology = client.viz.get_topology(scan_id="scan_12345")

print(f"Nodes: {len(topology.nodes)}")
print(f"Edges: {len(topology.edges)}")

# Print critical nodes
for node in topology.nodes:
    if node.severity == 'critical':
        print(f"  CRITICAL: {node.label} ({node.ip})")

# Apply layout
client.viz.apply_layout(
    topology_id=topology.topology_id,
    algorithm='force_directed',
    options={'repulsion': 300, 'iterations': 300}
)

# Export as glTF (for Blender)
export_url = client.viz.export(
    topology_id=topology.topology_id,
    format='gltf',
    resolution='4k'
)
print(f"Exported to: {export_url}")
```

## Particle System Example

```javascript
// Create particle system for traffic flow
const particleCount = 1000;
const particles = new THREE.BufferGeometry();
const positions = new Float32Array(particleCount * 3);
const colors = new Float32Array(particleCount * 3);

for (let i = 0; i < particleCount; i++) {
  // Random position in network
  positions[i * 3] = Math.random() * 100 - 50;
  positions[i * 3 + 1] = Math.random() * 100 - 50;
  positions[i * 3 + 2] = Math.random() * 100 - 50;
  
  // Color based on traffic type
  colors[i * 3] = 0;     // R
  colors[i * 3 + 1] = 1;   // G
  colors[i * 3 + 2] = 0;   // B (green for normal traffic)
}

particles.setAttribute('position', new THREE.BufferAttribute(positions, 3));
particles.setAttribute('color', new THREE.BufferAttribute(colors, 3));

const material = new THREE.PointsMaterial({
  size: 0.5,
  vertexColors: true,
  transparent: true,
  opacity: 0.6,
  blending: THREE.AdditiveBlending
});

const particleSystem = new THREE.Points(particles, material);
scene.add(particleSystem);

// Animate particles
function animateParticles() {
  const positions = particles.attributes.position.array;
  for (let i = 0; i < positions.length; i += 3) {
    positions[i] += (Math.random() - 0.5) * 0.1;  // Drift
  }
  particles.attributes.position.needsUpdate = true;
}
```

## VR/AR Mode (WebXR)

```typescript
import { VRButton } from 'three/examples/jsm/webxr/VRButton';

// Enable VR
document.body.appendChild(VRButton.createButton(renderer));

// VR session
renderer.xr.enabled = true;

const animate = () => {
  renderer.setAnimationLoop(() => {
    // Update XR camera
    const xrCamera = renderer.xr.getCamera(camera);
    // Render scene from XR perspective
    renderer.render(scene, xrCamera);
  });
};
```

## Performance Optimization

### Level of Detail (LOD)
```javascript
// Reduce quality for distant objects
const lod = new THREE.LOD();

// High detail (close)
const highDetail = new THREE.Mesh(geometry, materialHigh);
lod.addLevel(highDetail, 0);  // 0-20 units

// Medium detail
const medDetail = new THREE.Mesh(simplifiedGeometry, materialMed);
lod.addLevel(medDetail, 20);  // 20-50 units

// Low detail (far)
const lowDetail = new THREE.Mesh(basicGeometry, materialLow);
lod.addLevel(lowDetail, 50);  // 50+ units

scene.add(lod);
```

### Frustum Culling
```javascript
// Only render objects in camera view
const frustum = new THREE.Frustum();
const matrix = new THREE.Matrix4().multiplyMatrices(
  camera.projectionMatrix,
  camera.matrixWorldInverse
);
frustum.setFromProjectionMatrix(matrix);

// Check if object is in view
if (frustum.intersectsObject(node)) {
  renderer.render(node, camera);
}
```

## Configuration

### Environment Variables
```bash
# Service
VIZ_SERVICE_PORT=8025
VIZ_SERVICE_HOST=0.0.0.0

# Three.js/WebGL
MAX_PARTICLES=10000
ENABLE_RAYTRACING=true
ENABLE_POST_PROCESSING=true
MAX_FPS=60

# Physics (for force-directed)
PHYSICS_ENABLED=true
REPULSION_FORCE=300
ATTRACTION_FORCE=0.1
GRAVITY_FORCE=0.1

# Real-Time
WEBSOCKET_MAX_CONNECTIONS=100
UPDATE_THROTTLE_MS=50
BUFFER_SIZE=1000

# Export
EXPORT_DIR=/app/exports
MAX_EXPORT_RESOLUTION=4k
VIDEO_FPS=60

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec

# Redis (for real-time)
REDIS_URL=redis://redis:6379
```

## Monitoring

### Metrics
- `viz_nodes_rendered` — Nodes in scene
- `viz_edges_rendered` — Edges in scene
- `viz_fps` — Frames per second
- `viz_particles_active` — Active particle count
- `viz_websocket_connections` — Active WebSocket connections

### Performance Profiling
```javascript
// Three.js performance monitor
import { Stats } from 'three/examples/jsm/libs/stats.module';

const stats = new Stats();
document.body.appendChild(stats.dom);

const animate = () => {
  stats.begin();
  renderer.render(scene, camera);
  stats.end();
  requestAnimationFrame(animate);
};
```

## Troubleshooting

### WebGL Not Supported
```javascript
// Check WebGL support
const canvas = document.createElement('canvas');
const gl = canvas.getContext('webgl') || canvas.getContext('experimental-webgl');

if (!gl) {
  alert('WebGL not supported. Use Chrome/Firefox with hardware acceleration enabled.');
}
```

### Low FPS
```javascript
// Reduce particle count
const particleCount = 500;  // Reduce from 10000

// Disable post-processing
ENABLE_POST_PROCESSING=false;

// Use simpler geometries
const geometry = new THREE.BoxGeometry(1, 1, 1);  // Instead of SphereGeometry
```

### WebSocket Disconnects
```bash
# Check service logs
docker logs 3d-visualization | grep "websocket"

# Verify Redis connectivity
docker exec 3d-visualization redis-cli ping

# Check client-side
# Browser console: monitor WebSocket readyState
```

## Next Steps

- [Breach Attack Simulation](./breach-attack-sim.md)
- [Edge Computing](./edge-computing.md)
- [IoT/OT Security](./iot-ot-security.md)
- [Frontend 3D Viz Guide](../frontend/3d-viz.md)
- [WebGL Performance Tuning](../guides/webgl-optimization.md)
