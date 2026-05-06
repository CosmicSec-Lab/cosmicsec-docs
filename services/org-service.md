# 👥 Org Service#

## Overview#

The **Org Service** provides enterprise-grade multi-tenant organization management with premium features like billing integration, subscription management, and team collaboration tools.

**Location:** `cosmicsec-services/services/org_service/`  
**Port:** 8013  
**Framework:** FastAPI#

## Premium Features#

### 1. Multi-Tenant Architecture
- **Isolated Tenants** — Complete data isolation
- **Custom Domains** — your-company.cosmicsec.com
- **Branding** — White-label with custom logos
- **SSO Integration** — Per-org SAML/OIDC
- **Data Residency** — Choose storage region#

### 2. Subscription Management
| Tier | Price/Month | Features |
|------|--------------|----------|
| **Basic** | $99 | 5 users, basic scans, email support |
| **Professional** | $499 | 25 users, AI, priority support |
| **Enterprise** | $1999 | Unlimited, premium AI, dedicated support |
| **Elite** | $4999 | Everything + on-prem, custom SLA |

### 3. Team Management
- **Departments** — Create organizational units
- **Teams** — Group users by function
- **Roles (Per-Org)** — Custom role definitions
- **Invite Flow** — Email/link invitations
- **Onboarding Automation** — Welcome emails, wizard#

### 4. Billing & Usage
- **Real-Time Metering** — Requests, scans, storage
- **Cost Allocation** — By department/team
- **Budget Alerts** — Prevent overruns
- **Invoice Generation** — PDF invoices, tax docs
- **Usage Forecasting** — Predict next month's bill#

### 5. SSO Configuration (Per-Org)
- **SAML 2.0** — Okta, Azure AD, Keycloak
- **OIDC** — Auth0, Cognito
- **LDAP/Active Directory** — Legacy enterprise
- **Social Login** — Google, Microsoft
- **Custom IdP** — Bring your own#

## API Endpoints#

### Get Organization Details
```http
GET /api/v1/org?organization_id=org_12345
```

**Response:**
```json
{
  "organization_id": "org_12345",
  "name": "Acme Corporation",
  "domain": "acme.com",
  "tier": "enterprise",
  "subscription": {
    "plan": "enterprise",
    "status": "active",
    "renews_at": "2026-06-01T00:00:00Z",
    "users_limit": 100,
    "users_current": 67
  },
  "branding": {
    "logo_url": "https://cdn.cosmicsec.com/acme/logo.png",
    "primary_color": "#1e40af",
    "custom_domain": "security.acme.com"
  },
  "data_residency": {
    "region": "eu-west-1",
    "country": "IE"
  },
  "sso": {
    "enabled": true,
    "provider": "okta",
    "domain": "acme.okta.com"
  },
  "billing": {
    "current_usage": {
      "scans": 450,
      "api_requests": 150000,
      "storage_gb": 25.5
    },
    "current_bill": 1999.00,
    "estimated_next_bill": 2100.00
  },
  "created_at": "2025-01-15T10:00:00Z"
}
```

### Update Organization
```http
PUT /api/v1/org
```

**Request:**
```json
{
  "organization_id": "org_12345",
  "name": "Acme Corp (Updated)",
  "branding": {
    "logo_url": "https://cdn.cosmicsec.com/acme/new-logo.png",
    "primary_color": "#0ea5e9"
  },
  "data_residency": {
    "region": "us-east-1"
  }
}
```

### Invite User
```http
POST /api/v1/org/invite
```

**Request:**
```json
{
  "organization_id": "org_12345",
  "email": "newuser@acme.com",
  "role": "analyst",
  "department": "security",
  "team": "soc-team",
  "personal_message": "Welcome to our security team!",
  "expires_in_days": 7
}
```

**Response:**
```json
{
  "invitation_id": "inv_12345",
  "email": "newuser@acme.com",
  "invite_link": "https://security.acme.com/accept-invite/inv_12345",
  "expires_at": "2026-05-12T22:00:00Z",
  "status": "sent"
}
```

### List Members
```http
GET /api/v1/org/members?organization_id=org_12345&role=analyst&limit=50
```

**Response:**
```json
{
  "members": [
    {
      "user_id": "user_123",
      "email": "analyst@acme.com",
      "name": "John Doe",
      "role": "analyst",
      "department": "security",
      "team": "soc-team",
      "status": "active",
      "last_active": "2026-05-05T21:30:00Z",
      "2fa_enabled": true
    }
  ],
  "total": 67,
  "limit": 50,
  "offset": 0
}
```

### Update Member Role
```http
PUT /api/v1/org/members/{user_id}/role
```

**Request:**
```json
{
  "organization_id": "org_12345",
  "role": "soc_analyst",
  "department": "security",
  "team": "incident-response"
}
```

### Remove Member
```http
DELETE /api/v1/org/members/{user_id}
```

### Get Billing Info
```http
GET /api/v1/org/billing?organization_id=org_12345&month=2026-05
```

**Response:**
```json
{
  "organization_id": "org_12345",
  "month": "2026-05",
  "plan": "enterprise",
  "base_cost": 1999.00,
  "usage_charges": {
    "extra_scans": 100.00,
    "excess_api_requests": 50.00,
    "extra_storage": 25.00
  },
  "total": 2174.00,
  "usage_summary": {
    "scans": 450,
    "api_requests": 150000,
    "storage_gb": 25.5
  },
  "invoice_url": "https://cdn.cosmicsec.com/invoices/inv_2026_05.pdf"
}
```

