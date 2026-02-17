# MCP Server

Supp exposes a **Model Context Protocol (MCP)** server that lets AI agents — Claude Code, Cursor, Windsurf, and other MCP-compatible clients — manage your support operations.

## What You Can Do

Use natural language through your AI agent to:

- "Classify this message and show me the intent"
- "Create a routing rule for bug reports that creates GitHub issues"
- "Show me all escalated tickets from the last week"
- "Connect my Slack workspace"
- "What's my current balance?"
- "Auto-escalate all critical messages"

The MCP server translates these into API calls behind the scenes.

## Setup

### Claude Code

Add to your MCP settings:

```json
{
  "mcpServers": {
    "supp": {
      "url": "https://mcp.supp.support/mcp",
      "transport": "streamable-http"
    }
  }
}
```

On first use, you'll be prompted to authorize via OAuth. After that, your AI agent can manage Supp directly.

### API Key Authentication

For non-interactive setups, use a secret key:

```json
{
  "mcpServers": {
    "supp": {
      "url": "https://mcp.supp.support/mcp",
      "transport": "streamable-http",
      "headers": {
        "Authorization": "Bearer sk_live_your_secret_key"
      }
    }
  }
}
```

## Available Tools

### Classification

| Tool | Description |
|------|-------------|
| `supp_classify` | Classify a message (returns intent + confidence) |
| `supp_classify_with_priority` | Classify with priority scoring in one call |
| `supp_intents` | List all 315 intents, filterable by category |
| `supp_categories` | List all intent categories with counts |

### Tickets

| Tool | Description |
|------|-------------|
| `supp_tickets` | List tickets with filters (status, intent, date range) |
| `supp_ticket` | Get full ticket details with messages |
| `supp_ticket_resolve` | Mark a ticket as resolved |
| `supp_escalated` | List escalated tickets needing attention |
| `supp_escalation_respond` | Respond to an escalated ticket |

### Routing Rules (Triggers)

| Tool | Description |
|------|-------------|
| `supp_triggers` | List all routing rules |
| `supp_trigger_create` | Create a new routing rule |
| `supp_trigger_update` | Update an existing rule |
| `supp_trigger_delete` | Delete a rule |
| `supp_trigger_simulate` | Dry-run a rule against sample text |
| `supp_trigger_execute` | Execute a rule's actions for real |
| `supp_routing_bulk` | Apply config to multiple intents |
| `supp_routing_category` | Apply config to all intents in a category |

### Integrations

| Tool | Description |
|------|-------------|
| `supp_integrations` | List connected integrations |
| `supp_connect` | Get OAuth URL or setup instructions |
| `supp_disconnect` | Disconnect an integration |

### Priority

| Tool | Description |
|------|-------------|
| `supp_priority_score` | Score a message's urgency |
| `supp_priority_rules` | List blanket priority rules |
| `supp_priority_rule_set` | Create/update a blanket rule |
| `supp_priority_rule_delete` | Delete a blanket rule |
| `supp_priority_scoring_intent` | Per-intent priority override |
| `supp_priority_scoring_settings` | Global priority settings |

### Billing

| Tool | Description |
|------|-------------|
| `supp_balance` | Check current credit balance |
| `supp_spend_cap` | View/update spend caps |
| `supp_refund_status` | Check refund status |

### Analytics

| Tool | Description |
|------|-------------|
| `supp_stats` | Overview metrics for a time period |
| `supp_stats_intents` | Intent distribution breakdown |
| `supp_stats_triggers` | Trigger performance (fires, resolutions) |
| `supp_stats_export` | Export data as CSV or JSON |

### API Keys

| Tool | Description |
|------|-------------|
| `supp_api_key_create` | Generate a new API key |

### Approvals

| Tool | Description |
|------|-------------|
| `supp_queue` | View pending approvals |
| `supp_approve` | Approve a pending action |
| `supp_reject` | Reject a pending action |

### Knowledge

| Tool | Description |
|------|-------------|
| `supp_docs` | Search Supp documentation |
| `supp_docs_connect` | Link external docs (Notion, GitHub, URL) |

## Example Workflows

### Set Up Bug Report Automation

```
You: "Create a trigger that sends bug reports to GitHub"

Agent uses: supp_integrations → finds connected GitHub
Agent uses: supp_trigger_create → creates rule:
  - when: bug_report
  - action: create GitHub issue
  - labels: ["bug", "from-supp"]
  - response: "We've filed a ticket for your issue."
```

### Review Escalated Tickets

```
You: "Show me escalated tickets from this week"

Agent uses: supp_escalated → returns 3 tickets
Agent uses: supp_ticket → loads each ticket's messages
Agent: "There are 3 escalated tickets:
  1. Login issues (critical) - customer locked out for 2 days
  2. Billing dispute - customer charged twice
  3. Feature request - wants dark mode"
```

### Auto-Escalate Critical Issues

```
You: "Auto-escalate anything critical"

Agent uses: supp_priority_rule_set → creates blanket rule:
  - priority_level: critical
  - action: escalate to human
  - enabled: true
```

### Bulk Configure a Category

```
You: "Route all billing intents to our Slack #billing channel"

Agent uses: supp_routing_category → applies to all 31 billing intents:
  - action: post to Slack
  - channel: #billing
  - response template: "Your billing inquiry has been forwarded to our team."
```
