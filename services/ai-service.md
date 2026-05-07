# 🤖 AI Service (Helix Engine)

## Overview

The **AI Service** (codenamed **Helix**) is the most advanced AI-powered security intelligence engine. It combines multiple LLM providers, real-time learning, automated remediation, and predictive analytics.

**Location:** `cosmicsec-ai/services/ai_service/`  
**Port:** 8003  
**Framework:** FastAPI

## Features

### 1. Multi-Model Ensemble (Premium)
Combines multiple AI models for superior accuracy:
- **OpenAI GPT-4o** — Advanced reasoning and analysis
- **Anthropic Claude 3 Opus** — Deep security context understanding
- **Meta Llama 3.1 70B** — Local high-performance inference
- **Ollama** — Offline-capable local models

### 2. Real-Time Learning (Premium)
- Continuous adaptation to new threats
- Feedback loops with security analysts
- Automatic model adjustment based on performance
- Learning rate: 0.01, momentum: 0.9

### 3. Automated Remediation (Premium)
- Generates fix playbooks by vulnerability type
- Step-by-step remediation guidance
- Priority-based action items
- Integration with orchestration tools

### 4. Predictive Analytics (Premium)
- Forecast attack trends
- Resource needs prediction
- Anomaly detection with IsolationForest + z-score
- Zero-day vulnerability forecasting

### 5. Core AI Capabilities
- Vulnerability analysis and prioritization
- Threat hunting with MITRE ATT&CK mapping
- Natural language queries (NLP Search)
- Report generation and summarization
- Code security review
- Phishing detection
- Malware analysis
- Log analysis and anomaly detection

## API Endpoints (30+)

### Health & Status
```http
GET /health
```
Returns service health and model status.

```http
GET /ai/models
```
Lists available AI models and their status.

### Core Analysis
```http
POST /ai/analyze
```
Analyze vulnerabilities or security data.

**Request:**
```json
{
  "content": "SQL injection in login form allows...",
  "context": {
    "target": "example.com",
    "scan_id": "scan_12345"
  },
  "analysis_type": "vulnerability"
}
```

**Response:**
```json
{
  "analysis_id": "analysis_001",
  "severity": "critical",
  "summary": "Critical SQL injection vulnerability...",
  "recommendations": [
    "Use parameterized queries",
    "Implement input validation",
    "Deploy WAF rules"
  ],
  "confidence": 0.95,
  "model_used": "gpt-4o"
}
```

### Vulnerability Analysis
```http
POST /ai/vulnerability/analyze
```
Deep analysis of a specific vulnerability.

```http
POST /ai/vulnerability/prioritize
```
Prioritize vulnerabilities by risk score.

### Threat Hunting
```http
POST /ai/threat-hunting/query
```
NLP-powered threat hunting queries.

**Request:**
```json
{
  "query": "Show me all critical vulnerabilities in web applications"
}
```

**Response:**
```json
{
  "results": [
    {
      "vuln_id": "vuln_001",
      "title": "SQL Injection in login form",
      "severity": "critical",
      "target": "example.com"
    }
  ],
  "summary": "Found 5 critical vulnerabilities...",
  "mitre_mapping": ["T1190", "T1059"]
}
```

### MITRE ATT&CK Mapping
```http
POST /ai/mitre/map
```
Map threats to MITRE ATT&CK framework.

**Response:**
```json
{
  "threat": "SQL Injection",
  "mitre_techniques": [
    {
      "id": "T1190",
      "name": "Exploit Public-Facing Application",
      "tactic": "Initial Access"
    }
  ]
}
```

### Report Generation
```http
POST /ai/report/generate
```
Generate AI-powered security reports.

**Request:**
```json
{
  "scan_id": "scan_12345",
  "report_type": "executive",
  "format": "pdf"
}
```

### Code Review
```http
POST /ai/code/review
```
AI-powered code security review.

**Request:**
```json
{
  "code": "def login(username, password):\n    query = f\"SELECT...\"",
  "language": "python",
  "context": "authentication function"
}
```

**Response:**
```json
{
  "issues": [
    {
      "type": "sql_injection",
      "severity": "critical",
      "line": 2,
      "description": "SQL injection vulnerability...",
      "fix": "Use parameterized queries: cursor.execute('SELECT...', (username,))"
    }
  ]
}
```

### Phishing Detection
```http
POST /ai/phishing/detect
```
Analyze emails/URLs for phishing indicators.

### Malware Analysis
```http
POST /ai/malware/analyze
```
Analyze file hashes or samples for malware.

### Log Analysis
```http
POST /ai/logs/analyze
```
Analyze logs for anomalies and threats.

### Chat & Copilot
```http
POST /ai/chat
```
Interactive AI security assistant.

**Request:**
```json
{
  "message": "How do I fix a SQL injection vulnerability?",
  "context": {
    "user_id": "user_123",
    "session_id": "session_456"
  }
}
```

