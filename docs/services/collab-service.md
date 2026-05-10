# 👥 Collab Service — SOC Collaboration

## Overview

The **Collab Service** enables real-time collaboration for Security Operations Center (SOC) teams. Features include shared whiteboards, war rooms, live chat, and action item tracking.

**Location:** `cosmicsec-services/services/collab_service/`  
**Port:** 8006  
**Framework:** FastAPI + WebSockets

## Features

### 1. Real-Time Whiteboard
- **Collaborative Drawing** — Multiple users draw simultaneously
- **Shapes & Annotations** — Rectangles, circles, text, arrows
- **Timeline Visualization** — Incident timeline with events
- **Asset Mapping** — Drag-and-drop network assets
- **Export** — PNG, SVG, PDF exports

### 2. War Room
- **Incident-Specific Rooms** — Isolated collaboration spaces
- **Role-Based Access** — Lead, analyst, viewer roles
- **Screen Sharing** — WebRTC-based screen sharing
- **Voice/Video** — WebRTC audio/video calls
- **Recording** — Session recording for later review

### 3. Live Chat
- **Real-Time Messaging** — Instant team communication
- **File Sharing** — Upload images, logs, reports
- **Code Snippets** — Syntax-highlighted code sharing
- **Message Threading** — Reply to specific messages
- **@Mentions** — Notify specific team members

### 4. Shared Notes
- **Collaborative Editing** — Multiple users edit simultaneously (CRDT)
- **Rich Text** — Formatting, tables, images
- **Version History** — Track changes, revert to previous
- **Templates** — Incident response playbooks
- **Export** — Markdown, PDF, HTML

### 5. Action Items
- **Task Assignment** — Assign to team members
- **Priority Levels** — Critical, high, medium, low
- **Due Dates** — Set deadlines
- **Status Tracking** — Todo, in progress, blocked, done
- **Notifications** — Reminders for upcoming deadlines

## API Endpoints

### Whiteboard

#### Create Whiteboard
```http
POST /api/v1/collab/whiteboard
```

**Request:**
```json
{
  "incident_id": "incident_12345",
  "title": "Ransomware Investigation",
  "initial_data": {
    "nodes": [],
    "edges": []
  }
}
```

**Response:**
```json
{
  "whiteboard_id": "wb_12345",
  "websocket_url": "ws://localhost:8006/collab/ws/whiteboard/wb_12345",
  "created_at": "2026-05-05T22:00:00Z"
}
```

#### Get Whiteboard
```http
GET /api/v1/collab/whiteboard/{whiteboard_id}
```

#### Update Whiteboard
```http
PUT /api/v1/collab/whiteboard/{whiteboard_id}
```

### War Room

#### Create War Room
```http
POST /api/v1/collab/war-room
```

**Request:**
```json
{
  "incident_id": "incident_12345",
  "name": "Ransomware Attack - Acme Corp",
  "members": ["analyst_123", "analyst_456"],
  "roles": {
    "analyst_123": "lead",
    "analyst_456": "analyst"
  }
}
```

**Response:**
```json
{
  "war_room_id": "wr_12345",
  "chat_websocket": "ws://localhost:8006/collab/ws/chat/wr_12345",
  "whiteboard_id": "wb_12345",
  "created_at": "2026-05-05T22:00:00Z"
}
```

### Live Chat (WebSocket)

#### Connect to Chat
```javascript
const ws = new WebSocket('ws://localhost:8006/collab/ws/chat/wr_12345');

ws.onopen = () => {
  // Join room
  ws.send(JSON.stringify({
    type: 'join',
    user_id: 'analyst_123',
    name: 'John Doe'
  }));
};

ws.onmessage = (event) => {
  const message = JSON.parse(event.data);
  console.log(`${message.sender}: ${message.content}`);
};
```

#### Send Message
```json
{
  "type": "message",
  "content": "Found C2 server at 192.168.1.100",
  "attachments": [
    {
      "type": "image",
      "url": "http://localhost:8006/files/screenshot.png"
    }
  ]
}
```

#### Message Types
| Type | Description |
|------|-------------|
| `join` | User joins room |
| `leave` | User leaves room |
| `message` | Chat message |
| `file_upload` | File attachment |
| `typing` | User is typing indicator |

### Shared Notes

#### Create Note
```http
POST /api/v1/collab/notes
```

**Request:**
```json
{
  "war_room_id": "wr_12345",
  "title": "Investigation Notes",
  "content": "# Ransomware Investigation\n\n## Findings\n- C2: 192.168.1.100\n- Patient zero: CEO laptop"
}
```

#### Update Note (Real-Time via WebSocket)
```json
{
  "type": "note_update",
  "note_id": "note_12345",
  "operations": [
    {"type": "insert", "pos": 50, "text": "New finding: "},
    {"type": "delete", "pos": 100, "length": 10}
  ]
}
```

### Action Items

#### Create Action Item
```http
POST /api/v1/collab/action-items
```

**Request:**
```json
{
  "war_room_id": "wr_12345",
  "title": "Isolate infected machines",
  "description": "Disconnect all machines in HR department from network",
  "assigned_to": "analyst_456",
  "priority": "critical",
  "due_date": "2026-05-05T23:00:00Z"
}
```

**Response:**
```json
{
  "action_item_id": "ai_12345",
  "status": "todo",
  "created_at": "2026-05-05T22:00:00Z"
}
```

#### Update Action Item Status
```http
PUT /api/v1/collab/action-items/{action_item_id}
```

