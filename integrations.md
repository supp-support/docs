# Integrations

Supp connects to 16 external services. When a message is classified, routing rules can automatically create issues, post messages, send emails, or trigger webhooks in your existing tools.

## Supported Integrations

### Issue Tracking

| Provider | Connection | What It Does |
|----------|------------|-------------|
| **GitHub** | OAuth or token | Creates issues with labels, assignees, and priority tags |
| **Linear** | OAuth or API key | Creates issues with priority, team, and project |
| **Jira** | OAuth or basic auth | Creates issues with project, type, and priority |
| **Notion** | OAuth | Creates pages in databases with custom properties |

### Communication

| Provider | Connection | What It Does |
|----------|------------|-------------|
| **Slack** | OAuth or webhook | Posts messages to channels with rich formatting |
| **Discord** | Webhook | Posts messages to channels |
| **Microsoft Teams** | Webhook | Posts messages to channels |
| **Email (Resend)** | API key | Sends templated emails |

### CRM & Support

| Provider | Connection | What It Does |
|----------|------------|-------------|
| **Zendesk** | API key | Creates support tickets |
| **Intercom** | OAuth or API key | Creates conversations |
| **HubSpot** | API key | Creates tickets in pipelines |
| **Shopify** | API key | Tags orders, updates customer records |

### Automation

| Provider | Connection | What It Does |
|----------|------------|-------------|
| **Zapier** | Webhook URL | Triggers any Zap workflow |
| **Custom Webhook** | HTTPS URL | POSTs JSON to any endpoint |

## Connecting an Integration

### OAuth (Recommended)

For GitHub, Slack, Linear, Jira, Notion, and Intercom:

1. Go to **Dashboard > Integrations**
2. Click **Connect** next to the provider
3. Authorize on the provider's consent screen
4. Redirected back to your dashboard — done

### Manual Configuration

For providers without OAuth, or if you prefer:

1. Go to **Dashboard > Integrations**
2. Click **Configure** next to the provider
3. Enter your API key, webhook URL, or credentials
4. Click **Test Connection** to verify
5. Click **Save**

## Testing Connections

Before saving, you can test that your credentials work:

- **GitHub**: Verifies API access with your token
- **Slack**: Sends a test message to confirm bot permissions
- **Linear**: Queries your workspace to confirm access
- **Jira**: Validates project access
- **Custom/Zapier**: Pings the webhook URL

## Priority Mapping

When Supp creates issues in external tools, priority levels are mapped automatically:

| Supp Priority | GitHub Label | Linear | Jira |
|---------------|-------------|--------|------|
| critical | P0 | Urgent | Highest |
| high | P1 | High | High |
| medium | P2 | Medium | Medium |
| low | P3 | Low | Low |

## Slack Message Format

Slack messages include rich Block Kit formatting:

- Intent classification with confidence score
- Priority badge (if scoring is enabled)
- Customer message excerpt
- Direct link to the conversation in your dashboard

Bot token connections get the richest formatting. Webhook connections get a simpler format.

## GitHub Issue Format

GitHub issues are created with:

- **Title**: Intent display label or customer subject
- **Body**: Customer message with classification metadata
- **Labels**: Your configured labels + priority tag (P0-P3)

## Email (Resend)

Send automated emails when specific intents are detected:

- Template variables supported ({{intent}}, {{message}}, {{priority}})
- From address configurable
- HTML and plain text support

## Custom Webhooks

Send a POST request to any HTTPS endpoint:

```json
{
  "provider": "custom",
  "config": {
    "webhook_url": "https://your-api.example.com/supp-webhook"
  }
}
```

The webhook receives a JSON payload with the conversation details, classification results, and priority.

## Zapier

Trigger any Zap by connecting a Zapier webhook:

1. Create a Zap with "Webhooks by Zapier" as the trigger
2. Copy the webhook URL
3. Paste it into Supp's Zapier integration config
4. Set up your routing rule to trigger on the desired intent

This lets you connect Supp to any of Zapier's 6,000+ apps.

## Managing Integrations

### Disconnecting

Go to **Dashboard > Integrations** and click **Disconnect**. The integration stops receiving actions immediately.

### Re-Connecting

If a token expires or credentials change, reconnect via OAuth or update the manual configuration. Supp automatically detects expired tokens and will prompt you to reconnect.

### Multiple Integrations

You can connect multiple instances of the same provider. For example:
- Two Slack workspaces (one for engineering, one for support)
- Multiple GitHub repos
- Different email addresses for different intent categories

Configure which integration to use in each routing rule's `action_config`.

## Using Integrations via MCP

AI agents can manage integrations through the MCP server:

```
"Connect my GitHub account"     → supp_connect
"List my integrations"          → supp_integrations
"Disconnect Slack"              → supp_disconnect
```

See [MCP Server](./mcp-server.md) for the full tool reference.
