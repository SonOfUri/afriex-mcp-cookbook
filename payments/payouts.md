# Payouts

Production-oriented prompt for bank and mobile-money payouts with institution resolution and idempotency.

## Use case

Your product pays contractors, vendors, or remittance recipients across Africa. Each payout needs:

- A verified **customer** (or business wallet context)
- A **payment method** (destination)
- A **WITHDRAW** transaction with unique **idempotencyKey** and merchant **reference**

This recipe covers **BANK_ACCOUNT** in Nigeria and shows how to extend to **MOBILE_MONEY**.

## Business outcome

A **payout receipt** (`transactionId`, status, `paymentMethodId`, reference) plus idempotency retry guidance your finance team can audit.

## Prompt

```
You are helping me implement payouts with Afriex MCP. Environment: development (authenticate if needed).

Goal: execute one sandbox payout and return a payout receipt with idempotency notes.

Payout request:
- customerId: [CUSTOMER_ID]
- Send 25000 NGN to recipient
- Source: USD wallet (WITHDRAW — Afriex computes USD debit from destinationAmount)
- Destination: BANK_ACCOUNT in NG
- accountNumber: 2208707402
- accountName: Timothy Alo
- institutionCode: 000015 (Zenith Bank)
- meta.reference: "payout-inv-2026-001"
- meta.idempotencyKey: <generate new UUID>

Steps:
1. resolve_payment_method with channel BANK_ACCOUNT, accountNumber, institutionCode "000015", countryCode NG — note resolved account name if returned.
2. create_payment_method if no reusable destination exists:
   - channel BANK_ACCOUNT, customerId, type WITHDRAW, accountName, accountNumber, countryCode NG
   - institution: { "institutionCode": "000015" }
3. get_balance currencies "USD" — topup_balance if insufficient (development only).
4. create_transaction WITHDRAW with destinationId, destinationAmount "25000", destinationCurrency NGN, sourceCurrency USD, and meta.reference + meta.idempotencyKey.
5. Return payout receipt: transactionId, status, paymentMethodId, reference.

Also document:
- Retry with SAME idempotencyKey vs NEW key
- Expanding to Kenya MOBILE_MONEY: list_institutions channel MOBILE_MONEY countryCode KE

Do not duplicate payouts if I paste this prompt twice with the same idempotencyKey — explain what Afriex returns on replay.
```

## Expected outcome

The assistant should:

- Use **`resolve_payment_method`** / **`list_institutions`** where appropriate
- Create **`create_payment_method`** with nested **`institution`** object
- Execute **`create_transaction`** WITHDRAW with proper **meta**
- Explain **idempotency** behavior on retry
- Output a concise **payout receipt**

## Extensions

- **Mobile money (KE):** `channel: MOBILE_MONEY`, `list_institutions` for KE, phone as `accountNumber`.
- **Bulk CSV:** Ask for a batch script outline (human approval before each `create_transaction`).
- **Compliance:** Add `meta.narration` and `meta.invoice` for high-value payouts.

## Related

- [First transaction](../getting-started/first-transaction.md)
- [Payment status](payment-status.md)
