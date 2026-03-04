# n8n Quickstart

## Step 1 — Import the workflow

Download the workflow and import it into n8n:

- From this repo: [`workflows/prevent-duplicate-webhook-executions.json`](../workflows/prevent-duplicate-webhook-executions.json)
- Direct download: [https://api.getaari.com/n8n-template](https://api.getaari.com/n8n-template)

The workflow includes: Webhook trigger → AARI gate → duplicate check → your action → report SUCCESS.

## Step 2 — Create API credential

1. Get a free key at [https://api.getaari.com/n8n](https://api.getaari.com/n8n)
2. In n8n: Credentials → New → **Header Auth**
   - Header name: `X-API-Key`
   - Value: your AARI key
3. Select this credential in the **AARI idempotency gate** node.

## Step 3 — Idempotency key

The key is pre-set. It auto-detects the event ID from the payload:

| Provider | Key used |
|----------|----------|
| Stripe / GitHub / Shopify | `{{ $json.id }}` |
| Generic (no id field) | `{{ $json.type }}:{{ $json.created }}` |

No manual configuration needed for standard webhook providers.

## Webhook response mode

The Webhook node is set to **Respond immediately**. This sends `200 OK` to the provider before the workflow runs, preventing retry loops caused by slow execution time.

Do not change this to "Respond after workflow" — it will cause providers to retry on slow runs.

## Expected outcomes

| Event | Decision | Outcome |
|-------|----------|---------|
| First delivery | ALLOW | SUCCESS |
| Retry or duplicate | BLOCK | BLOCKED |

Zero PENDING in normal runs. If you see PENDING, the **📋 Report SUCCESS** node did not complete — check that the AARI credential is selected there too.
