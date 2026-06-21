# Payment status tracking

Find and monitor payments by merchant reference, transaction ID, or status filters.

## Use case

Support, finance, and engineering teams need answers to:

- “Did checkout **order-2026-001** settle?”
- “What's the status of transaction **6a371…**?”
- “Show all **FAILED** withdrawals in the last 24 hours.”

MCP exposes **`get_transaction`** and **`list_transactions`** with rich filters. There is no tool to look up checkout sessions by ID — use **`reference`** (merchant reference / `meta.reference`) instead.

## Business outcome

A **markdown investigation report** with status, amounts, recommended actions per transaction, and PII masked for sharing.

## Prompt

```
Using Afriex MCP, help me investigate payment status.

Goal: produce a markdown report I can paste into Slack or a support ticket.

Scenario A — known merchant reference:
- reference: "order-pro-monthly-abc123"
- list_transactions with reference filter, limit 10, page 0
- For each match, get_transaction — summarize status, type, channel, amounts, currencies, createdAt, meta

Scenario B — known transaction ID:
- transactionId: [PASTE_ID]
- get_transaction only; explain if NOT_FOUND

Scenario C — ops sweep (last 24h):
- list_transactions with fromDate/toDate (ISO 8601), status ["FAILED","REJECTED","CUSTOMER_ACTION_REQUIRED"], limit 20, page 0
- Table: transactionId | reference | status | type | amounts

For each scenario, add recommended next actions (retry, contact customer, check balance, etc.).

Rules: MCP tools only; mask customer PII; do not invent transactions; status filter must be an array.
```

## Expected outcome

The assistant should:

- Demonstrate **`list_transactions`** filters: `reference`, `status` (array), `fromDate`, `toDate`
- Use **`get_transaction`** for detail
- Map statuses to **plain-language actions**
- Note **`limit`** max 50 per page
- Produce a **markdown report** suitable for Slack or email

## Extensions

- **Webhook-first:** Note that production apps should prefer `TRANSACTION.UPDATED` over polling.
- **Checkout sessions:** Cross-reference `merchantReference` from `create_checkout_session` via `list_transactions` reference filter.
- **Pagination:** Request page 1, 2 if results exceed `limit`.

## Related

- [Payment links](payment-links.md)
- [Support agent](../ai-agents/support-agent.md)
