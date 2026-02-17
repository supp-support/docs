# Routing & Automation

Routing rules determine what happens when a message is classified. Each rule maps an intent to an action — from sending a template response to creating a GitHub issue to executing a multi-step pipeline.

## How Routing Works

```
Message classified as "bug_report" (confidence: 0.89)
    │
    ▼
Look up routing rule for "bug_report"
    │
    ├─ Confidence above threshold?  ✓
    ├─ Priority filter matches?  ✓
    │
    ▼
Execute action(s)
```

## Action Types

### Template Response

Send a pre-written response with variable interpolation:

```json
{
  "intent": "password_reset",
  "action_type": "template",
  "action_config": {
    "response_template": "Hi! To reset your password, visit {{app_url}}/reset. Your request has been classified as {{intent}} with {{confidence}}% confidence."
  }
}
```

**Available template variables:**

| Variable | Value |
|----------|-------|
| `{{intent}}` | Classified intent slug |
| `{{confidence}}` | Confidence percentage (e.g., "89") |
| `{{message}}` | Original customer message |
| `{{channel}}` | Channel type (chat, email, slack, api) |
| `{{priority}}` | Priority bucket (low, medium, high, critical) |
| `{{app_url}}` | Your application URL |

### Integration Action

Execute an action on a connected integration:

```json
{
  "intent": "bug_report",
  "action_type": "mcp_action",
  "action_config": {
    "integration_provider": "github",
    "github_owner": "your-org",
    "github_repo": "your-app",
    "github_labels": ["bug", "from-supp"]
  }
}
```

### Escalate to Human

Route the conversation to a human agent:

```json
{
  "intent": "complaint_about_employee",
  "action_type": "escalate"
}
```

### None (Classify Only)

Classify the message but take no action. Useful for analytics:

```json
{
  "intent": "humor_sarcasm_irony",
  "action_type": "none"
}
```

## Multi-Action Pipelines

Chain multiple actions together for complex workflows:

```json
{
  "intent": "critical_bug_report",
  "action_type": "mcp_action",
  "action_config": {
    "actions": [
      {
        "id": "1",
        "type": "mcp_action",
        "provider": "github",
        "config": {
          "github_owner": "your-org",
          "github_repo": "your-app",
          "github_labels": ["bug", "critical"]
        }
      },
      {
        "id": "2",
        "type": "mcp_action",
        "provider": "slack",
        "config": {
          "channel": "#engineering-alerts"
        }
      },
      {
        "id": "3",
        "type": "template",
        "responseTemplate": "We've created a ticket and notified our engineering team. We'll follow up within 24 hours."
      }
    ]
  }
}
```

**Pipeline rules:**
- Actions execute sequentially in the order defined
- `escalate` is terminal — nothing after it runs
- Maximum 10 actions per pipeline

## Supported Providers

| Provider | Action |
|----------|--------|
| **GitHub** | Create issues with labels and assignees |
| **Slack** | Post messages to channels (rich formatting) |
| **Linear** | Create issues with priority and project |
| **Jira** | Create issues with project and type |
| **Notion** | Create pages in databases |
| **Discord** | Post messages to channels |
| **Teams** | Post messages to channels |
| **Email / Resend** | Send templated emails |
| **Zendesk** | Create support tickets |
| **Intercom** | Create conversations |
| **HubSpot** | Create tickets in pipelines |
| **Shopify** | Tag orders, update customers |
| **Custom Webhook** | POST to any HTTPS endpoint |
| **Zapier** | Trigger Zap workflows |

### Priority Mapping

When creating issues in external tools, Supp's priority levels are mapped automatically:

| Supp Priority | GitHub Label | Linear | Jira |
|---------------|-------------|--------|------|
| critical | P0 | 1 (Urgent) | Highest |
| high | P1 | 2 (High) | High |
| medium | P2 | 3 (Medium) | Medium |
| low | P3 | 4 (Low) | Low |

## Configuration Options

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `intent` | string | required | Intent slug to match |
| `action_type` | enum | required | `template`, `mcp_action`, `escalate`, `none` |
| `action_config` | object | `{}` | Provider-specific configuration |
| `confidence_threshold` | float | 0.80 | Minimum confidence to trigger |
| `require_confirmation` | bool | false | Require customer confirmation first |
| `enabled` | bool | true | Active/inactive toggle |
| `priority_filter` | string[] | null | Only fire for these priority levels |
| `enable_priority_scoring` | bool | null | Override global priority setting for this intent |
| `collect_fields` | array | null | Custom fields to collect before execution |

## Collect Fields

Collect additional information from the customer before executing an action:

```json
{
  "collect_fields": [
    {"key": "email", "label": "Your email", "type": "email", "required": true},
    {"key": "steps", "label": "Steps to reproduce", "type": "text", "required": false}
  ]
}
```

**Supported field types:** `email`, `number`, `text`, `tel`

When configured, the widget shows a form after classification. The action only executes after the customer submits the form.

## Bulk Configuration

Apply the same configuration to multiple intents at once via the API:

```json
POST /api/routing/batch
{
  "intents": ["bug_report", "performance_issue", "crash_report"],
  "action_type": "mcp_action",
  "action_config": {
    "integration_provider": "github",
    "github_owner": "acme",
    "github_repo": "app"
  }
}
```

Supports up to 500 intents per batch, with optional per-intent overrides.

## Testing Rules

### Simulate a Classification

Test whether a rule would fire for a given message without executing any actions:

```json
POST /api/routing/test
{
  "trigger_id": "rule-uuid",
  "sample_text": "My app keeps crashing when I open settings"
}
```

Returns: whether the rule would fire, the matched intent, confidence, and a preview of the template response.
