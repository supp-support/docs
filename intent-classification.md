# Intent Classification

Supp uses a custom-trained ML model to classify customer messages into **315 intents** across **13 categories**. Classification happens automatically when a customer sends a message through the widget or API.

## How It Works

1. Customer sends a message
2. The model analyzes the text and returns the most likely intent + confidence score
3. If confidence exceeds your configured threshold, the routing rule fires
4. If confidence is too low, the message is escalated to a human

Classification latency is ~100-200ms.

## Intent Taxonomy

### 315 Intents Across 13 Categories

| Category | Count | Examples |
|----------|-------|---------|
| **No Intent** | 8 | Off-topic, self-talk, humor, rambling |
| **Account Management** | 31 | Password reset, 2FA setup, account deletion, device management |
| **Billing & Payment** | 31 | Refund request, invoice dispute, payment failed, tax exemption |
| **Subscription Management** | 21 | Upgrade, downgrade, pause, cancel, auto-renewal |
| **Product Inquiry** | 31 | Availability, pricing, feature comparison, compatibility |
| **Technical Support** | 47 | Bug report, performance issue, API troubleshooting, installation |
| **Order & Delivery** | 23 | Order tracking, shipping delay, cancellation, address change |
| **Returns & Refunds** | 24 | Return request, exchange, defective product, refund status |
| **Feature Requests** | 22 | New feature, UI improvement, integration request, roadmap |
| **Complaints & Feedback** | 20 | Service complaint, negative experience, response time |
| **General Inquiry** | 15 | Business hours, contact info, general questions |
| **Verification & Auth** | 19 | Identity verification, biometric setup, security questions |
| **Loyalty & Rewards** | 14 | Points balance, referral codes, cashback claims |

### Intent Structure

Each intent has:
- **ID**: Machine-readable slug (e.g., `password_reset`)
- **Category**: Parent grouping (e.g., `account_management`)
- **Display Label**: Human-friendly name (e.g., "Password Reset")
- **Description**: What this intent covers

You can browse all intents in your dashboard under **Routing > Intents**.

## Confidence Thresholds

The system uses confidence scores to decide what action to take:

| Threshold | Default | Effect |
|-----------|---------|--------|
| **Escalation floor** | 0.35 | Below this, always escalate to human |
| **Auto-success** | 0.70 | Above this, charge immediately (no confirmation needed) |
| **Routing rule** | 0.80 | Per-rule threshold — action only fires if confidence exceeds this |

### Example

```
Message: "I can't log into my account"
Classification: account_login_issue (confidence: 0.92)

✓ 0.92 >= 0.35 (escalation floor) → Continue
✓ 0.92 >= 0.80 (routing rule threshold) → Execute action
✓ 0.92 >= 0.70 (auto-success) → Charge immediately
```

```
Message: "hmm maybe the thing with the stuff"
Classification: observational_rambling (confidence: 0.28)

✗ 0.28 < 0.35 (escalation floor) → Escalate to human
```

You can adjust the routing rule threshold per intent. Lower thresholds fire more often but with less certainty.

## Classification via API

### POST `/api/ai/classify`

```json
{
  "message": "The API returns 500 errors intermittently",
  "include_priority": true
}
```

```json
{
  "intent": "api_troubleshooting",
  "confidence": 0.88,
  "all_intents": [
    {"intent": "api_troubleshooting", "confidence": 0.88},
    {"intent": "performance_issue", "confidence": 0.72}
  ],
  "priority": "high",
  "action_type": "mcp_action",
  "suggested_response": "We've created a ticket for your issue.",
  "cost": 0.33
}
```

See [API Reference](./api-reference.md) for full endpoint documentation.

## Batch Classification

For bulk operations, pass `is_batch: true`. Batch requests get a **50% discount** but run at lower priority (only when the server is idle).

## Classification Feedback

When customers flag a classification as incorrect via the widget's feedback buttons, that data is collected and used to improve the model over time.

You can enable/disable feedback collection per organization in **Dashboard > Settings**.
