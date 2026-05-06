# 🎨 Theme Builder Service#

## Overview#

The **Theme Builder Service** provides a premium, real-time theme customization platform with 4 preset themes, unlimited custom themes, and instant preview.

**Location:** `cosmicsec-services/services/theme_builder/`  
**Port:** 8029  
**Framework:** FastAPI + WebSockets

## Premium Features#

### 1. Real-Time Theme Editor
- **Instant Preview** — See changes immediately (50ms latency)
- **Live Glassmorphism** — Adjust blur, opacity, borders live
- **Color Pickers** — HSL, RGB, HEX, with harmony suggestions
- **Gradient Editor** — Multi-stop gradients with angle control
- **Typography Preview** — Test fonts before applying
- **Component Gallery** — Preview all 20+ components

### 2. Glassmorphism Controls
| Control | Range | Default | Description |
|---------|-------|---------|-------------|
| **Blur** | 0-30px | 15px | Backdrop blur intensity |
| **Opacity** | 0.0-1.0 | 0.8 | Background opacity |
| **Border Opacity** | 0.0-1.0 | 0.15 | Border transparency |
| **Border Radius** | 0-20px | 12px | Corner roundness |
| **Shadow Intensity** | 0-50px | 20px | Box-shadow spread |

### 3. Preset Themes (4 Built-In)
1. **Cosmic Dark** — Purple/indigo gradient, professional
2. **Midnight Blue** — Navy/teal, calming
3. **Neon Nights** — Magenta/cyan, vibrant
4. **Emerald Forest** — Green emerald, nature-inspired

### 4. Advanced Color Harmony
- **Complementary** — Opposite on color wheel
- **Analogous** — Adjacent colors (3 colors)
- **Triadic** — Equilateral triangle (3 colors)
- **Split-Complementary** — Base + 2 adjacent opposites
- **Tetradic** — Rectangle (4 colors)

### 5. Export/Import Themes
- **JSON Format** — Share themes as JSON files
- **URL Sharing** — Generate shareable theme URLs
- **Theme Marketplace** — Community theme sharing
- **Version Control** — Track theme evolution
- **Fork Themes** — Create variants from existing

### 6. WCAG 2.1 AA Compliance Checker
- **Contrast Ratio** — 4.5:1 minimum for normal text
- **Color Blindness Simulation** — Protanopia, deuteranopia, tritanopia
- **Keyboard Navigation** — Tab order verification
- **Screen Reader Preview** — ARIA label checking

## API Endpoints#

### List Preset Themes
```http
GET /api/v1/themes/presets
```

**Response:**
```json
{
  "presets": [
    {
      "id": "cosmic-dark",
      "name": "Cosmic Dark",
      "description": "Professional purple/indigo theme",
      "preview_image": "https://cdn.cosmicsec.com/themes/cosmic-dark.png",
      "background": "linear-gradient(135deg, #0f0c29, #302b63, #24243e)",
      "glass": {
        "background": "rgba(255, 255, 255, 0.08)",
        "blur": 15,
        "border": "1px solid rgba(255, 255, 255, 0.15)"
      },
      "colors": {
        "primary": "#6366f1",
        "secondary": "#8b5cf6",
        "accent": "#06b6d4",
        "success": "#10b981",
        "warning": "#f59e0b",
        "danger": "#ef4444"
      }
    }
  ]
}
```

### Create Custom Theme
```http
POST /api/v1/themes
```

**Request:**
```json
{
  "name": "My Corporate Theme",
  "description": "Company branded theme",
  "background": "linear-gradient(135deg, #1e3a5f, #2d4a6f, #3e5b7f)",
  "glass": {
    "background": "rgba(255, 255, 255, 0.1)",
    "blur": 20,
    "border": "1px solid rgba(100, 255, 218, 0.2)"
  },
  "colors": {
    "primary": "#64ffda",
    "secondary": "#1e40af",
    "accent": "#06b6d4",
    "success": "#10b981",
    "warning": "#f59e0b",
    "danger": "#ef4444",
    "text_primary": "rgba(255, 255, 255, 0.95)",
    "text_secondary": "rgba(255, 255, 255, 0.7)"
  },
  "typography": {
    "font_family": "Inter, sans-serif",
    "heading_weight": "700",
    "body_weight": "400"
  },
  "animations": {
    "duration_ms": 300,
    "easing": "ease-in-out",
    "enabled": true
  },
  "wcag_compliant": true
}
```

