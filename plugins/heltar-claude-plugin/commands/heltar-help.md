---
description: List every Heltar skill bundled in this plugin and what it covers
---

# /heltar-help

You are responding to a `/heltar-help` invocation. List every Heltar skill installed via this plugin and a one-line description of when to use it. Then ask the user what they want to do — pick the right skill based on their answer and load it.

Output exactly this skeleton, then stop:

```
Heltar skills available in this plugin:

  heltar-authentication  — API keys, headers, security. Start here for new integrations.
  heltar-messaging       — Send text/media/template/interactive messages, fetch media, read history.
  heltar-templates       — Create / approve / list / delete / analyze WhatsApp templates.
  heltar-campaigns       — Bulk template campaigns (immediate or scheduled), per-recipient stats.
  heltar-chatbots        — Activate bots, trigger talks, assign to contacts, off-WhatsApp test inference.
  heltar-webhooks        — Configure event delivery, parse Meta payloads, custom-data correlation.
  heltar-contacts        — Create / update contacts, attributes, tags, agent assignment, bot toggles.
  heltar-calls           — WhatsApp & SIP voice calls, employee-direct & AI-agent modes.
  heltar-groups          — WhatsApp Cloud-API groups: create, list, participants, invites, pins.
  heltar-code-editor     — Run a deployed Code Editor function from external systems.
  heltar-schedule        — Schedule chatbot nudges, future messages, future campaigns.

What do you want to do? (e.g. "send a template to 500 customers", "wire up webhooks", "place an AI voice call")
```

After the user answers, load the matching skill(s) and proceed. If the task spans multiple surfaces (e.g. "send a campaign and capture delivery webhooks"), load all relevant skills.
