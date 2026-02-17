# Getting Started

## 1. Create an Account

Sign up at [supp.support/signup](https://supp.support/signup). You'll get a **$5.00 credit** automatically — enough for ~25 classifications.

## 2. Create an API Key

Go to **Dashboard > API Keys** and create a publishable key (`pk_live_`). This key is safe to use in client-side code.

You can optionally:
- Restrict the key to specific domains (recommended for production)
- Set a daily spend cap (default: $5.00/day)

## 3. Configure Routing Rules

Go to **Dashboard > Routing** and set up rules for the intents you care about.

For example:
- `bug_report` → Create a GitHub issue
- `password_reset` → Send a template response with reset instructions
- `complaint_about_employee` → Escalate to a human agent

See [Routing & Automation](./routing-and-automation.md) for the full configuration guide.

## 4. Connect Integrations

Go to **Dashboard > Integrations** and connect the services you use:

- **GitHub** — Create issues automatically
- **Slack** — Post to channels with rich formatting
- **Linear** — Create issues with priority mapping
- **Jira** — Create issues in your project
- And 12 more...

Most integrations support OAuth (one-click connect). See [Integrations](./integrations.md) for details.

## 5. Embed the Widget

Add the widget to your website:

```html
<script src="https://supp.support/widget.js"></script>
<script>
  SupWidget.init({
    apiKey: "pk_live_your_key_here",
    orgId: "your-org-id"
  });
</script>
```

See [Widget Integration](./widget-integration.md) for the full embed guide and configuration options.

## 6. Add Credits

When your signup bonus runs out, add credits via **Dashboard > Billing**. Credit packages range from $1 to $2,000, or enter a custom amount up to $10,000.

See [Billing & Pricing](./billing-and-pricing.md) for the full pricing breakdown.

## What Happens When a Customer Sends a Message

```
Customer types: "I can't log into my account"
         │
         ▼
   Classification
   → intent: "account_login_issue" (confidence: 0.92)
         │
         ▼
   Routing Rule Lookup
   → Found rule for "account_login_issue": send template
         │
         ▼
   Action Execution
   → Response: "To reset your password, visit example.com/reset"
         │
         ▼
   Billing
   → $0.20 charged from your balance
```

If no routing rule exists for the classified intent, the conversation is escalated to your team.

## Next Steps

- [Set up routing rules](./routing-and-automation.md) for your most common intents
- [Browse all 315 intents](./intent-classification.md) to see what Supp can classify
- [Connect integrations](./integrations.md) to route tickets to your existing tools
- [Try the MCP server](./mcp-server.md) to manage support with AI agents