**Response:**
```json
{
  "theme_id": "theme_12345",
  "name": "My Corporate Theme",
  "status": "created",
  "wcag_score": 98,  // 0-100 compliance score
  "shareable_url": "https://cosmicsec.com/theme/theme_12345",
  "created_at": "2026-05-05T22:00:00Z"
}
```

### List Custom Themes
```http
GET /api/v1/themes?organization_id=org_12345&limit=50
```

### Get Theme Details
```http
GET /api/v1/themes/{theme_id}
```

### Update Theme
```http
PUT /api/v1/themes/{theme_id}
```

### Delete Theme
```http
DELETE /api/v1/themes/{theme_id}
```

### Apply Theme
```http
POST /api/v1/themes/{theme_id}/apply
```

**Request:**
```json
{
  "organization_id": "org_12345",
  "apply_globally": true,  // Apply to all users
  "notify_users": true
}
```

### Import Theme
```http
POST /api/v1/themes/import
```

**Request:**
```json
{
  "theme_json": "{ \"name\": \"Imported Theme\", ... }",
  "organization_id": "org_12345"
}
```

### Export Theme
```http
GET /api/v1/themes/{theme_id}/export
```

**Response:**
```json
{
  "theme_id": "theme_12345",
  "name": "My Corporate Theme",
  "exported_at": "2026-05-05T22:00:00Z",
  "json": {
    "name": "My Corporate Theme",
    "background": "linear-gradient(...)",
    ...
  }
}
```

## Theme Preview WebSocket#

### Connect to Live Preview
```javascript
const ws = new WebSocket('ws://localhost:8029/api/v1/themes/ws/preview');

// Send theme updates
ws.send(JSON.stringify({
  type: 'update',
  theme: {
    glass: { blur: 20, opacity: 0.9 },
    colors: { primary: '#ff0000' }
  }
}));

// Receive preview confirmation
ws.onmessage = (event) => {
  const response = JSON.parse(event.data);
  if (response.type === 'preview_ready') {
    console.log('Preview updated!');
  }
};
```

## WCAG Compliance Checker#

### Contrast Ratio Calculation
```python
def calculate_contrast_ratio(color1, color2):
    # Convert to luminance
    l1 = relative_luminance(color1)
    l2 = relative_luminance(color2)
    
    # Contrast ratio
    lighter = max(l1, l2)
    darker = min(l1, l2)
    return (lighter + 0.05) / (darker + 0.05)

# Example:
# Primary color: #6366f1 (Indigo)
# Background: rgba(255, 255, 255, 0.08)
# Contrast ratio: 4.8:1 (Passes AA for large text)
```

### Color Blindness Simulation
```typescript
// Simulate deuteranopia (red-green color blindness)
function simulateDeuteranopia(color: string): string {
  const rgb = hexToRgb(color);
  // Transform matrix for deuteranopia
  const matrix = [
    0.625, 0.375, 0,
    0.7, 0.3, 0,
    0, 0.3, 0.7
  ];
  return applyColorMatrix(rgb, matrix);
}
```

## CLI Usage#

```bash
# List preset themes
cosmicsec theme presets;

# Create custom theme
cosmicsec theme create \
  --name "My Theme" \
  --background "linear-gradient(135deg, #000, #fff)" \
  --glass-blur 20 \
  --primary-color "#ff0000";

# List custom themes
cosmicsec theme list --org org_12345;

# Apply theme
cosmicsec theme apply theme_12345 \
  --org org_12345 \
  --global;

# Export theme
cosmicsec theme export theme_12345 --output my-theme.json;

# Import theme
cosmicsec theme import \
  --file my-theme.json \
  --org org_12345;

# Check WCAG compliance
cosmicsec theme wcag-check theme_12345;
```

