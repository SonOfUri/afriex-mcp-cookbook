# Laravel example

PHP/Laravel patterns for Afriex checkout, webhooks, and reconciliation.

## Use case

Laravel teams need:

- A **controller** to start hosted checkout
- A **webhook controller** with raw body signature verification
- **Config** via `.env` (`AFRIEX_API_KEY`, webhook public key)
- **Queue jobs** for async webhook processing

MCP validates JSON shapes during design; implementation uses **HTTP client** (Guzzle) matching [Afriex API docs](https://docs.afriex.com). MCP cannot verify webhooks or list checkout sessions.

## Business outcome

**Laravel module skeleton**: config, `AfriexClient` service, checkout + webhook controllers, routes, and `.env.example`.

## Prompt

```
Design a Laravel 11 integration for Afriex Business API.

Goal: checkout redirect + webhook handler with raw-body signature verify. Use MCP to confirm create_checkout_session fields first.

Deliver:

1. config/afriex.php
   - api_key, base_url (sandbox.api.afriex.com vs api.afriex.com from AFRIEX_ENVIRONMENT), webhook_public_key

2. App\Services\AfriexClient
   - Guzzle wrapper: getBalance(currencies string), createCheckoutSession(array), listTransactions(array)
   - Throws AfriexApiException on 4xx/5xx

3. App\Http\Controllers\CheckoutController
   - POST /checkout — validates Request, createCheckoutSession, redirects()->away($checkoutUrl)
   - channels: VIRTUAL_BANK_ACCOUNT, MOBILE_MONEY (MCP-supported channels)

4. App\Http\Controllers\AfriexWebhookController
   - POST /webhooks/afriex
   - $request->getContent() for RAW body (not $request->all())
   - verifySignature($signature, $rawBody, publicKey) RSA-SHA256 per Afriex docs
   - Dispatch ProcessAfriexWebhook job — handle TRANSACTION.UPDATED for order settlement
   - Return 200 within 5 seconds

5. routes/web.php and routes/api.php snippets
6. .env.example

PHPUnit sketch: invalid signature → 400.

Comment MCP tool → API path mapping (e.g. create_checkout_session → POST checkout session endpoint).
```

## Expected outcome

The assistant should:

- Provide **Laravel-idiomatic** structure (config, service, controllers, job)
- Emphasize **`getContent()`** for webhook raw body
- Document **RSA-SHA256** verification
- Map endpoints to **official API paths**
- Note **TRANSACTION.UPDATED** for payment confirmation

## Extensions

- **Sanctum API:** Expose `/api/v1/internal/balances` for admin dashboard.
- **Webhook IP allowlist:** Middleware note for Afriex IPs from docs.
- **Reconciliation:** Use `listTransactions` with reference filter (maps to MCP list_transactions).

## Related

- [Payment links](../payments/payment-links.md)
- [Collections](../virtual-accounts/collections.md)
