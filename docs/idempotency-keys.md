# Choosing idempotency keys

## The rule

A good idempotency key identifies one specific event, is stable across retries, and is unique across different events.

## Prefer provider event IDs

Most webhook providers include a unique event ID. Use it.

```
Stripe:   $json.id          → "evt_1Abc23DefGHI..."
GitHub:   $json.id          → "push_abc123..."
Shopify:  $json.id          → "12345678"
Custom:   $json.event_id    → your own UUID
Headers:  x-event-id        → some providers use headers
```

The template checks these automatically in order: `body.id` → `event_id` → `eventId` → `webhook_id` → header `x-event-id` → header `x-request-id`.

## Body fingerprint fallback

If no ID field exists, the template falls back to a fingerprint of stable body fields:

```
type:event:action:created:data.object.id:order_id:customer_id:...
```

This works for most cases where the payload is structurally identical on retry.

## Avoid delivery timestamps

Do not include delivery timestamps in the key. Providers may change them on retry.

```
# Bad — changes on retry
$json.delivered_at

# Good — event creation time, stable
$json.created
```

## Last resort: execution ID

`$execution.id + '_action'` is the final fallback. It only deduplicates within the same n8n execution — it does **not** catch retries from the provider across separate executions.

Use this only if you have no other stable identifier.
