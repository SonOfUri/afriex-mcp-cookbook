# First transaction

Execute a sandbox payout (WITHDRAW) from USD balance to a Nigerian bank account using MCP.

## Use case

You've created a customer and need to prove the full **money-movement** path:

1. Ensure development **USD** balance (`topup_balance`)
2. Register a **payment method** (bank account)
3. Create a **WITHDRAW** transaction with idempotent metadata
4. Track status until SUCCESS (development settles in ~1–2 minutes)

This is the core payout rail most fintech products build on.

## Business outcome

A **payout receipt** with `customerId`, `paymentMethodId`, and `transactionId`, plus status and sandbox settlement notes your team can use as an integration checklist.

## Prompt

```
Using Afriex MCP, run a complete development-environment WITHDRAW (payout) test.

Goal: produce a payout receipt with all IDs and initial transaction status.

Inputs:
- customerId: [PASTE_CUSTOMER_ID or create_customer first per first-customer recipe]
- Payout: 5000 NGN destination amount
- Source currency: USD (debited from Afriex wallet)
- Bank: Zenith Bank Nigeria — institutionCode "000015", accountNumber "0123456789", accountName matching customer name, countryCode NG, channel BANK_ACCOUNT

Steps:
1. authenticate environment "development" if needed.
2. get_balance with currencies "USD". If below 50 USD, topup_balance amount 100 currency USD.
3. create_payment_method:
   - channel BANK_ACCOUNT, customerId, type WITHDRAW
   - accountName, accountNumber, countryCode NG
   - institution: { "institutionCode": "000015" }
4. create_transaction:
   - customerId, type WITHDRAW
   - sourceCurrency USD, destinationCurrency NGN
   - destinationAmount "5000"
   - destinationId = paymentMethodId from step 3
   - meta: { "idempotencyKey": "<new UUID>", "reference": "cookbook-first-txn-success" }
5. get_transaction for the returned transactionId and report status.

Explain sandbox behavior: reference containing "fail" forces FAILED; otherwise expect SUCCESS after ~1–2 minutes and a TRANSACTION.UPDATED webhook to your dashboard-configured URL.

Use MCP tools only. Show all IDs at the top of your reply.
```

## Expected outcome

The assistant should:

- Call **`topup_balance`** if needed (development only)
- Create **`create_payment_method`** with nested **`institution`** object, then **`create_transaction`** (WITHDRAW)
- Return **`transactionId`** and initial status (often `PENDING` / `PROCESSING`)
- Include **`meta.idempotencyKey`** and **`meta.reference`** on the transaction
- Mention **webhook** `TRANSACTION.UPDATED` for async status (configured in Dashboard, not MCP)

## Extensions

- **Failure testing:** Repeat with `meta.reference: "cookbook-fail-test"` to trigger FAILED in development.
- **SWAP instead:** `type: SWAP`, USD→NGN, set only `sourceAmount` OR `destinationAmount` (not both), plus `meta.idempotencyKey` and `meta.reference`.
- **DEPOSIT:** Requires `sourceId` on a pull-capable payment method (`type: DEPOSIT` on payment method).

## Related

- [Payouts](../payments/payouts.md)
- [Payment status](../payments/payment-status.md)
