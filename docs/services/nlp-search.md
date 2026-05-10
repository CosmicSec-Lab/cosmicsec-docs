# 🤖 NLP Search Service

## Overview

The **NLP Search Service** provides intent-recognition search with entity extraction, fuzzy matching, and command palette integration (Ctrl+K).

**Location:** `cosmicsec-services/services/nlp_search/`  
**Port:** 8031  
**Framework:** FastAPI + spaCy + Hugging Face Transformers

## Premium Features

### 1. Intent Recognition (15+ Intents)
| Intent | Example Query | Action |
|--------|----------------|--------|
| `list_vulnerabilities` | "show me critical vulns" | Filter by severity |
| `create_scan` | "scan example.com" | Open scan form |
| `generate_report` | "create report for scan_123" | Start report generation |
| `threat_hunt` | "hunt for ransomware" | Open threat hunting |
| `check_compliance` | "GDPR compliance status" | Show compliance dashboard |
| `manage_users` | "invite user to org" | Open user management |

### 2. Entity Extraction
- **CVE IDs** — CVE-2024-12345
- **IP Addresses** — IPv4 and IPv6
- **Domains/URLs** — example.com, https://...
- **Scan IDs** — scan_12345, report_67890
- **Severity Levels** — critical, high, medium, low
- **Date Ranges** — "last 7 days", "since May 1st"

### 3. Fuzzy Matching
- **Typo Tolerance** — "critical" matches "crtical"
- **Synonym Recognition** — "vulnerabilities" = "vulns" = "issues"
- **Phonetic Matching** — Soundex algorithm support
- **Levenshtein Distance** — Up to 3 character differences

### 4. Command Palette (Ctrl+K)
- **Quick Actions** — Keyboard-driven interface
- **Real-Time Suggestions** — As you type
- **Recent Searches** — History with one-click
- **Keyboard Navigation** — Arrow keys, Enter, Escape

### 5. Context Awareness
- **Page Context** — Search within current page
- **User Role** — Filter results by permissions
- **Time Zone** — "yesterday" = relative to user TZ
- **Organization** — Scope to org's assets

## API Endpoints

### Search
```http
POST /api/v1/nlp/search
```

**Request:**
```json
{
  "query": "show me all critical vulnerabilities in web applications from last 7 days",
  "context": {
    "page": "/dashboard",
    "user_id": "user_123",
    "organization_id": "org_12345",
    "timezone": "America/New_York"
  },
  "limit": 10
}
```

**Response:**
```json
{
  "query": "show me all critical vulnerabilities in web applications from last 7 days",
  "intent": {
    "name": "list_vulnerabilities",
    "confidence": 0.95,
    "parameters": {
      "severity": "critical",
      "target_type": "web_applications",
      "time_range": {
        "start": "2026-04-28T00:00:00Z",
        "end": "2026-05-05T23:59:59Z"
      }
    }
  },
  "entities": [
    {
      "type": "severity",
      "value": "critical",
      "start": 12,
      "end": 20
    },
    {
      "type": "target_type",
      "value": "web_applications",
      "start": 24,
      "end": 42
    },
    {
      "type": "date_range",
      "value": "last 7 days",
      "start": 46,
      "end": 58,
      "normalized": {
        "start": "2026-04-28",
        "end": "2026-05-05"
      }
    }
  ],
  "action": {
    "type": "redirect",
    "url": "/scans?severity=critical&target_type=web_app&created_after=2026-04-28",
    "api_call": {
      "endpoint": "/api/v1/scans",
      "params": {
        "severity": "critical",
        "target_type": "web_app",
        "created_after": "2026-04-28"
      }
    }
  },
  "suggestions": [
    "show me high vulnerabilities",
    "scan example.com for web apps",
    "generate report for last scan"
  ]
}
```

### Get Recognized Intents
```http
GET /api/v1/nlp/intents
```

**Response:**
```json
{
  "intents": [
    {
      "name": "list_vulnerabilities",
      "description": "List vulnerabilities with filters",
      "example_queries": [
        "show me critical vulnerabilities",
        "list all high severity issues"
      ]
    },
    {
      "name": "create_scan",
      "description": "Create a new scan",
      "example_queries": [
        "scan example.com",
        "run web app scan on target.com"
      ]
    }
  ]
}
```

### Get Extracted Entities
```http
GET /api/v1/nlp/entities?query=CVE-2024-12345%20on%20example.com
```

**Response:**
```json
{
  "query": "CVE-2024-12345 on example.com",
  "entities": [
    {
      "type": "cve",
      "value": "CVE-2024-12345",
      "confidence": 1.0
    },
    {
      "type": "domain",
      "value": "example.com",
      "confidence": 0.98
    }
  ]
}
```

### Submit Feedback
```http
POST /api/v1/nlp/feedback
```

**Request:**
```json
{
  "query": "show me critical vulns",
  "intent": "list_vulnerabilities",
  "correct": true,
  "user_id": "user_123",
  "notes": "Correctly identified intent despite typo in 'vulns'"
}
```

## Frontend Integration

