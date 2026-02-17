# API Reference

REST API for Supp. All endpoints are served from `api.supp.support`.

## Authentication

| Method | Usage |
|--------|-------|
| API Key (`X-API-Key: pk_live_...`) | Widget and public endpoints |
| API Key (`X-API-Key: sk_live_...`) | Server-side and admin endpoints |
| Bearer Token (`Authorization: Bearer <JWT>`) | Dashboard and authenticated endpoints |

## Widget Endpoints

### POST `/api/widget` — Submit Message

Classify a customer message and execute routing rules.

```json
// Request
{
  "api_key": "pk_live_...",
  "message": "I can't log into my account",
  "session_id": "existing-session-id",     // optional, for continuing a conversation
  "customer_email": "user@example.com"      // optional
}

// Response
{
  "session_id": "sess_abc123",
  "session_token": "...",
  "conversation_id": "conv_xyz",
  "response": "To reset your password, visit...",
  "intent": "password_reset",
  "confidence": 0.89,
  "action": "template",
  "cost": 0.20,
  "new_balance": 4.80
}
```

### GET `/api/widget/config` — Widget Configuration

```
GET /api/widget/config?api_key=pk_live_...
```

```json
{
  "require_email": true,
  "feedback_enabled": true,
  "charge_window_minutes": 30
}
```

### POST `/api/widget/status` — Agent Availability

```json
// Request
{ "api_key": "pk_live_..." }

// Response
{
  "human_status": "online",
  "offline_message": "We'll get back to you within 24 hours",
  "show_human_button": true,
  "show_powered_by": true
}
```

### POST `/api/widget/confirm` — Confirm Deferred Charge

```json
// Request
{
  "api_key": "pk_live_...",
  "charge_token": "...",
  "accepted": true
}

// Response
{ "charged": true, "cost": 0.20, "new_balance": 4.80 }
```

### POST `/api/widget/fields` — Submit Collected Fields

```json
// Request
{
  "api_key": "pk_live_...",
  "conversation_id": "conv_xyz",
  "fields": {
    "email": "user@example.com",
    "steps_to_reproduce": "1. Open settings\n2. Click profile\n3. Crash"
  }
}

// Response
{ "success": true }
```

### POST `/api/widget/feedback` — Classification Feedback

```json
{
  "api_key": "pk_live_...",
  "session_id": "sess_abc123",
  "accepted": false,
  "message_text": "I want to cancel my subscription",
  "intent": "subscription_downgrade",
  "confidence": 0.78
}
```

### POST `/api/widget/end` — End Conversation

```json
{
  "api_key": "pk_live_...",
  "session_token": "..."
}
```

### POST `/api/widget/history` — Load History

```json
// Request
{
  "api_key": "pk_live_...",
  "session_token": "..."
}

// Response
{
  "conversation_id": "conv_xyz",
  "messages": [
    { "sender_type": "customer", "body": "I can't log in", "created_at": "..." },
    { "sender_type": "ai", "body": "To reset your password...", "created_at": "..." }
  ]
}
```

## Classification Endpoints

### POST `/api/ai/classify` — Classify Message

```json
// Request
{
  "message": "The API returns 500 errors intermittently",
  "include_priority": true,
  "is_batch": false
}

// Response
{
  "intent": "api_troubleshooting",
  "confidence": 0.88,
  "all_intents": [
    {"intent": "api_troubleshooting", "confidence": 0.88},
    {"intent": "performance_issue", "confidence": 0.72}
  ],
  "priority": "high",
  "action_type": "mcp_action",
  "suggested_response": "We've created a ticket...",
  "cost": 0.33
}
```

### GET `/api/ai/intents` — List Intents

```
GET /api/ai/intents?category=technical_support
```

```json
{
  "data": {
    "intents": [
      { "intent": "bug_report", "display_label": "Bug Report", "category": "technical_support" }
    ],
    "total": 47
  }
}
```

### POST `/api/ai/priority` — Priority Scoring

```json
// Request
{ "message": "Complete outage affecting all users" }

// Response
{ "priority": "critical", "cost": 0.03 }
```

## Routing Endpoints

### GET `/api/routing` — List Rules

Returns all routing rules for your organization.

### POST `/api/routing` — Create/Update Rule

```json
{
  "intent": "bug_report",
  "action_type": "mcp_action",
  "action_config": {
    "integration_provider": "github",
    "github_owner": "acme",
    "github_repo": "app",
    "github_labels": ["bug"]
  },
  "confidence_threshold": 0.80,
  "priority_filter": ["high", "critical"]
}
```

Upserts: updates if a rule exists for the intent, creates otherwise.

### POST `/api/routing/batch` — Bulk Create/Update

Apply the same config to multiple intents (up to 500 per request).

### POST `/api/routing/test` — Simulate Trigger

Test if a rule would fire for sample text. Non-destructive.

### GET/PATCH/DELETE `/api/routing/[id]` — Single Rule CRUD

## Conversation Endpoints

### GET `/api/conversations` — List Conversations

```
GET /api/conversations?status=open&priority=high&page=1&limit=20
```

### POST `/api/conversations` — Create Conversation

### GET `/api/conversations/[id]` — Get Conversation

Full conversation with messages and customer info.

### PATCH `/api/conversations/[id]` — Update Status

```json
{ "status": "resolved" }
```

Valid statuses: `open`, `pending`, `resolved`, `escalated`.

### Messages

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/conversations/[id]/messages` | List messages |
| POST | `/api/conversations/[id]/messages` | Add message |
| PATCH | `/api/conversations/[id]/messages` | Update draft |
| DELETE | `/api/conversations/[id]/messages` | Discard draft |

## Integration Endpoints

### GET `/api/integrations` — List Integrations

### POST `/api/integrations` — Connect Integration

```json
{
  "provider": "github",
  "name": "GitHub - acme/app",
  "config": { "access_token": "..." },
  "test": true  // test without saving
}
```

### DELETE `/api/integrations` — Disconnect

### POST `/api/integrations/connect` — Get OAuth URL

```json
// Request
{ "service": "github" }

// Response
{ "auth_url": "https://github.com/login/oauth/authorize?...", "oauth": true }
```

### POST `/api/integrations/verify` — Check Token Validity

## Analytics Endpoints

### GET `/api/analytics` — Summary

```
GET /api/analytics?period=30d
```

```json
{
  "data": {
    "summary": {
      "total_conversations": 1420,
      "resolved": 1180,
      "escalated": 240,
      "avg_confidence": 0.82,
      "total_cost": 342.60
    },
    "intent_distribution": [
      { "intent": "bug_report", "count": 312, "percentage": 22.0 }
    ]
  }
}
```

### GET `/api/analytics/triggers` — Trigger Performance

### POST `/api/analytics/export` — Export as CSV or JSON

## API Key Management

### POST `/api/apikeys` — Create Key

Returns the raw key once (never retrievable again).

### GET `/api/apikeys` — List Keys

### DELETE `/api/apikeys/[id]` — Revoke Key

## Error Responses

```json
{ "error": "Human-readable error message" }
```

| Status | Meaning |
|--------|---------|
| 400 | Bad request (validation error) |
| 401 | Unauthorized (missing or invalid auth) |
| 402 | Payment required (insufficient balance) |
| 403 | Forbidden (insufficient permissions) |
| 404 | Not found |
| 429 | Rate limited or spend cap exceeded |
| 500 | Internal server error |
