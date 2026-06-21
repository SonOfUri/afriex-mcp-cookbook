# Payment links (hosted checkout)

Create a hosted checkout session so customers pay without you building a full payment UI.

## Use case

You want to send a customer to an **Afriex-hosted checkout page** to pay a fixed amount — ideal for invoices, payment links in email/SMS, or MVP products without embedding rails.

Checkout sessions:

- Use amounts in **minor units** (e.g. `1500000` = ₦15,000.00 NGN)
- Require a unique **`merchantReference`** per session
- Return a **`checkoutUrl`** to redirect the customer
- MCP supports channels **`VIRTUAL_BANK_ACCOUNT`** and **`MOBILE_MONEY`** only (no CARD in MCP `create_checkout_session`)

There is **no MCP tool to fetch checkout session status** — track payment via `list_transactions` filtered by `reference` (maps to `meta.reference`) or `TRANSACTION.UPDATED` webhooks.

## Business outcome

A **shareable checkout URL** plus integration notes: redirect flow, webhook events, and how to confirm payment settled.

## Prompt

```
Using Afriex MCP, create a hosted checkout payment link for a one-time purchase.

Goal: return a checkoutUrl I can embed in a "Pay now" email and document how we know when payment settles.

Order details:
- Product: "Pro Plan — Monthly"
- Amount: 15000 NGN (1500000 in minor units / kobo)
- merchantReference: "order-pro-monthly-[UNIQUE_ID]" (generate unique)
- redirectUrl: https://myapp.com/checkout/success
- customer: { name: "Ada Okonkwo", email: "ada@example.com", phone: "+2348081950123", countryCode: "NG" }
- channels: ["VIRTUAL_BANK_ACCOUNT", "MOBILE_MONEY"]
- metadata: { "plan": "pro", "source": "cookbook-demo" }

Steps:
1. create_checkout_session with the above (MCP tool — use exact field names).
2. Return checkoutUrl and one-paragraph redirect instructions for Next.js or Laravel.
3. Explain webhooks:
   - CHECKOUT_SESSION.CREATED fires when the session is created (trigger_webhook uses entityId = checkout session UUID)
   - TRANSACTION.UPDATED fires when payment settles — this is what we use to mark orders paid
   - Webhook URL must be configured in Afriex Dashboard (MCP cannot set it)
4. Explain how to confirm payment via list_transactions with reference = merchantReference after customer pays.

Do not invent checkout session GET endpoints — they are not MCP tools.
```

## Expected outcome

The assistant should:

- Call **`create_checkout_session`** with valid minor-unit **amount**, **currency**, **merchantReference**, **redirectUrl**, and **customer**
- Return a usable **`checkoutUrl`**
- Distinguish **CHECKOUT_SESSION.CREATED** vs **TRANSACTION.UPDATED** for settlement
- Mention **webhook setup** in the dashboard and **`list_transactions`** for status lookup

## Extensions

- **USD checkout:** Change currency to USD and amount to cents (e.g. $20.00 → `2000`).
- **Payment status:** Follow [payment-status.md](payment-status.md) to query `list_transactions` by reference.
- **Sandbox webhook test:** `trigger_webhook` with `event: "CHECKOUT_SESSION.CREATED"` and `entityId` = session UUID (development only).

## Related

- [Payment status](payment-status.md)
- [Next.js example](../examples/nextjs.md)
