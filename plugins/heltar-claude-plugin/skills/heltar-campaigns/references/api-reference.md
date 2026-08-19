---
title: Campaigns
description: Create and manage campaigns
icon: Send
order: 6
---

# Campaigns API

Send bulk template message campaigns to thousands of contacts. Campaigns are processed in the background with full delivery tracking.

> [!IMPORTANT]
> Campaigns can only use **approved templates**. Make sure your template is approved before creating a campaign.

---

:::api
method: POST
endpoint: /v1/campaigns/send
title: Send Campaign
description: Create and send a campaign immediately. Messages are queued and sent in batches for optimal delivery.

## Body Parameters

- campaignName: string [required] - Campaign name for identification
- templateName: string [required] - Approved template name to use
- languageCode: string [required] - Template language code
- messages: array - Array of recipients with personalized variables. Required unless you send `finalPayloadFileUrl` instead
- finalPayloadFileUrl: string - URL of an uploaded NDJSON file holding the recipients, one per line. Use this instead of `messages` for large campaigns
- recipientCount: number - Number of recipients in that file. Required with `finalPayloadFileUrl`
- campaignDesc: string - Campaign description
- source: string - Campaign source (web, api, csv)

> [!NOTE]
> Supply **either** `messages` **or** `finalPayloadFileUrl` with `recipientCount` — not both.

```request
{
  "campaignName": "Diwali Sale 2024",
  "campaignDesc": "Festival discount offer",
  "templateName": "promo_offer",
  "languageCode": "en",
  "messages": [
    {
      "clientWaNumber": "919876543210",
      "variables": [
        { "type": "text", "text": "John" },
        { "type": "text", "text": "20%" }
      ]
    },
    {
      "clientWaNumber": "919876543211",
      "variables": [
        { "type": "text", "text": "Jane" },
        { "type": "text", "text": "25%" }
      ]
    }
  ],
  "source": "api"
}
```

## Response

```response
{
  "campaign": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "name": "Diwali Sale 2024",
    "status": "running",
    "templateName": "promo_offer",
    "statsTotal": 2,
    "statsSent": 2,
    "statsDelivered": 0,
    "statsRead": 0,
    "statsFailed": 0,
    "createdAt": "2024-01-15T10:00:00Z"
  },
  "messagesResponse": {
    "success": { "919876543210": {...}, "919876543211": {...} },
    "fail": {}
  }
}
```

:::

---

## Large campaigns

Sending a few lakh recipients inside `messages` makes the request body very
large. Upload the recipients as a file instead and send its URL: the request
stays a couple of KB whatever the campaign's size, and there is no recipient
count at which it stops working.

The file is **NDJSON** — one JSON object per line, each line exactly what a
`messages` entry would have been:

```
{"clientWaNumber":"919876543210","variables":[{"type":"text","text":"John"}]}
{"clientWaNumber":"919876543211","variables":[{"type":"text","text":"Jane"}]}
```

Upload it wherever the API can read it over HTTPS, then send:

```json
{
  "campaignName": "Diwali Sale 2024",
  "templateName": "promo_offer",
  "languageCode": "en",
  "finalPayloadFileUrl": "https://your-storage.example.com/diwali-recipients.ndjson",
  "recipientCount": 200000,
  "source": "api"
}
```

`recipientCount` is a ceiling, not a hint: if the file turns out to hold more
recipients than you declared, only the first `recipientCount` of them are sent.
Fewer is fine — the campaign simply sends what the file holds.

Each line must be a single recipient and no line may exceed 1,000,000
characters. A file written as one big JSON array is a single line and will be
rejected with nothing sent; write one recipient per line instead.

The response carries the `campaign` object as usual. `messagesResponse` comes
back empty for this form — the recipients are already in the file you supplied,
so they are not echoed. Track delivery with
`GET /v1/campaigns/{campaignId}` as normal.

The call returns once every recipient in the file has been queued, so a very
large file keeps the request open a little longer. Sending then continues in the
background at your account's send rate, so `statsSent` climbs over the following
minutes rather than being final when the call returns.

---

:::api
method: GET
endpoint: /v1/campaigns
title: List All Campaigns
description: Get all campaigns with their delivery statistics.

## Response

```response
{
  "data": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "name": "Diwali Sale 2024",
      "status": "sent",
      "templateName": "promo_offer",
      "statsTotal": 1000,
      "statsSent": 950,
      "statsDelivered": 920,
      "statsRead": 450,
      "statsFailed": 50,
      "createdAt": "2024-01-15T10:00:00Z"
    }
  ]
}
```

:::

---

:::api
method: GET
endpoint: /v1/campaigns/:id
title: Get Campaign Stats
description: Get real-time delivery statistics for a campaign.

## Path Parameters

- id: string [required] - Campaign ID

## Response

```response
{
  "data": {
    "statsTotal": 1000,
    "statsSent": 950,
    "statsDelivered": 920,
    "statsRead": 450,
    "statsFailed": 50
  }
}
```

:::

---

:::api
method: GET
endpoint: /v1/campaigns/download-stats/:id
title: Download Campaign Stats
description: Download detailed campaign statistics as a CSV file for analysis.

## Path Parameters

- id: string [required] - Campaign ID

## Response

Returns a CSV file with per-recipient delivery status.
:::

---

## Campaign Status

| Status     | Description                   |
| ---------- | ----------------------------- |
| `draft`    | Saved but not sent            |
| `schedule` | Scheduled for future delivery |
| `running`  | Currently sending messages    |
| `sent`     | All messages sent             |

---

## Third-Party Integrations

Track campaign performance in your analytics platforms by including integration tracking IDs:

```json
{
  "messages": [
    {
      "clientWaNumber": "919876543210",
      "variables": [...],
      "integrations": [
        { "name": "webEngage", "msgId": "unique-tracking-id" },
        { "name": "cleverTap", "msgId": "ct-123" },
        { "name": "moEngage", "msgId": "me-456" }
      ]
    }
  ]
}
```

| Platform    | Description                   |
| ----------- | ----------------------------- |
| `webEngage` | WebEngage journey tracking    |
| `cleverTap` | CleverTap campaign engagement |
| `moEngage`  | MoEngage push tracking        |

---

## Best Practices

> [!TIP]
> Follow these guidelines for successful campaigns:

- **Test first** - Send to a small group (10-20) before large campaigns
- **Personalize** - Use template variables for customer names, order IDs, etc.
- **Timing matters** - Schedule during business hours in recipient's timezone
- **Monitor delivery** - Watch for high failure rates which may indicate quality issues
- **Respect opt-outs** - Remove unsubscribed contacts before sending