### Command Palette (Ctrl+K)
```tsx
import { useState, useEffect, useRef } from 'react';
import { GlassCard } from '@/components/ui/GlassCard';

export function CommandPalette() {
  const [isOpen, setIsOpen] = useState(false);
  const [query, setQuery] = useState('');
  const [results, setResults] = useState(null);
  const [selectedIndex, setSelectedIndex] = useState(0);
  const inputRef = useRef<HTMLInputElement>(null);
  
  useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      if ((e.metaKey || e.ctrlKey) && e.key === 'k') {
        e.preventDefault();
        setIsOpen(!isOpen);
      }
      if (e.key === 'Escape') {
        setIsOpen(false);
      }
    };
    
    window.addEventListener('keydown', handleKeyDown);
    return () => window.removeEventListener('keydown', handleKeyDown);
  }, []);
  
  useEffect(() => {
    if (isOpen) {
      inputRef.current?.focus();
    }
  }, [isOpen]);
  
  const handleSearch = async () => {
    if (!query.trim()) return;
    
    const response = await api.post('/api/v1/nlp/search', {
      query,
      context: {
        page: window.location.pathname,
        user_id: currentUser?.user_id
      }
    });
    
    setResults(response);
    setSelectedIndex(0);
  };
  
  const handleKeyDown = (e: React.KeyboardEvent) => {
    if (e.key === 'ArrowDown') {
      e.preventDefault();
      setSelectedIndex(prev => Math.min(prev + 1, results?.suggestions?.length || 0));
    } else if (e.key === 'ArrowUp') {
      e.preventDefault();
      setSelectedIndex(prev => Math.max(prev - 1, 0));
    } else if (e.key === 'Enter' && results) {
      e.preventDefault();
      if (results.action?.url) {
        window.location.href = results.action.url;
      }
    }
  };
  
  if (!isOpen) return null;
  
  return (
    <div className="fixed inset-0 bg-black/50 flex items-start justify-center pt-20 z-50">
      <GlassCard blur={20} opacity={0.95} className="w-full max-w-2xl">
        <input
          ref={inputRef}
          type="text"
          value={query}
          onChange={(e) => {
            setQuery(e.target.value);
            handleSearch();
          }}
          onKeyDown={handleKeyDown}
          placeholder="Type a command or search..."
          className="w-full p-4 text-lg bg-transparent border-none outline-none"
        />
        
        {results && (
          <div className="border-t border-white/10">
            {/* Intent */}
            {results.intent && (
              <div className="p-4 bg-white/5">
                <div className="text-sm text-cyan-400">
                  Intent: {results.intent.name} ({Math.round(results.intent.confidence * 100)}% confidence)
                </div>
              </div>
            )}
            
            {/* Action */}
            {results.action && (
              <div className="p-4 hover:bg-white/10 cursor-pointer">
                <div className="font-semibold">{results.action.type === 'redirect' ? '→' : '⚡'} {results.action.url || results.action.endpoint}</div>
              </div>
            )}
            
            {/* Suggestions */}
            {results.suggestions?.map((suggestion, idx) => (
              <div
                key={idx}
                className={`p-3 cursor-pointer ${
                  idx === selectedIndex ? 'bg-white/20' : 'hover:bg-white/10'
                }`}
                onClick={() => {
                  setQuery(suggestion);
                  handleSearch();
                }}
              >
                {suggestion}
              </div>
            ))}
          </div>
        )}
      </GlassCard>
    </div>
  );
}
```

## CLI Usage

```bash
# Search via CLI
cosmicsec search "show me critical vulnerabilities"

# NLP analysis only
cosmicsec nlp parse "scan example.com for web apps"

# Get intent
cosmicsec nlp intent "generate report for scan_123"

# Extract entities
cosmicsec nlp entities "CVE-2024-12345 on example.com"
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Search
results = client.nlp.search(
    query="show me all critical vulnerabilities from last 7 days",
    context={
        "page": "/dashboard",
        "user_id": "user_123"
    }
)

print(f"Intent: {results.intent['name']} ({results.intent['confidence']*100:.1f}%)")

if results.action:
    print(f"Action: {results.action['type']}")
    if 'url' in results.action:
        print(f"  Redirect to: {results.action['url']}")

# Extract entities
entities = client.nlp.extract_entities(
    query="CVE-2024-12345 on example.com"
)

print("\nEntities:")
for entity in entities.entities:
    print(f"  {entity['type']}: {entity['value']} ({entity['confidence']*100:.0f}%)")

# Submit feedback
client.nlp.submit_feedback(
    query="show me critical vulns",
    intent="list_vulnerabilities",
    correct=True,
    notes="Correctly handled typo"
)
```

## Configuration

### Environment Variables
```bash
# Service
NLP_SEARCH_PORT=8031
NLP_SEARCH_HOST=0.0.0.0

# NLP Models
SPACY_MODEL=en_core_web_sm
TRANSFORMER_MODEL=bert-base-uncased
ENTITY_RECOGNITION_ENABLED=true

# Fuzzy Matching
FUZZY_MATCHING_ENABLED=true
LEVENSHTEIN_THRESHOLD=3
TYPOS_TOLERANCE=0.8

# Command Palette
COMMAND_PALETTE_ENABLED=true
MAX_SUGGESTIONS=10
RECENT_SEARCHES_COUNT=20

# Context
CONTEXT_AWARENESS_ENABLED=true
USER_ROLE_FILTERING=true
TIMEZONE_AWARE=true

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec
```

## Next Steps

- [Notification Service](./notification-service.md)
- [Compliance Service](./compliance-service.md)
- [Org Service](./org-service.md)
- [Admin Service](./admin-service.md)
- [NLP Search Guide](../guides/nlp-search.md)