### Update Billing
```http
PUT /api/v1/org/billing
```

**Request:**
```json
{
  "organization_id": "org_12345",
  "payment_method_id": "pm_12345",
  "billing_email": "billing@acme.com",
  "tax_id": "EU123456789",
  "purchase_order": "PO-2026-001"
}
```

## Frontend Integration#

### Organization Settings Page
```tsx
export function OrgSettings() {
  const { data: org } = api.useQuery(['org', orgId], 
    () => api.get(`/api/v1/org?organization_id=${orgId}`)
  );
  const [form, setForm] = useState(org || {});
  
  const mutation = useMutation((data) => 
    api.put('/api/v1/org', data)
  );
  
  return (
    <GlassCard blur={20} opacity={0.9} className="max-w-4xl mx-auto">
      <h2 className="text-2xl font-bold mb-6">Organization Settings</h2>
      
      <div className="space-y-4">
        <div>
          <label>Organization Name</label>
          <input 
            value={form.name}
            onChange={(e) => setForm({...form, name: e.target.value})}
            className="glass-input w-full"
          />
        </div>
        
        <div>
          <label>Custom Domain</label>
          <input 
            value={form.branding?.custom_domain}
            onChange={(e) => setForm({
              ...form, 
              branding: {...form.branding, custom_domain: e.target.value}
            })}
            className="glass-input w-full"
          />
        </div>
        
        <GlassButton 
          variant="primary" 
          onClick={() => mutation.mutate(form)}
          loading={mutation.isLoading}
        >
          Save Changes
        </GlassButton>
      </div>
      
      {/* Subscription Card */}
      <GlassCard blur={10} opacity={0.8} className="mt-6">
        <h3>Subscription</h3>
        <div className="flex justify-between items-center">
          <div>
            <div className="text-xl font-bold">{org?.subscription?.plan?.toUpperCase()}</div>
            <div className="text-sm text-gray-400">
              {org?.subscription?.users_current} / {org?.subscription?.users_limit} users
            </div>
          </div>
          <GlassButton variant="ghost">Upgrade</GlassButton>
        </div>
      </GlassCard>
    </GlassCard>
  );
}
```

## CLI Usage#

```bash
# Get org details
cosmicsec org get --org org_12345;

# Update org
cosmicsec org update \
  --name "Acme Corp Updated" \
  --custom-domain security.acme.com;

# Invite user
cosmicsec org invite \
  --org org_12345 \
  --email newuser@acme.com \
  --role analyst \
  --department security;

# List members
cosmicsec org members \
  --org org_12345 \
  --role analyst;

# Update member role
cosmicsec org update-role \
  --org org_12345 \
  --user user_123 \
  --role soc_analyst;

# Remove member
cosmicsec org remove-member \
  --org org_12345 \
  --user user_123;

# Get billing
cosmicsec org billing \
  --org org_12345 \
  --month 2026-05
```

## Python SDK Usage#

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Get organization
org = client.org.get(organization_id="org_12345")
print(f"Organization: {org.name}")
print(f"Tier: {org.tier}")
print(f"Users: {org.subscription['users_current']}/{org.subscription['users_limit']}")

# Update organization
client.org.update(
    organization_id="org_12345",
    name="Acme Corp (Updated)",
    branding={
        "logo_url": "https://.../new-logo.png",
        "primary_color": "#0ea5e9"
    }
)
print("Organization updated!")

# Invite user
invite = client.org.invite(
    organization_id="org_12345",
    email="newuser@acme.com",
    role="analyst",
    department="security"
)
print(f"Invitation sent: {invite.invite_link}")

# List members
members = client.org.list_members(
    organization_id="org_12345",
    limit=50
)
print(f"\nTeam Members ({members.total}):")
for member in members.members:
    status = "✅" if member.status == 'active' else "❌"
    print(f"{status} {member.email} - {member.role}")

# Get billing
billing = client.org.get_billing(
    organization_id="org_12345",
    month="2026-05"
)
print(f"\nBilling for {billing.month}:")
print(f"Plan: {billing.plan}")
print(f"Total: ${billing.total}")
print(f"Invoice: {billing.invoice_url}")
```

## Configuration#

### Environment Variables
```bash
# Service
ORG_SERVICE_PORT=8013
ORG_SERVICE_HOST=0.0.0.0

# Subscription
DEFAULT_TIER=basic
ENABLED_TIERS=basic,professional,enterprise,elite
TRIAL_DAYS=14

# Billing (Stripe)
STRIPE_SECRET_KEY=sk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...

# SSO
SAML_ENABLED=true
OIDC_ENABLED=true
LDAP_ENABLED=false

# Data Residency
DEFAULT_REGION=us-east-1
ENABLED_REGIONS=us-east-1,eu-west-1,ap-southeast-1

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec
```

## Next Steps#

- [Admin Service](./admin-service.md)
- [Notification Service](./notification-service.md)
- [Egress Service](./egress-service.md)
- [Billing Guide](../guides/billing.md)
- [SSO Configuration](../guides/sso-config.md)