## Python SDK Usage#

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# List preset themes
presets = client.theme.get_presets()
print("Preset themes:")
for theme in presets.presets:
    print(f"  {theme.name}: {theme.description}")

# Create custom theme
theme = client.theme.create(
    name="My Corporate Theme",
    background="linear-gradient(135deg, #1e3a5f, #2d4a6f)",
    glass={
        "background": "rgba(255, 255, 255, 0.1)",
        "blur": 20,
        "border": "1px solid rgba(100, 255, 218, 0.2)"
    },
    colors={
        "primary": "#64ffda",
        "secondary": "#1e40af"
    },
    organization_id="org_12345"
)
print(f"Theme created: {theme.theme_id}")
print(f"WCAG score: {theme.wcag_score}/100")

# Apply theme
client.theme.apply(
    theme_id=theme.theme_id,
    organization_id="org_12345",
    apply_globally=True
)
print("Theme applied globally!")

# Export theme
exported = client.theme.export(theme.theme_id)
with open('my-theme.json', 'w') as f:
    json.dump(exported.json, f, indent=2)
print("Theme exported to my-theme.json")

# Import theme
imported = client.theme.import_(
    theme_json=json.dumps(exported.json),
    organization_id="org_12345"
)
print(f"Theme imported: {imported.theme_id}")
```

## Theme Structure (JSON Schema)#

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "name": { "type": "string" },
    "description": { "type": "string" },
    "background": { "type": "string" },  // CSS gradient
    "glass": {
      "type": "object",
      "properties": {
        "background": { "type": "string" },  // rgba()
        "blur": { "type": "number", "minimum": 0, "maximum": 30 },
        "border": { "type": "string" }  // CSS border
      }
    },
    "colors": {
      "type": "object",
      "properties": {
        "primary": { "type": "string" },
        "secondary": { "type": "string" },
        "accent": { "type": "string" },
        "success": { "type": "string" },
        "warning": { "type": "string" },
        "danger": { "type": "string" }
      }
    },
    "typography": { /* ... */ },
    "animations": { /* ... */ }
  },
  "required": ["name", "background", "colors"]
}
```

## Configuration#

### Environment Variables
```bash
# Service
THEME_BUILDER_PORT=8029
THEME_BUILDER_HOST=0.0.0.0

# Theme Storage
THEME_STORE_DIR=/app/themes
MAX_CUSTOM_THEMES=100
ALLOW_USER_UPLOADS=true

# WCAG Checker
WCAG_CHECKER_ENABLED=true
MIN_CONTRAST_RATIO=4.5  # AA standard
SIMULATE_COLORBLINDNESS=true

# Marketplace (Future)
THEME_MARKETPLACE_ENABLED=false
MARKETPLACE_URL=https://marketplace.cosmicsec.com

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec
```

## Troubleshooting#

### Theme Not Applying
```bash
# Check theme exists
curl http://localhost:8029/api/v1/themes/theme_12345;

# Verify organization
curl http://localhost:8001/api/v1/settings/general | grep theme;

# Force re-apply
curl -X POST http://localhost:8029/api/v1/themes/theme_12345/apply \
  -d '{"organization_id": "org_12345", "apply_globally": true}'
```

### WCAG Compliance Failing
```bash
# Check contrast ratios
curl http://localhost:8029/api/v1/themes/theme_12345/wcag-check;

# Adjust colors
# Increase contrast by darkening text or lightening background
# Target: 4.5:1 for normal text, 3:1 for large text
```

### Import Failing
```bash
# Validate JSON
cat my-theme.json | jq .

# Check required fields
jq '.name, .background, .colors' my-theme.json;

# Test import locally
curl -X POST http://localhost:8029/api/v1/themes/import \
  -H "Content-Type: application/json" \
  -d @my-theme.json
```

## Next Steps#

- [Onboarding Wizard](./onboarding-wizard.md)
- [NLP Search](./nlp-search.md)
- [Notification Service](./notification-service.md)
- [Frontend Theme Builder](../frontend/theme-builder.md)
- [Glassmorphism Design System](../frontend/design-system.md)
