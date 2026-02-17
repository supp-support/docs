# Priority Scoring

Priority scoring classifies message urgency in real time. It runs alongside intent classification and helps you route critical issues faster.

## How It Works

```
Customer message: "Our entire team is locked out of the platform"
    │
    ▼
Intent Classification → "account_login_issue" (confidence: 0.91)
Priority Scoring → "critical"
    │
    ▼
Routing Rule (priority_filter: ["high", "critical"]) → ✓ Match
    → Slack alert to #incidents
    │
Blanket Priority Rule ("critical" → escalate) → ✓ Match
    → Conversation escalated to human
```

## Priority Levels

| Level | Description |
|-------|-------------|
| `low` | Routine questions, general inquiries |
| `medium` | Standard support requests |
| `high` | Urgent issues affecting workflow |
| `critical` | Outages, security incidents, data loss |

## Pricing

| Operation | Cost |
|-----------|------|
| Priority scoring (standalone) | $0.03 per message |
| Priority scoring (with classification) | +$0.03 added to resolution cost |

## Enabling Priority Scoring

### Global Setting

Enable for all intents in **Dashboard > Settings**:

```
Enable Priority Scoring: ON
```

### Per-Intent Override

Override the global setting for specific intents in your routing rules:

| Setting | Behavior |
|---------|----------|
| On | Always score priority for this intent |
| Off | Never score priority for this intent |
| Inherit | Use the global organization setting |

## Priority Filters

Routing rules can filter by priority level. Only messages matching the filter trigger the action:

```json
{
  "intent": "bug_report",
  "action_type": "mcp_action",
  "priority_filter": ["high", "critical"]
}
```

If no filter is set, the rule fires for any priority level.

## Blanket Priority Rules

Apply an action to ALL messages at a given priority level, regardless of intent:

- Auto-escalate all critical messages
- Post all high-priority messages to Slack #urgent
- Send an acknowledgment template for all medium messages

Configure blanket rules in **Dashboard > Priority Rules**. One rule per priority level.

Blanket rules execute alongside intent-specific rules (not instead of).

## Template Variables

Use `{{priority}}` in template responses:

```
Your request has been classified as {{priority}} priority.
```

## API Usage

### Standalone Priority Scoring

```json
POST /api/ai/priority
{
  "message": "We're experiencing a complete outage"
}

// Response
{
  "priority": "critical",
  "cost": 0.03
}
```

### Classification with Priority

```json
POST /api/ai/classify
{
  "message": "The API is returning 500 errors",
  "include_priority": true
}

// Response
{
  "intent": "api_troubleshooting",
  "confidence": 0.88,
  "priority": "high",
  "cost": 0.23
}
```
