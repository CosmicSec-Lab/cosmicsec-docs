# REST API Reference

The CosmicSec REST API provides a programmatic interface for managing the ecosystem. All APIs are served via the API Gateway.

## 🔑 Authentication

All requests must include a valid JWT in the `Authorization` header:

```http
Authorization: Bearer <your-jwt-token>
```

Tokens can be obtained via the `auth_service`.

## 📍 Base URL

```text
https://api.cosmicsec.local/v1
```

## 📦 Core Endpoints

### Scans
Manage and monitor vulnerability scans.

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/scans` | List all scans with pagination. |
| `POST` | `/scans` | Create and launch a new scan. |
| `GET` | `/scans/{id}` | Get detailed status and results for a scan. |
| `DELETE` | `/scans/{id}` | Cancel and delete a scan. |

### AI Helix
Interact with the autonomous AI engine.

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `POST` | `/ai/analyze` | Submit a target for autonomous analysis. |
| `GET` | `/ai/reports/{id}` | Retrieve an AI-generated security report. |
| `POST` | `/ai/remediate` | Apply an AI-suggested remediation patch. |

### SOC & Alerts
Manage security events and alerts.

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/alerts` | Retrieve recent SOC alerts. |
| `PATCH` | `/alerts/{id}` | Update alert status (e.g., acknowledged, resolved). |
| `GET` | `/soc/stats` | Get high-level security posture statistics. |

## 📐 Data Formats

The API uses standard JSON for all requests and responses. Dates are formatted according to ISO 8601.

### Example Response (Scan Detail)
```json
{
  "id": "scan-12345",
  "target": "https://example.com",
  "status": "completed",
  "created_at": "2024-05-12T10:00:00Z",
  "findings_count": 12,
  "risk_score": 7.5
}
```

## 🚥 Rate Limiting

The API Gateway enforces rate limits to ensure platform stability:
*   **Default**: 100 requests per minute per user.
*   **Enterprise**: Up to 2000 requests per minute.

## 🛠️ Interactive Documentation

Live, interactive API documentation (Swagger/ReDoc) is available at:
*   **Swagger UI**: `https://api.cosmicsec.local/docs`
*   **ReDoc**: `https://api.cosmicsec.local/redoc`
