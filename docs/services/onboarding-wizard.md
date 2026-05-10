# 🚀 Onboarding Wizard Service

## Overview

The **Onboarding Wizard** provides a premium, step-by-step guided experience for new organizations. Features 7 interactive steps, real-time validation, and automated setup.

**Location:** `cosmicsec-services/services/onboarding_wizard/`  
**Port:** 8030  
**Framework:** FastAPI + WebSocket

## Premium Features

### 1. 7-Step Interactive Wizard
1. **Welcome** — Premium introduction animation
2. **Organization Setup** — Company details, industry, size
3. **First Scan** — Guided scan creation with templates
4. **AI Configuration** — Connect AI providers (OpenAI, Anthropic, Ollama)
5. **Recon & Threat Intel** — Configure dark web monitoring
6. **Team Invitation** — Invite SOC team members
7. **Complete** — Summary, next steps, resources

### 2. Real-Time Validation
- **Live API Testing** — Test connections instantly
- **Credential Validation** — Verify API keys work
- **DNS Checks** — Verify domain ownership
- **Connectivity Tests** — Ensure all services reachable

### 3. Smart Defaults
- **Industry-Based** — Preset configs for Finance, Healthcare, Retail
- **Org Size Scaling** — Auto-adjust resources
- **Compliance Templates** — GDPR, HIPAA, PCI-DSS presets
- **Geographic Optimization** — Auto-select nearest data center

### 4. Progress Tracking
- **Visual Progress Bar** — Animated step indicator
- **Time Estimates** — "Step 3 of 7 — ~2 minutes remaining"
- **Resume Capability** — Stop and continue later
- **Multi-Device Sync** — Start on desktop, finish on mobile

### 5. Interactive Tutorials
- **Embedded Videos** — 30-60 second explainers
- **Interactive Demos** — Try features in sandbox
- **Tooltips & Hints** — Context-sensitive help
- **Keyboard Shortcuts** — Power user features

## API Endpoints

### Get Onboarding Steps
```http
GET /api/v1/onboarding/steps
```

**Response:**
```json
{
  "steps": [
    {
      "step_id": "welcome",
      "order": 1,
      "title": "Welcome to CosmicSec",
      "description": "Your premium security platform",
      "status": "completed",
      "estimated_time_seconds": 30
    },
    {
      "step_id": "organization",
      "order": 2,
      "title": "Organization Setup",
      "description": "Configure your organization details",
      "status": "in_progress",
      "estimated_time_seconds": 120
    },
    {
      "step_id": "first_scan",
      "order": 3,
      "title": "Run Your First Scan",
      "description": "Discover vulnerabilities in your assets",
      "status": "pending",
      "estimated_time_seconds": 300
    }
  ],
  "total_steps": 7,
  "completed_steps": 1,
  "remaining_steps": 6,
  "estimated_time_remaining_seconds": 1200
}
```

### Complete Step
```http
POST /api/v1/onboarding/step/{step_id}/complete
```

**Request:**
```json
{
  "step_id": "organization",
  "data": {
    "org_name": "Acme Corp",
    "industry": "technology",
    "size": "100-500",
    "country": "US",
    "compliance_requirements": ["gdpr", "soc2"]
  }
}
```

**Response:**
```json
{
  "step_id": "organization",
  "status": "completed",
  "completed_at": "2026-05-05T22:05:00Z",
  "next_step": "first_scan",
  "validation_errors": []
}
```

### Get Onboarding Status
```http
GET /api/v1/onboarding/status?organization_id=org_12345
```

**Response:**
```json
{
  "organization_id": "org_12345",
  "status": "in_progress",
  "current_step": "first_scan",
  "started_at": "2026-05-05T22:00:00Z",
  "completed_steps": 2,
  "total_steps": 7,
  "progress_percentage": 28.57,
  "estimated_completion": "2026-05-05T22:20:00Z"
}
```

### Skip Step
```http
POST /api/v1/onboarding/skip
```

**Request:**
```json
{
  "step_id": "recon",
  "reason": "Not needed for now"
}
```

### Reset Onboarding
```http
POST /api/v1/onboarding/reset
```

## Frontend Integration

