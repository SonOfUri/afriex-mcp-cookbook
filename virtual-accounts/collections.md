# Virtual account collections

Reconcile incoming bank transfers to virtual accounts and match them to your ledger.

## Use case

Customers send NGN/USD to virtual account numbers you issued. Your platform must:

- Know when money **arrived**
- Match deposits to **customers** and **invoices**
- Handle **partial**, **duplicate**, and **late** transfers

Afriex surfaces collections via **transactions** (channel `VIRTUAL_BANK_ACCOUNT`) and **webhooks** — not by polling account numbers alone. There is no MCP tool to list checkout sessions; reconcile using **`list_transactions`** and **`meta.reference`**.

## Business outcome

A **reconciliation report** matching expected references to transactions, plus a short playbook for webhook-driven production reconciliation.

## Prompt

```
Using Afriex MCP, design a collections reconciliation workflow for virtual account deposits.

Goal: produce a reconciliation report for reference "inv-7842" and document our production playbook.

Context:
- We issue NGN static virtual accounts (label SALES) per customer.
- customerId: [CUSTOMER_ID]
- Expected inbound: 50000 NGN, merchant reference "inv-7842".

Tasks:
1. list_virtual_accounts with currency NGN and customerId — record paymentMethodId and accountNumber.
2. list_transactions with:
   - channel: ["VIRTUAL_BANK_ACCOUNT"]
   - currency: ["NGN"]
   - reference: "inv-7842"
   - fromDate = 7 days ago (ISO 8601)
   - limit 10, page 0
3. For each match, get_transaction and summarize: transactionId, status, type, destinationAmount, meta.reference, createdAt.
4. If no match, list recent NGN transactions with status ["SUCCESS"] (limit 10) for manual matching.
5. Write reconciliation best practices:
   - Store paymentMethodId ↔ customerId in our database
   - Prefer TRANSACTION.UPDATED webhooks in production over polling
   - Use meta.reference on transactions where we control the reference
6. If sandbox has no deposits, explain testing webhook handlers with trigger_webhook event TRANSACTION.UPDATED and entityId = a real transactionId from a prior sandbox txn (development only).

Do not fabricate deposit events.
```

## Expected outcome

The assistant should:

- Connect **virtual accounts** to **transaction list** queries with **array filters**
- Explain **webhook-driven reconciliation** vs polling
- Reference **`meta.reference`** as merchant reconciliation key
- Use **`trigger_webhook`** with **`entityId`** (not resourceId) for sandbox handler testing

## Extensions

- **Pool accounts:** Compare with `get_pool_account` for country-level collection rails (`country: "NG"`).
- **Accounting export:** Ask for CSV column spec from `list_transactions` results.
- **Disputes:** Note statuses like `DISPUTED` / `IN_REVIEW` in monitoring.

## Related

- [Onboarding flow](onboarding-flow.md)
- [Payment status](../payments/payment-status.md)
