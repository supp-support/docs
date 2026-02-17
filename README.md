# Supp Documentation

**Supp** is an AI-powered support automation platform that classifies customer messages, routes them to the right team or tool, and resolves tickets automatically — all on a pay-per-resolution pricing model.

## What Supp Does

1. A customer sends a message through an embeddable chat widget on your website
2. Our ML model classifies the message into one of **315 intents** across 13 categories
3. Routing rules determine what happens next: send a template response, create a GitHub issue, post to Slack, escalate to a human, or execute a multi-step pipeline
4. You are charged only when a ticket is successfully resolved

No subscriptions. No seat licenses. You pay for results.

## Documentation

| Section | Description |
|---------|-------------|
| [Getting Started](./getting-started.md) | Create an account, add credits, and embed the widget |
| [Widget Integration](./widget-integration.md) | Embed the chat widget on your website |
| [API Reference](./api-reference.md) | REST API for classification, conversations, and management |
| [Intent Classification](./intent-classification.md) | 315 intents, confidence thresholds, and how classification works |
| [Routing & Automation](./routing-and-automation.md) | Set up rules, actions, and multi-step pipelines |
| [Priority Scoring](./priority-scoring.md) | Real-time urgency classification and blanket rules |
| [Integrations](./integrations.md) | Connect GitHub, Slack, Linear, Jira, and 12 more |
| [MCP Server](./mcp-server.md) | Use AI agents (Claude Code, Cursor) to manage support |
| [Billing & Pricing](./billing-and-pricing.md) | Pay-per-resolution pricing, credits, and spend caps |

## Why Supp?

- **Pay for results, not seats** — charged per resolved ticket, not per user or per month
- **315 intents** — custom-trained ML model with deep understanding of support requests
- **16 integrations** — GitHub, Slack, Linear, Jira, Notion, Discord, Teams, Zendesk, and more
- **Multi-step pipelines** — chain actions together (e.g., create GitHub issue + Slack alert + template response)
- **Priority scoring** — automatically classify urgency and route critical issues faster
- **MCP support** — let AI agents manage your support operations via Claude Code, Cursor, or any MCP client
- **Embeddable widget** — drop a script tag on your site and start resolving tickets

## Quick Start

```html
<!-- 1. Add the widget to your website -->
<script src="https://supp.support/widget.js"></script>
<script>
  SupWidget.init({
    apiKey: "pk_live_your_key_here",
    orgId: "your-org-id"
  });
</script>
```

That's it. Messages from your customers will be classified and routed based on your configured rules.

See [Getting Started](./getting-started.md) for the full setup guide.
