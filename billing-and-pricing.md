# Billing & Pricing

Supp uses a **pay-per-resolution** model. You purchase prepaid credits and are charged only when tickets are successfully resolved. No subscriptions, no seat licenses.

## Pricing

| Action | Cost |
|--------|------|
| Classification + template response | $0.20 |
| Classification + integration action (GitHub, Slack, etc.) | $0.30 |
| Priority scoring (addon) | +$0.03 |
| Batch classification | 50% discount |

## Credit Packages

Purchase credits in **Dashboard > Billing**:

- Preset packages from **$1** to **$2,000**
- Custom amounts from **$1** to **$10,000**
- Pay with any card via Stripe checkout

### Signup Bonus

New accounts receive **$5.00 in free credits** — enough for ~25 basic classifications.

## Spend Caps

Every API key has a configurable spend limit to protect against unexpected usage:

- **Default**: $5.00/day
- **Configurable**: Set any amount, daily or weekly window
- **Override**: Temporarily increase for planned high-volume events

If the spend cap is reached, the widget returns an error until the next window or an override is approved.

## Deferred Charging

When the classification confidence is below the auto-success threshold (0.70), the customer is asked to confirm the suggested response before you're charged. You only pay when the customer accepts.

## Refunds

If an integration action fails after charging (e.g., GitHub API is down), credits are automatically refunded and the conversation is escalated to a human.

## How Billing Works

```
1. Customer sends message → Classification runs
2. Balance check → Do you have enough credits?
3. Action executes → Template response or integration call
4. Charge → Credits deducted from your balance
5. If action fails → Automatic refund
```

All charges are atomic and idempotent — you'll never be double-charged for the same conversation.

## Viewing Usage

- **Dashboard > Billing**: Current balance and transaction history
- **Dashboard > Analytics**: Cost breakdown by intent, time period, and action type
- **API**: `GET /api/analytics?period=30d` for programmatic access
- **MCP**: `supp_balance` and `supp_stats` tools

## Notifications

You'll receive email notifications when:
- Credits are added successfully
- Your balance is running low
- A payment fails
