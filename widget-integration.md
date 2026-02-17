# Widget Integration

The Supp widget is an embeddable chat interface that you add to your website. When customers send a message, it's classified, routed, and resolved automatically.

## Quick Start

```html
<script src="https://supp.support/widget.js"></script>
<script>
  SupWidget.init({
    apiKey: "pk_live_your_key_here",
    orgId: "your-org-id"
  });
</script>
```

The widget appears as a floating chat button in the bottom-right corner.

## How It Works

```
1. Customer opens widget → Settings loaded (email requirement, branding)
2. Customer types message → Sent to Supp for classification
3. AI classifies intent → Routing rule checked
4. Response shown → Template, integration result, or escalation
5. Customer confirms or provides feedback
6. Conversation ends → Marked as resolved
```

## Conversation Lifecycle

### 1. Submit a Message

The customer types a message. The widget sends it along with your API key.

**Response includes:**
- `session_id` — unique conversation identifier
- `session_token` — for loading history or ending the conversation later
- `intent` — what the model classified
- `confidence` — how confident the model is
- `response` — the AI's suggested response (if a routing rule matched)

### 2. Confirmation (When Needed)

If confidence is below 0.70, the customer sees the suggested response and is asked "Is this helpful?" before you're charged.

If they accept, the charge goes through. If they reject, no charge.

### 3. Collect Fields (When Configured)

If your routing rule requires additional fields (email, description, etc.), the widget shows a form. The action executes only after the customer submits the form.

### 4. Feedback

If feedback is enabled, customers can flag a response as incorrect. This data improves the model over time.

### 5. End Conversation

The customer clicks "End" to resolve the conversation.

## Configuration Options

Configure these in **Dashboard > Settings**:

| Setting | Default | Description |
|---------|---------|-------------|
| Require email | false | Ask for email before first message |
| Feedback enabled | true | Show "Was this helpful?" buttons |
| Show "Talk to human" button | true | Let customers request a human agent |
| Powered by Supp | true | Show branding in widget |
| Offline message | "" | Message shown when team is unavailable |
| Auto-escalation | false | Auto-escalate if no routing rule matches |

## Domain Restrictions

For production, restrict your API key to specific domains:

```
Allowed domains: example.com, app.example.com
```

Requests from other domains will be rejected. This prevents unauthorized use of your API key.

## Spend Caps

Set a daily or weekly spend limit on your API key:

```
Spend cap: $5.00/day
```

If the cap is reached, the widget stops processing messages until the next window resets.

## Escalation Detection

If a customer types something like "I want to speak to a human," the widget bypasses classification and immediately escalates to your team.

## Resuming Conversations

Customers can resume previous conversations using their session token. The widget loads the conversation history and continues where they left off.

## Demo Mode

Your landing page can offer a free trial classification — one per visitor per week, no API key needed. Great for letting prospects try Supp before signing up.

## Widget API Endpoints

All widget endpoints accept your publishable API key (`pk_live_`).

| Endpoint | Description |
|----------|-------------|
| `POST /api/widget` | Submit message for classification |
| `GET /api/widget/config` | Load widget settings |
| `POST /api/widget/status` | Check agent availability |
| `POST /api/widget/confirm` | Confirm deferred charge |
| `POST /api/widget/fields` | Submit collected field values |
| `POST /api/widget/feedback` | Submit classification feedback |
| `POST /api/widget/end` | End conversation |
| `POST /api/widget/history` | Load conversation history |

See [API Reference](./api-reference.md) for request/response details.
