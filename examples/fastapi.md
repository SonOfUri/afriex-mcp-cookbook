# FastAPI example

Scaffold FastAPI with Afriex webhooks, optional Python SDK, and a checkout endpoint.

## Use case

Python teams often want **FastAPI** for:

- **`POST /webhooks/afriex`** — verify signatures, dispatch events
- **`POST /checkout`** — create session, return `checkoutUrl`
- Background tasks for **transaction status** updates

MCP helps explore payloads during design; **afriex-python-sdk** (`WebhookVerifier`, `Afriex` client) implements production calls. MCP cannot verify webhooks.

## Business outcome

A **minimal FastAPI project layout** with checkout router, webhook router (raw-body verify), and typed settings.

## Prompt

```
Build a minimal FastAPI app for Afriex integration.

Goal: checkout endpoint + webhook endpoint with signature verification. Use MCP to confirm shapes, implement with afriex Python SDK.

Endpoints:
1. POST /v1/checkout
   - Body: amount_ngn (float major units), customer { name, email, phone, country_code }
   - Creates checkout session via client.checkout_sessions.create
   - Returns { checkout_url, merchant_reference }

2. POST /webhooks/afriex
   - Read raw request body (bytes) — do NOT parse JSON before verify
   - Header: x-webhook-signature
   - WebhookVerifier(public_key=settings.afriex_webhook_public_key).verify_and_parse
   - Dispatch on event.event: TRANSACTION.UPDATED, CUSTOMER.CREATED, CHECKOUT_SESSION.CREATED
   - Return 200 OK quickly

3. GET /health

Layout: app/main.py, app/config.py, app/routers/checkout.py, app/routers/webhooks.py
Settings: AFRIEX_API_KEY, AFRIEX_WEBHOOK_PUBLIC_KEY, AFRIEX_ENVIRONMENT (staging/production for SDK)

Before coding, use MCP create_checkout_session once in development to validate field names match SDK (merchantReference vs merchant_reference).

Include pytest stub for webhook verify with mocked signature.
```

## Expected outcome

The assistant should:

- Produce **FastAPI project structure** with typed settings
- Use **`WebhookVerifier`** on **raw body**
- Separate **checkout** (SDK) from **webhook** (verifier only)
- Note MCP → SDK field name mapping
- Clarify **TRANSACTION.UPDATED** is the settlement event for orders

## Extensions

- **Async:** Use `AsyncAfriex` with `async def` routes.
- **Idempotency:** Store processed webhook event IDs in Redis/DB.
- **IP allowlist:** Document Afriex webhook IPs from official docs.

## Related

- [Payment links](../payments/payment-links.md)
- [Python scripts](python.md)