**Response:**
```json
{
  "response": "To fix SQL injection vulnerabilities...",
  "suggestions": [
    "Use parameterized queries",
    "Implement ORM frameworks",
    "Validate input with whitelists"
  ],
  "references": ["OWASP SQL Injection Prevention"]
}
```

### WebSocket (Real-Time)
```websocket
WS /ai/chat/ws
```
Real-time chat with AI assistant.

## Premium Features

### Multi-Model Ensemble
```http
POST /ai/ensemble/analyze
```
Combine multiple AI models for consensus.

**Request:**
```json
{
  "content": "Analyze this vulnerability...",
  "models": ["gpt-4o", "claude-3-opus", "llama-3.1-70b"],
  "strategy": "weighted_vote"  // majority, weighted_vote, average
}
```

**Response:**
```json
{
  "consensus": {
    "severity": "critical",
    "confidence": 0.92
  },
  "model_results": {
    "gpt-4o": {"severity": "critical", "confidence": 0.95},
    "claude-3-opus": {"severity": "critical", "confidence": 0.90},
    "llama-3.1-70b": {"severity": "high", "confidence": 0.88}
  }
}
```

### Real-Time Learning
```http
POST /ai/learning/feedback
```
Submit feedback to improve AI models.

**Request:**
```json
{
  "analysis_id": "analysis_001",
  "rating": 5,  // 1-5 stars
  "correct": true,
  "feedback": "Excellent analysis, very accurate",
  "user_id": "analyst_123"
}
```

```http
GET /ai/learning/metrics
```
Get learning performance metrics.

**Response:**
```json
{
  "total_feedback": 1250,
  "accuracy": 0.94,
  "model_performance": {
    "gpt-4o": {"accuracy": 0.96, "feedback_count": 500},
    "claude-3-opus": {"accuracy": 0.95, "feedback_count": 400},
    "llama-3.1-70b": {"accuracy": 0.91, "feedback_count": 350}
  },
  "learning_rate": 0.01,
  "last_updated": "2026-05-05T22:00:00Z"
}
```

### Automated Remediation
```http
POST /ai/remediation/generate
```
Generate fix playbooks for vulnerabilities.

**Request:**
```json
{
  "vulnerability_type": "sql_injection",
  "context": {
    "language": "python",
    "framework": "django",
    "code_snippet": "query = f'SELECT...'"
  }
}
```

**Response:**
```json
{
  "playbook_id": "playbook_001",
  "title": "Fix SQL Injection in Python/Django",
  "steps": [
    {
      "step": 1,
      "action": "Replace string formatting with parameterized query",
      "code": "cursor.execute('SELECT * FROM users WHERE username = %s', (username,))",
      "verification": "Test with SQLMap to verify fix"
    },
    {
      "step": 2,
      "action": "Implement input validation",
      "code": "if not re.match(r'^[a-zA-Z0-9_]+$', username): raise ValueError",
      "verification": "Unit test with invalid inputs"
    }
  ],
  "estimated_time": "30 minutes",
  "priority": "critical"
}
```

### Predictive Analytics
```http
POST /ai/predictive/datapoint
```
Add data point for predictive modeling.

```http
GET /ai/predictive/trend
```
Forecast attack trends.

**Response:**
```json
{
  "predictions": [
    {
      "threat_type": "ransomware",
      "predicted_increase": "35%",
      "timeframe": "next_30_days",
      "confidence": 0.87
    },
    {
      "threat_type": "supply_chain",
      "predicted_increase": "22%",
      "timeframe": "next_90_days",
      "confidence": 0.79
    }
  ],
  "anomalies_detected": 3,
  "zero_day_forecast": {
    "probability": 0.15,
    "timeframe": "next_7_days"
  }
}
```

## AI Models Configuration

### Model Registry
```python
AI_MODELS = {
    "gpt-4o": {
        "provider": "openai",
        "enabled": True,
        "priority": 1,
        "timeout": 30
    },
    "claude-3-opus": {
        "provider": "anthropic",
        "enabled": True,
        "priority": 2,
        "timeout": 30
    },
    "llama-3.1-70b": {
        "provider": "ollama",
        "enabled": True,
        "priority": 3,
        "timeout": 60
    }
}
```

### Ensemble Strategies
- **Majority Vote** — Most common prediction wins
- **Weighted Vote** — Models weighted by historical accuracy
- **Average** — Average confidence scores
- **Adaptive** — Dynamic weighting based on context

## Data Models

### Analysis Request
```python
class AnalysisRequest:
    content: str
    context: Optional[Dict]
    analysis_type: str  # vulnerability, threat, log, code, etc.
    models: Optional[List[str]]
    options: Optional[Dict]
```

### Analysis Result
```python
class AnalysisResult:
    analysis_id: str
    severity: str
    summary: str
    recommendations: List[str]
    confidence: float
    model_used: str
    mitre_mapping: Optional[List[str]]
    metadata: Optional[Dict]
```