### Wizard Component (React)
```tsx
import { useState, useEffect } from 'react';
import { GlassCard, GlassButton } from '@/components/ui/GlassCard';
import { ProgressBar } from '@/components/onboarding/ProgressBar';

export function OnboardingWizard() {
  const [steps, setSteps] = useState([]);
  const [currentStep, setCurrentStep] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    loadSteps();
  }, []);
  
  const loadSteps = async () => {
    const data = await api.get('/api/v1/onboarding/steps');
    setSteps(data.steps);
    const current = data.steps.find(s => s.status === 'in_progress');
    setCurrentStep(current);
    setLoading(false);
  };
  
  const completeStep = async (stepId: string, data: any) => {
    await api.post(`/api/v1/onboarding/step/${stepId}/complete`, { data });
    await loadSteps(); // Reload steps
  };
  
  if (loading) return <GlassCard>Loading wizard...</GlassCard>;
  
  return (
    <div className="max-w-4xl mx-auto p-6">
      <GlassCard blur={20} opacity={0.9}>
        <h1 className="text-3xl font-bold mb-4">🚀 Onboarding Wizard</h1>
        
        {/* Progress Bar */}
        <ProgressBar steps={steps} currentStep={currentStep} />
        
        {/* Current Step Content */}
        <div className="mt-8">
          {currentStep?.step_id === 'welcome' && (
            <WelcomeStep onComplete={(d) => completeStep('welcome', d)} />
          )}
          {currentStep?.step_id === 'organization' && (
            <OrganizationStep onComplete={(d) => completeStep('organization', d)} />
          )}
          {currentStep?.step_id === 'first_scan' && (
            <FirstScanStep onComplete={(d) => completeStep('first_scan', d)} />
          )}
          {/* ... other steps */}
        </div>
        
        {/* Navigation */}
        <div className="flex justify-between mt-6">
          <GlassButton
            variant="ghost"
            onClick={() => skipStep(currentStep?.step_id)}
          >
            Skip for now
          </GlassButton>
          <div className="text-sm text-gray-400">
            Step {currentStep?.order} of {steps.length}
          </div>
        </div>
      </GlassCard>
    </div>
  );
}

function ProgressBar({ steps, currentStep }) {
  const progress = (steps.filter(s => s.status === 'completed').length / steps.length) * 100;
  
  return (
    <div className="w-full">
      <div className="flex justify-between mb-2">
        {steps.map(step => (
          <div
            key={step.step_id}
            className={`w-8 h-8 rounded-full flex items-center justify-center ${
              step.status === 'completed' ? 'bg-green-500' :
              step.status === 'in_progress' ? 'bg-blue-500' : 'bg-gray-600'
            }`}
          >
            {step.status === 'completed' ? '✓' : step.order}
          </div>
        ))}
      </div>
      <div className="w-full bg-gray-700 rounded-full h-2">
        <div
          className="bg-gradient-to-r from-indigo-500 to-cyan-500 h-2 rounded-full transition-all duration-500"
          style={{ width: `${progress}%` }}
        />
      </div>
    </div>
  );
}
```

### Welcome Step
```tsx
function WelcomeStep({ onComplete }) {
  return (
    <div className="text-center">
      <div className="mb-6">
        <div className="text-6xl mb-4">🌌</div>
        <h2 className="text-2xl font-semibold mb-2">Welcome to CosmicSec</h2>
        <p className="text-gray-400 max-w-2xl mx-auto">
          The world's most advanced, enterprise-grade, AI-powered, quantum-ready,
          zero-trust, dark-web monitoring, glassmorphism-designed, premium security platform.
        </p>
      </div>
      
      <div className="grid grid-cols-3 gap-4 mb-6">
        {[
          { icon: '🤖', title: 'AI-Powered', desc: '25+ microservices, 30+ AI endpoints' },
          { icon: '🔐', title: 'Zero-Trust', desc: 'ZTNA, mTLS, device posture' },
          { icon: '🌐', title: 'Dark Web Intel', desc: '17+ networks monitored' }
        ].map(feature => (
          <GlassCard key={feature.title} blur={10} opacity={0.7} className="p-4">
            <div className="text-3xl mb-2">{feature.icon}</div>
            <h3 className="font-semibold">{feature.title}</h3>
            <p className="text-sm text-gray-400">{feature.desc}</p>
          </GlassCard>
        ))}
      </div>
      
      <GlassButton
        variant="primary"
        size="lg"
        onClick={() => onComplete({})}
      >
        Get Started →
      </GlassButton>
    </div>
  );
}
```

