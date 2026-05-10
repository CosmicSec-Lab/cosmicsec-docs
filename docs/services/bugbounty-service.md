# 🎯 Bug Bounty Service

## Overview

The **Bug Bounty Service** manages vulnerability disclosure programs, researcher submissions, triage workflows, and automated payouts via Stripe integration.

**Location:** `cosmicsec-services/services/bugbounty_service/`  
**Port:** 8007  
**Framework:** FastAPI

## Features

### 1. Program Management
- **Create Bounty Programs** — Define scope, rewards, rules
- **Safe Harbor** — Legal protection for researchers
- **Custom Rewards** — Critical $5000, High $2000, etc.
- **Scope Management** — In-scope/out-of-scope assets
- **Leaderboard** — Top researchers ranking

### 2. Vulnerability Submission
- **Web Form** — Easy submission interface
- **API Endpoint** — Programmatic submissions
- **Evidence Upload** — Screenshots, PoC code, videos
- **Duplicate Detection** — Auto-detect duplicate submissions
- **Anonymous Option** — Privacy-preserving submissions

### 3. Triage Workflow
- **Initial Review** — Validate submission
- **Severity Rating** — CVSS scoring
- **Reward Assignment** — Auto-calculate payout
- **Status Tracking** — New → In Review → Accepted → Paid
- **Dispute Resolution** — Handle disagreements

### 4. Payment Integration
- **Stripe Connect** — Automated payouts
- **Multi-Currency** — USD, EUR, BTC, ETH
- **Tax Forms** — W-9/W-8BEN collection
- **Payment History** — Complete audit trail
- **KYC Verification** — Know Your Customer checks

## API Endpoints

### Programs

#### Create Program
```http
POST /api/v1/bounty/programs
```

**Request:**
```json
{
  "name": "Acme Corp Bug Bounty",
  "scope": {
    "in_scope": ["*.acme.com", "api.acme.com"],
    "out_of_scope": ["*.test.acme.com"],
    "excluded_technologies": ["third-party-plugins"]
  },
  "rewards": {
    "critical": 5000,
    "high": 2000,
    "medium": 500,
    "low": 100
  },
  "rules": "No DOS testing, no social engineering...",
  "safe_harbor": true,
  "min_severity": "medium"
}
```

**Response:**
```json
{
  "program_id": "prog_12345",
  "name": "Acme Corp Bug Bounty",
  "status": "active",
  "submission_email": "security@acme.com",
  "created_at": "2026-05-05T22:00:00Z"
}
```

#### List Programs
```http
GET /api/v1/bounty/programs?status=active&limit=10
```

#### Get Program
```http
GET /api/v1/bounty/programs/{program_id}
```

#### Update Program
```http
PUT /api/v1/bounty/programs/{program_id}
```

### Submissions

#### Submit Vulnerability
```http
POST /api/v1/bounty/submissions
```

**Request:**
```json
{
  "program_id": "prog_12345",
  "researcher_id": "researcher_789",
  "title": "SQL Injection in Login Form",
  "description": "The login form is vulnerable to SQL injection...",
  "severity": "critical",
  "cvss_score": 9.8,
  "evidence": {
    "screenshots": ["https://.../screenshot1.png"],
    "poc_code": "'; DROP TABLE users;--",
    "curl_command": "curl -X POST https://.../login -d 'user=admin'\\''..."
  },
  "impact": "Complete database compromise",
  "remediation": "Use parameterized queries"
}
```

**Response:**
```json
{
  "submission_id": "sub_12345",
  "status": "new",
  "estimated_reward": 5000,
  "submitted_at": "2026-05-05T22:00:00Z"
}
```

#### List Submissions
```http
GET /api/v1/bounty/submissions?program_id=prog_12345&status=accepted
```

#### Triage Submission
```http
PUT /api/v1/bounty/submissions/{submission_id}/triage
```

**Request:**
```json
{
  "status": "accepted",
  "severity": "critical",
  "cvss_score": 9.8,
  "reward_amount": 5000,
  "triage_notes": "Valid submission, critical impact confirmed",
  "cve_id": "CVE-2026-12345"
}
```

