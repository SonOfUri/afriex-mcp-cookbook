# Next.js example

Scaffold a Next.js App Router app with Afriex hosted checkout and a webhook route.

## Use case

You're building a **Next.js 14+** product and want:

- A **“Pay now”** button that creates a checkout session and redirects to Afriex
- A **return URL** page after payment
- An **API route** for webhooks (signature verification in app code — not via MCP)

Use MCP during development to confirm **`create_checkout_session`** fields; implement production calls with the **Afriex HTTP API** or **TypeScript SDK**.

## Business outcome

Key **Next.js files** (`/pay`, `/pay/return`, webhook route, `.env.example`) with server-side checkout creation and raw-body webhook stub.

## Prompt

```
Scaffold a Next.js 14 App Router integration for Afriex hosted checkout.

Goal: minimal working checkout + return page + webhook stub. Use MCP first to validate fields, then generate code.

Product requirements:
- Route /pay — form: amount (NGN major units), customer name, email, phone (+234...)
- On submit: server action or route handler calls Afriex create_checkout_session (via HTTP API in code, not MCP at runtime):
  - amount in kobo (major * 100)
  - currency NGN
  - merchantReference: "nextjs-{uuid}"
  - redirectUrl: https://myapp.com/pay/return
  - customer: { name, email, phone, countryCode: "NG" }
  - channels: ["VIRTUAL_BANK_ACCOUNT", "MOBILE_MONEY"] (MCP-supported channels only)
- Redirect browser to checkoutUrl
- Route /pay/return — show processing copy + merchantReference from query; note payment confirmation via TRANSACTION.UPDATED webhook or list_transactions by reference
- Route app/api/webhooks/afriex/route.ts — POST handler stub:
  - Read raw body as text/bytes (required for signature verification)
  - Read x-webhook-signature header
  - Comment: verify with RSA-SHA256 per https://docs.afriex.com/api-reference/endpoint/webhooks/introduction (or Afriex TS SDK WebhookVerifier)
  - Return 200 on success, 400 on bad signature

Constraints:
- TypeScript, App Router
- AFRIEX_API_KEY server-side only — never expose to client
- Include .env.example

First: call MCP create_checkout_session in development with test data to validate shape, then generate project structure and key files only.
```

## Expected outcome

The assistant should:

- Use MCP to validate **`create_checkout_session`** payload
- Generate **server-side** checkout creation (no leaked API key)
- Implement **redirect** to `checkoutUrl`
- Webhook route reads **raw body** (signature-safe)
- Note **TRANSACTION.UPDATED** for settlement (not CHECKOUT_SESSION.CREATED alone)
- Provide **`.env.example`**

## Extensions

- **Payment status page:** Server-side `list_transactions` by reference on `/pay/return`.
- **TS SDK:** Use `@afriex/sdk` if available in your stack.
- **MCP-only test:** [Payment links](../payments/payment-links.md) for checkout validation in chat.

## Related

- [Payment links](../payments/payment-links.md)
- [FastAPI example](fastapi.md) for webhook verification patterns