### Organization Setup Step
```tsx
function OrganizationStep({ onComplete }) {
  const [form, setForm] = useState({
    org_name: '',
    industry: 'technology',
    size: '100-500',
    country: 'US',
    compliance_requirements: []
  });
  
  const industries = [
    { value: 'technology', label: 'Technology' },
    { value: 'finance', label: 'Finance' },
    { value: 'healthcare', label: 'Healthcare' },
    { value: 'retail', label: 'Retail' }
  ];
  
  const complianceOptions = [
    { value: 'gdpr', label: 'GDPR' },
    { value: 'hipaa', label: 'HIPAA' },
    { value: 'pci-dss', label: 'PCI-DSS' },
    { value: 'soc2', label: 'SOC 2' }
  ];
  
  return (
    <div>
      <h2 className="text-2xl font-semibold mb-4">Organization Setup</h2>
      <div className="space-y-4 max-w-md">
        <div>
          <label>Organization Name</label>
          <input
            className="w-full p-2 rounded glass-input"
            value={form.org_name}
            onChange={(e) => setForm({...form, org_name: e.target.value})}
            placeholder="Acme Corp"
          />
        </div>
        
        <div>
          <label>Industry</label>
          <select
            value={form.industry}
            onChange={(e) => setForm({...form, industry: e.target.value})}
          >
            {industries.map(ind => (
              <option key={ind.value} value={ind.value}>{ind.label}</option>
            ))}
          </select>
        </div>
        
        <GlassButton
          variant="primary"
          onClick={() => onComplete(form)}
          disabled={!form.org_name}
        >
          Continue →
        </GlassButton>
      </div>
    </div>
  );
}
```

## CLI Usage

```bash
# Check onboarding status
cosmicsec onboarding status --org org_12345

# Complete step (programmatic)
cosmicsec onboarding complete welcome \
  --data '{}'

cosmicsec onboarding complete organization \
  --name "Acme Corp" \
  --industry technology \
  --size "100-500"

# Skip step
cosmicsec onboarding skip recon --reason "Not needed"

# Reset onboarding
cosmicsec onboarding reset --org org_12345
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Get steps
steps = client.onboarding.get_steps()
print(f"Total steps: {len(steps.steps)}")
print(f"Completed: {steps.completed_steps}/{steps.total_steps}")

# Complete welcome step
result = client.onboarding.complete_step(
    step_id="welcome",
    data={}
)
print(f"Welcome completed: {result.status}")

# Setup organization
result = client.onboarding.complete_step(
    step_id="organization",
    data={
        "org_name": "Acme Corp",
        "industry": "technology",
        "size": "100-500",
        "compliance_requirements": ["gdpr", "soc2"]
    }
)
print(f"Organization setup: {result.status}")

# Check status
status = client.onboarding.get_status(organization_id="org_12345")
print(f"Progress: {status.progress_percentage:.1f}%")
print(f"Current step: {status.current_step}")
```

## Configuration

### Environment Variables
```bash
# Service
ONBOARDING_WIZARD_PORT=8030
ONBOARDING_WIZARD_HOST=0.0.0.0

# Steps Configuration
TOTAL_STEPS=7
ENABLE_SKIP=true
ALLOW_RESUME=true

# Industry Presets
PRESET_FINANCE_ENABLED=true
PRESET_HEALTHCARE_ENABLED=true
PRESET_RETAIL_ENABLED=true

# Validation
VALIDATE_API_KEYS=true
VALIDATE_DOMAINS=true
VALIDATE_CONNECTIVITY=true

# Tutorials
TUTORIAL_VIDEOS_ENABLED=true
INTERACTIVE_DEMOS_ENABLED=true

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec
```

## Next Steps

- [Theme Builder](./theme-builder.md)
- [NLP Search](./nlp-search.md)
- [Notification Service](./notification-service.md)
- [Compliance Service](./compliance-service.md)
- [Onboarding Guide](../guides/onboarding.md)