### Payments

#### Process Payout
```http
POST /api/v1/bounty/payouts
```

**Request:**
```json
{
  "submission_id": "sub_12345",
  "amount": 5000,
  "currency": "USD",
  "payment_method": "stripe",
  "researcher_stripe_id": "acct_12345"
}
```

**Response:**
```json
{
  "payout_id": "payout_12345",
  "status": "processing",
  "arrival_date": "2026-05-08T22:00:00Z",
  "fee": 150
}
```

#### Get Leaderboard
```http
GET /api/v1/bounty/leaderboard?program_id=prog_12345&year=2026
```

**Response:**
```json
{
  "leaderboard": [
    {
      "rank": 1,
      "researcher": "security_researcher_1",
      "total_earnings": 25000,
      "valid_submissions": 15,
      "reputation_score": 98
    }
  ]
}
```

## Data Models

### BountyProgram
```python
class BountyProgram:
    program_id: str
    name: str
    organization_id: str
    scope: Dict[str, List[str]]
    rewards: Dict[str, int]
    rules: str
    safe_harbor: bool
    min_severity: str
    status: str  # active, paused, closed
    created_at: datetime
    updated_at: datetime
```

### Submission
```python
class Submission:
    submission_id: str
    program_id: str
    researcher_id: str
    title: str
    description: str
    severity: str
    cvss_score: Optional[float]
    evidence: Dict[str, Any]
    status: str  # new, in_review, accepted, rejected, paid
    reward_amount: Optional[int]
    cve_id: Optional[str]
    submitted_at: datetime
    triaged_at: Optional[datetime]
    paid_at: Optional[datetime]
```

## Frontend Integration

### Submit Vulnerability Page
```tsx
// pages/bugbounty/SubmitVuln.tsx
import { GlassCard, GlassButton } from '@/components/ui/GlassCard';

export function SubmitVuln() {
  const [submission, setSubmission] = useState({
    title: '',
    description: '',
    severity: 'medium',
    evidence: { screenshots: [], poc_code: '' }
  });
  
  const handleSubmit = async () => {
    const result = await api.post('/api/v1/bounty/submissions', submission);
    toast.success(`Submission ${result.submission_id} created!`);
  };
  
  return (
    <GlassCard blur={15} opacity={0.9}>
      <h2>Submit Vulnerability</h2>
      <input 
        placeholder="Title" 
        value={submission.title}
        onChange={(e) => setSubmission({...submission, title: e.target.value})}
      />
      <textarea 
        placeholder="Description" 
        value={submission.description}
        onChange={(e) => setSubmission({...submission, description: e.target.value})}
      />
      <select 
        value={submission.severity}
        onChange={(e) => setSubmission({...submission, severity: e.target.value})}
      >
        <option value="critical">Critical</option>
        <option value="high">High</option>
        <option value="medium">Medium</option>
        <option value="low">Low</option>
      </select>
      <GlassButton onClick={handleSubmit}>Submit</GlassButton>
    </GlassCard>
  );
}
```

### Leaderboard Component
```tsx
// components/bounty/Leaderboard.tsx
export function Leaderboard({ programId }) {
  const { data } = api.useQuery(['leaderboard', programId], 
    () => api.get(`/api/v1/bounty/leaderboard?program_id=${programId}`)
  );
  
  return (
    <GlassCard>
      <h3>🏆 Leaderboard</h3>
      <table>
        <thead>
          <tr>
            <th>Rank</th>
            <th>Researcher</th>
            <th>Earnings</th>
            <th>Reputation</th>
          </tr>
        </thead>
        <tbody>
          {data?.leaderboard.map((entry, idx) => (
            <tr key={entry.researcher}>
              <td>#{entry.rank}</td>
              <td>{entry.researcher}</td>
              <td>${entry.total_earnings}</td>
              <td>
                <div className="reputation-bar">
                  <div style={{width: `${entry.reputation_score}%`}} />
                </div>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    </GlassCard>
  );
}
```

## CLI Usage

