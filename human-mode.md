# Human Mode

Human mode turns Supp into a free helpdesk. No AI classification, no per-ticket charges, no routing automation. Every message goes straight to your team's inbox.

## What Changes

| Feature | AI Mode | Human Mode |
|---------|---------|------------|
| Classification | Automatic (ML model) | Off |
| Routing rules | Active | Ignored |
| Per-ticket billing | $0.05-$0.30/resolution | Free |
| "Talk to a Human" button | Shown in widget | Hidden (redundant) |
| Auto-escalation detection | Active | Off |
| Priority scoring | Optional add-on | Off |
| Widget | Shows AI responses | Shows "Got it" confirmation |
| Dashboard inbox | AI-resolved + escalated tickets | All conversations |

## Enabling Human Mode

### During onboarding

The onboarding tutorial asks you to pick a mode after the welcome screen. Select **Human only** and finish the tutorial. Your org starts in human mode immediately.

### In settings

Go to **Dashboard > Settings > General**. The **Human mode** toggle is at the top. Flip it on. Takes effect immediately for all new widget messages.

## How It Works

When human mode is on:

1. Customer sends a message through the widget
2. Supp creates a conversation in your inbox -- no classifier runs, no charge
3. Widget shows: "Got it. A team member will get back to you shortly."
4. Your team sees the conversation in the dashboard and responds directly

The widget still collects messages, tracks sessions, and supports file attachments. It just doesn't run any AI on them.

## Switching to AI Mode

Turn off the human mode toggle in Settings. AI classification starts immediately on new messages. You have $5 in free credits from signup.

Your existing conversations stay in the inbox. Only new messages start going through the classifier.

## When to Use Human Mode

- You want a free helpdesk widget before committing to AI automation
- Your support volume is low enough that your team handles everything directly
- You're evaluating Supp as a platform and want to test the dashboard and widget first
- You have a specialized domain where you want to train a [custom model](https://supp.support/custom-models) before turning on AI

## API Behavior

When human mode is on, the widget API (`POST /api/widget`) returns:

```json
{
  "data": {
    "session_id": "...",
    "session_token": "...",
    "conversation_id": "...",
    "served": false,
    "human_mode": true,
    "response": null
  }
}
```

The `/api/widget/status` endpoint returns `human_mode: true` and `show_human_button: false`.

The `/api/widget/config` endpoint returns `human_mode: true`.

The `/api/ai/classify` endpoint is unaffected -- human mode only applies to widget messages. If you call the classification API directly, it still runs and charges normally.