### Feedback
```python
class Feedback:
    analysis_id: str
    rating: int  # 1-5
    correct: bool
    feedback: Optional[str]
    user_id: str
    timestamp: datetime
```

## LangGraph Agents (Advanced)

The AI service uses **LangGraph** for autonomous security agents:

### Agent Types
1. **Vulnerability Analyst** — Deep vuln analysis
2. **Threat Hunter** — Proactive threat detection
3. **Incident Responder** — Automated incident handling
4. **Compliance Checker** — Regulatory compliance verification

### Agent Workflow
```python
from langgraph import StateGraph

# Define agent state
class AgentState:
    messages: List[Message]
    current_step: str
    findings: List[Finding]

# Build agent graph
graph = StateGraph(AgentState)
graph.add_node("analyze", analyze_vulnerability)
graph.add_node("hunt", hunt_threats)
graph.add_node("respond", respond_to_incident)
graph.add_edge("analyze", "hunt")
graph.add_edge("hunt", "respond")

# Execute
result = await graph.run(initial_state)
```

## Anomaly Detection

### IsolationForest
```python
from sklearn.ensemble import IsolationForest

# Train on historical data
model = IsolationForest(contamination=0.1)
model.fit(historical_data)

# Detect anomalies
anomalies = model.predict(current_data)
```

### Z-Score Analysis
```python
from scipy import stats

z_scores = stats.zscore(data)
anomalies = abs(z_scores) > 3  # 3 sigma rule
```

## Integration with Other Services

### Scan Service → AI Service
```
Scan completes → Send vulns to AI → Get analysis → Store results
```

### Report Service → AI Service
```
Request report → AI generates content → Format and deliver
```

### Notification Service ← AI Service
```
Critical threat detected → AI analyzes → Send alert via notification
```

## Monitoring

### Metrics
- `ai_requests_total` — Total AI requests by type
- `ai_request_duration_seconds` — Request latency
- `ai_model_accuracy` — Model accuracy by provider
- `ai_feedback_count` — Feedback submissions
- `ai_anomalies_detected_total` — Anomalies found

### Cost Tracking
- Tokens used per model
- Cost per request
- Monthly budget tracking
- Cost optimization recommendations

## Configuration

### Environment Variables
```bash
# Service
AI_SERVICE_PORT=8003
AI_SERVICE_HOST=0.0.0.0

# AI Providers
OPENAI_API_KEY=sk-...         # optional: OpenAI key
# Optional Anthropic key (only if you want to use Claude models)
ANTHROPIC_API_KEY=sk-ant-...  # optional
OLLAMA_URL=http://ollama:11434

# Models
DEFAULT_MODEL=gpt-4o
ENABLE_ENSEMBLE=true
ENABLE_LEARNING=true

# Learning
LEARNING_RATE=0.01
MOMENTUM=0.9
FEEDBACK_RETENTION_DAYS=365

# Rate Limiting
MAX_REQUESTS_PER_MINUTE=1000
MAX_TOKENS_PER_REQUEST=4096

# Redis (for caching)
REDIS_URL=redis://redis:6379
```

## CLI Usage

```bash
# Analyze vulnerability
cosmicsec ai analyze --content "SQL injection in..."

# Chat with AI
cosmicsec ai chat

# Generate report
cosmicsec ai report --scan-id scan_12345

# Threat hunting
cosmicsec ai hunt --query "critical vulnerabilities"
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="your_api_key")

# Analyze vulnerability
analysis = client.ai.analyze(
    content="SQL injection vulnerability...",
    analysis_type="vulnerability"
)
print(f"Severity: {analysis.severity}")

# Multi-model ensemble
consensus = client.ai.ensemble_analyze(
    content="Analyze this threat...",
    models=["gpt-4o", "claude-3-opus"]
)

# Submit feedback
client.ai.submit_feedback(
    analysis_id="analysis_001",
    rating=5,
    correct=True
)
```

## Troubleshooting

### Model Not Responding
```bash
# Check model status
curl http://localhost:8003/ai/models

# Test model directly
curl -X POST http://localhost:8003/ai/analyze \
  -H "Content-Type: application/json" \
  -d '{"content": "test", "analysis_type": "test"}'
```

### High Latency
```bash
# Check model timeouts
# Consider using faster model (Llama for local, GPT-4o for complex)
# Enable caching for repeated queries
```

### Learning Not Improving
```bash
# Check feedback data
curl http://localhost:8003/ai/learning/metrics

# Ensure feedback is being submitted
# Adjust learning rate if needed
```

## Next Steps

- [Multi-Model Ensemble Details](../ai/ensemble.md)
- [Real-Time Learning Guide](../ai/learning.md)
- [Automated Remediation](../ai/remediation.md)
- [Predictive Analytics](../ai/predictive.md)
- [LangGraph Agents](../ai/agents.md)