```bash
# List programs
cosmicsec bounty programs list

# Submit vulnerability
cosmicsec bounty submit \
  --program prog_12345 \
  --title "SQL Injection" \
  --severity critical \
  --evidence ./poc.png

# Triage submission (admin)
cosmicsec bounty triage sub_12345 \
  --status accepted \
  --reward 5000

# View leaderboard
cosmicsec bounty leaderboard --program prog_12345

# Process payout
cosmicsec bounty payout \
  --submission sub_12345 \
  --amount 5000 \
  --method stripe
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Create program
program = client.bounty.create_program(
    name="My Company Bounty Program",
    scope={
        "in_scope": ["*.example.com"],
        "out_of_scope": ["test.example.com"]
    },
    rewards={
        "critical": 5000,
        "high": 2000,
        "medium": 500
    }
)
print(f"Program created: {program.program_id}")

# Submit vulnerability
submission = client.bounty.submit(
    program_id=program.program_id,
    title="SQL Injection in Login",
    description="The login form...",
    severity="critical",
    evidence={
        "poc_code": "' OR 1=1--",
        "screenshots": ["screenshot.png"]
    }
)
print(f"Submission ID: {submission.submission_id}")

# Get leaderboard
leaderboard = client.bounty.get_leaderboard(program.program_id)
for entry in leaderboard.leaderboard:
    print(f"#{entry.rank} {entry.researcher}: ${entry.total_earnings}")
```

## Configuration

### Environment Variables
```bash
# Service
BOUNTY_SERVICE_PORT=8007
BOUNTY_SERVICE_HOST=0.0.0.0

# Stripe Integration
STRIPE_SECRET_KEY=sk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...
STRIPE_CONNECT_CLIENT_ID=ca_...

# Payment Settings
DEFAULT_CURRENCY=USD
MIN_REWARD_AMOUNT=100
MAX_REWARD_AMOUNT=50000
PAYMENT_FEE_PERCENT=3.0

# Storage (for evidence)
MINIO_URL=http://minio:9000
EVIDENCE_BUCKET=bounty-evidence

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec

# Notifications
SMTP_HOST=smtp.gmail.com
NOTIFICATION_EMAIL=security@company.com
```

## Monitoring

### Metrics
- `bounty_programs_total` — Total active programs
- `bounty_submissions_total` — Submissions by status
- `bounty_payouts_total` — Total payout amount by currency
- `bounty_researchers_active` — Active researchers (30 days)

### Alerts
- Critical submission pending triage > 48 hours
- Payout failure (Stripe error)
- Duplicate submission spike
- Researcher dispute filed

## Legal Considerations

### Safe Harbor Provisions
Include in program rules:
```
Safe Harbor: We will not pursue legal action against 
researchers who:
1. Follow responsible disclosure guidelines
2. Test only in-scope systems
3. Do not access/modify user data
4. Do not cause service disruption
5. Report vulnerabilities promptly
```

### Privacy (GDPR)
- Minimize data collection
- Allow researcher anonymity
- Provide data export/deletion
- Store data in EU (for EU researchers)

## Troubleshooting

### Submission Not Appearing
```bash
# Check submission status
curl http://localhost:8007/api/v1/bounty/submissions/sub_12345

# Check logs
docker logs bugbounty-service | grep sub_12345
```

### Payout Failing
```bash
# Check Stripe connectivity
curl -X POST http://localhost:8007/api/v1/bounty/payouts/test

# Verify Stripe keys
docker exec bugbounty-service env | grep STRIPE

# Check researcher Stripe account
# Must be connected via Stripe Connect
```

### Duplicate Detection Issues
```bash
# Adjust similarity threshold
export DUPLICATE_SIMILARITY_THRESHOLD=0.85  # Default 0.9

# Check duplicate detection logs
docker logs bugbounty-service | grep "duplicate"
```

## Next Steps

- [DDoS Protection](./ddos-protection.md)
- [ZTNA Service](./ztna.md)
- [Threat Intel Feeds](./threat-intel-feeds.md)
- [Compliance Service](./compliance-service.md)
- [Admin Service](./admin-service.md)
