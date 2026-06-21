# Virtual account onboarding flow

End-to-end: create customer → KYC → virtual account → customer-facing funding instructions.

## Use case

You're launching a **collections product** where each end-user gets a dedicated bank account number. The onboarding flow must:

1. Register the user as an Afriex **customer**
2. Submit **KYC** (BVN required for NGN static VAs in production)
3. Mint a **static** virtual account (e.g. label `SALES`)
4. Produce **copy** for your UI: account name, number, bank name

This recipe chains MCP tools in the correct order.

## Business outcome

A complete **onboarding packet**: `customerId`, KYC confirmation, virtual account details, and user-ready funding instructions for your product UI.

## Prompt

```
Using Afriex MCP, implement a complete virtual account onboarding flow for a Nigerian SMB customer.

Goal: deliver an onboarding packet I can hand to frontend to render a "Fund your account" screen.

End-user profile:
- fullName: Amaka Nwosu
- email: amaka.nwosu+[UNIQUE]@example.com
- phone: +2348081950789
- countryCode: NG

Flow:
1. authenticate environment "development" if needed.
2. create_customer with meta { "onboardingStep": "started" }
3. update_customer_kyc with customerId from step 2 and kyc { "BVN": "22222222222", "DATE_OF_BIRTH": "1992-03-10", "COUNTRY": "NG" } (sandbox test values — note production uses real BVN)
4. list_virtual_accounts with currency NGN and customerId
5. If no suitable VA exists, create_virtual_account: currency NGN, customerId, label SALES (static — no amount field)
6. Build "funding instructions" markdown:
   - Account name, account number, bank / institution name
   - Warning: send only NGN to this account
   - paymentMethodId for internal reference
   - customerId for our database mapping

Explain production vs development limitations for virtual accounts. If create fails with VIRTUAL_ACCOUNT_LIMIT_REACHED, list existing VAs and recommend reuse.
```

## Expected outcome

The assistant should:

- Chain **create → KYC → list → create** virtual account
- Use **`update_customer_kyc(customerId, kyc: {...})`** — not flat KYC fields
- Output **user-ready funding instructions**
- Handle **limit reached** and **BVN** requirements
- Never pass both **label** and **amount** on create

## Extensions

- **Dynamic VA for invoices:** Second account with `amount` instead of `label` for one-time payment.
- **Webhook:** Mention `PAYMENT_METHOD.CREATED` and `TRANSACTION.UPDATED` when funds arrive (handler configured in Dashboard).
- **App scaffold:** Ask for a React component that displays the funding instructions block.

## Related

- [Create account](create-account.md)
- [Collections](collections.md)