**Request:**
```json
{
  "status": "in_progress",
  "note": "Started isolating machines"
}
```

## WebSocket Events

### Whiteboard Events
```json
{
  "event": "draw",
  "user": "analyst_123",
  "data": {
    "tool": "rectangle",
    "coords": [100, 200, 300, 400],
    "color": "#ef4444",
    "thickness": 2
  }
}
```

### Chat Events
```json
{
  "event": "message",
  "user": "analyst_123",
  "data": {
    "message_id": "msg_12345",
    "content": "Check the firewall logs",
    "timestamp": "2026-05-05T22:00:00Z",
    "attachments": []
  }
}
```

## Frontend Integration

### React Whiteboard Component
```tsx
import { useEffect, useRef } from 'react';
import { WebSocketClient } from '@/lib/websocket';

export function Whiteboard({ whiteboardId }) {
  const canvasRef = useRef<HTMLCanvasElement>(null);
  const ws = useRef<WebSocketClient>();
  
  useEffect(() => {
    ws.current = new WebSocketClient(
      `ws://localhost:8006/collab/ws/whiteboard/${whiteboardId}`
    );
    
    ws.current.on('draw', (data) => {
      drawOnCanvas(data);
    });
    
    return () => ws.current?.close();
  }, [whiteboardId]);
  
  const handleDraw = (coords) => {
    ws.current?.send({
      event: 'draw',
      data: { tool: 'rectangle', coords, color: '#ef4444' }
    });
  };
  
  return (
    <canvas
      ref={canvasRef}
      width={800}
      height={600}
      onMouseDown={handleDraw}
    />
  );
}
```

### Real-Time Chat Component
```tsx
import { useState } from 'react';
import { useWebSocket } from '@/hooks/useWebSocket';

export function Chat({ warRoomId }) {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState('');
  const ws = useWebSocket(`/collab/ws/chat/${warRoomId}`);
  
  ws.on('message', (msg) => {
    setMessages(prev => [...prev, msg]);
  });
  
  const sendMessage = () => {
    ws.send({
      type: 'message',
      content: input
    });
    setInput('');
  };
  
  return (
    <div className="chat-container">
      <div className="messages">
        {messages.map(msg => (
          <div key={msg.message_id}>
            <strong>{msg.sender}:</strong> {msg.content}
          </div>
        ))}
      </div>
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
        onKeyPress={(e) => e.key === 'Enter' && sendMessage()}
      />
    </div>
  );
}
```

## CLI Usage

```bash
# Create war room
cosmicsec collab war-room create \
  --incident-id incident_12345 \
  --members analyst_123,analyst_456

# List war rooms
cosmicsec collab war-room list

# Create action item
cosmicsec collab action-item create \
  --war-room wr_12345 \
  --title "Isolate infected machines" \
  --priority critical \
  --assign analyst_456
```

## Configuration

### Environment Variables
```bash
# Service
COLLAB_SERVICE_PORT=8006
COLLAB_SERVICE_HOST=0.0.0.0

# WebSockets
WS_HEARTBEAT_INTERVAL=30
WS_MAX_CONNECTIONS=1000
WS_MESSAGE_QUEUE_SIZE=100

# File Uploads
MAX_FILE_SIZE=10485760  # 10MB
UPLOAD_DIR=/app/uploads
ALLOWED_FILE_TYPES=image/png,image/jpeg,text/plain

# WebRTC (for screen sharing)
STUN_SERVERS=stun:stun.l.google.com:19302
TURN_SERVER=turn:turn.cosmicsec.com:3478
TURN_USERNAME=user
TURN_PASSWORD=pass

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec
```

## Data Models

### WarRoom
```python
class WarRoom:
    war_room_id: str
    incident_id: str
    name: str
    members: List[str]
    roles: Dict[str, str]  # user_id -> role
    whiteboard_id: str
    chat_enabled: bool
    screen_sharing_enabled: bool
    recording_enabled: bool
    created_at: datetime
    closed_at: Optional[datetime]
```

### ActionItem
```python
class ActionItem:
    action_item_id: str
    war_room_id: str
    title: str
    description: str
    assigned_to: str
    priority: str  # critical, high, medium, low
    status: str    # todo, in_progress, blocked, done
    due_date: Optional[datetime]
    created_at: datetime
    completed_at: Optional[datetime]
```

## Monitoring

### Metrics
- `collab_war_rooms_total` — Total active war rooms
- `collab_websocket_connections` — Active WebSocket connections
- `collab_messages_total` — Total chat messages
- `collab_action_items_total` — Action items by status

### Alerts
- War room inactive for 2+ hours
- Action item overdue
- WebSocket connection spike

## Troubleshooting

### WebSocket Connection Fails
```bash
# Check service is running
curl http://localhost:8006/health

# Test WebSocket
wscat -c ws://localhost:8006/collab/ws/chat/wr_12345

# Check firewall allows WebSocket upgrades
```

### Messages Not Delivered
```bash
# Check Redis (for Pub/Sub)
docker exec redis redis-cli
> PUBLISH collab_chat "test"

# Check logs
docker logs collab-service | grep "message"
```

### File Upload Fails
```bash
# Check upload directory permissions
docker exec collab-service ls -la /app/uploads

# Check file size limits
docker exec collab-service env | grep MAX_FILE_SIZE
```

## Next Steps

- [SOC Workflow Guide](../guides/soc-workflow.md)
- [Incident Response Playbooks](../guides/incident-response.md)
- [AI Chat Integration](../services/ai-service.md#chat--copilot)
- [Notification Service](../services/notification-service.md)
