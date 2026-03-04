# Prevent duplicate webhook executions in n8n

Webhook providers use **at-least-once delivery**. If a request times out or fails, the provider retries — and your workflow executes twice. The same Stripe charge runs again. The same confirmation email goes out again.

This template adds an idempotency gate before any side effect. The first event goes through. Retries are caught and stopped.

---

## How it works

```
Incoming webhook → AARI gate → Duplicate? → ALLOW → your action → report SUCCESS
                                           ↘ BLOCK → stop
```

1. The AARI gate checks the event's idempotency key against a 24-hour window.
2. First seen → `ALLOW` → your action runs → outcome recorded as `SUCCESS`.
3. Retry → `BLOCK` → workflow stops → outcome recorded as `BLOCKED`.

The Webhook node uses **Respond immediately** — `200 OK` goes back to the provider before the workflow runs. No retry loops caused by slow execution.

---

## Quick start (2 minutes)

**1. Import the workflow**

Download [`workflows/prevent-duplicate-webhook-executions.json`](workflows/prevent-duplicate-webhook-executions.json) and import it into n8n.

Or download directly: [https://api.getaari.com/n8n-template](https://api.getaari.com/n8n-template)

**2. Get a free AARI API key**

[https://api.getaari.com/n8n](https://api.getaari.com/n8n) — free, 2,500 gate calls/month, no credit card.

**3. Create a credential in n8n**

Credentials → New → **Header Auth**
- Header name: `X-API-Key`
- Value: your AARI key

Select it in the **AARI idempotency gate** node.

**4. Connect your action**

Replace the **✅ Run your action here** node with your real action — Stripe charge, send email, DB insert, API call.

---

## Idempotency key examples

The key is pre-set and auto-detects the event ID. No manual configuration needed.

| Provider | Resolved key |
|----------|-------------|
| Stripe | `$json.id` → `evt_1234...` |
| GitHub | `$json.id` → `push_5678...` |
| Shopify | `$json.id` → `order_9012...` |
| Generic (no id field) | `$json.type`:`$json.created` |

The fallback chain: `body.id` → `event_id` → `eventId` → `webhook_id` → header `x-event-id` → body fingerprint → `execution.id` (last resort, not retry-safe).

---

## What you see in the dashboard

| Event | Decision | Outcome |
|-------|----------|---------|
| First delivery | ALLOW | SUCCESS |
| Retry / duplicate | BLOCK | BLOCKED |
| 0 PENDING in normal runs | | |

Dashboard: [https://api.getaari.com/dashboard](https://api.getaari.com/dashboard)

---

## Works with

Stripe · GitHub · Shopify · WooCommerce · any webhook provider using at-least-once delivery.

---

## Documentation

- [n8n quickstart](docs/n8n-quickstart.md)
- [Choosing idempotency keys](docs/idempotency-keys.md)
- [API reference](https://api.getaari.com/docs#n8n)

---

**Get a free key + import workflow: [https://api.getaari.com/n8n](https://api.getaari.com/n8n)**
