# First customer

Create a production-valid Afriex customer via MCP — the prerequisite for payouts, virtual accounts, and checkout.

## Use case

Before you can pay out, collect into a customer-scoped virtual account, or attach payment methods, you need a **customer** record with:

- `fullName`
- Valid `email`
- `phone` in **E.164** format (e.g. `+2348081950123`)
- `countryCode` as ISO 3166-1 alpha-2 (e.g. `NG`)

This recipe validates MCP customer creation and retrieval without moving money.

## Business outcome

A reusable **`customerId`** plus a verification checklist confirming the record exists and matches your input — ready for payouts or virtual account recipes.

## Prompt

```
Using Afriex MCP, create and verify a new customer for our sandbox integration test.

Goal: return a customerId we can reuse in payout and virtual-account recipes.

Customer details:
- fullName: Chidi Okafor
- email: chidi.okafor+[UNIQUE]@example.com  (append a short random suffix)
- phone: +2348081950456
- countryCode: NG
- meta: { "source": "mcp-cookbook", "segment": "smb" }

Steps:
1. authenticate with environment "development" if session_info shows no active key.
2. create_customer with the fields above.
3. get_customer using the returned customerId — confirm name, email, phone, countryCode match.
4. list_customers with email filter set to the exact email — confirm exactly one match.
5. Summarize validation rules observed (E.164 phone, uppercase countryCode).

Do not call delete_customer unless I ask. Return customerId prominently at the top of your reply.
```

## Expected outcome

The assistant should:

- Successfully call **`create_customer`** and **`get_customer`**
- Return a **`customerId`** (24-char hex ObjectId in API responses)
- Confirm **`list_customers`** email filter works
- Note **PHONE_COUNTRY_MISMATCH** or validation errors if phone/country don't align

## Extensions

- **KYC for NGN virtual accounts:** Follow with `update_customer_kyc(customerId, kyc: { "BVN": "22222222222", "DATE_OF_BIRTH": "1990-05-15", "COUNTRY": "NG" })` before [Create virtual account](../virtual-accounts/create-account.md).
- **US customer:** Repeat with `countryCode: US` and `phone: +12025550123`.
- **Cleanup:** `delete_customer` when done testing in development.

## Related

- [First transaction](first-transaction.md)
- [Virtual account onboarding](../virtual-accounts/onboarding-flow.md)
