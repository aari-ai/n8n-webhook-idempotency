# Contributing

## Proposing improvements to the workflow

Open an issue describing the use case and the proposed change. Include:
- The webhook provider affected
- Why the current template doesn't handle it
- A suggested fix

## Testing

Import the workflow into n8n, connect an AARI credential, then:

1. Send a webhook payload — expect `decision: ALLOW`, `outcome: SUCCESS`
2. Send the same payload again — expect `decision: BLOCK`, `outcome: BLOCKED`
3. Send a different payload (different `id`) — expect `decision: ALLOW`, `outcome: SUCCESS`

Zero PENDING in normal runs.

## Style rules

- Keep the template **import-safe**: body parameters must use keypair mode (`specifyBody: keypair`), not a raw JSON string. This ensures expressions evaluate automatically after import without manual UI toggles.
- Do not hardcode API keys or credentials.
- The Webhook node must use `responseMode: onReceived` (Respond immediately).
