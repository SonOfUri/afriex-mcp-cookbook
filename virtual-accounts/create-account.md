# Create a virtual account

Mint a virtual bank account for collections — static (labeled) or dynamic (amount-tied).

## Use case

Your business needs a **dedicated account number** so a customer (or your ops team) can fund a wallet via bank transfer. Afriex supports virtual accounts in **USD, NGN, GBP, and EUR**.

You need to choose:

- **Static account** — long-lived, grouped by purpose (`SALES`, `OPERATIONS`, `PAYROLL`, etc.)
- **Dynamic account** — ephemeral, tied to a specific `amount` (for one-time invoices)

Live virtual-account rails are **production-oriented**; development may return errors or empty lists — validate request shapes in development, then smoke-test in production.

## Business outcome

**Customer-facing funding instructions** (account name, number, bank name, `paymentMethodId`) ready to paste into your app or send by email.

## Prompt

```
Using Afriex MCP, design and execute a virtual account collection flow for my business.

Goal: return funding instructions I can display in our subscription billing UI.

Context:
- We collect NGN from Nigerian customers for product subscriptions.
- Customer ID (if we already have one): [CUSTOMER_ID or "create new customer first"]
- We want a STATIC virtual account labeled SALES (not dynamic amount-based).

Steps:
1. authenticate environment "development" if needed.
2. If no customer ID was provided, create_customer with fullName, email (valid), phone in E.164 (+234...), countryCode NG.
3. update_customer_kyc with customerId and kyc object { "BVN": "22222222222", "DATE_OF_BIRTH": "1990-05-15", "COUNTRY": "NG" } — sandbox test values only; production requires real BVN.
4. list_virtual_accounts with currency NGN and customerId — note existing accounts.
5. If none exist for our use case, create_virtual_account with currency NGN, customerId, label SALES. Do NOT pass both label and amount.
6. Return markdown funding instructions: accountName, accountNumber, institution name, paymentMethodId, and a warning to send only NGN to this account.

Explain static vs dynamic tradeoffs in two sentences. Use MCP tools only; cite errors verbatim. If create fails in development, explain production smoke-test next steps.
```

## Expected outcome

The assistant should:

- Create or reuse a **customer**
- Call **`update_customer_kyc`** with **`customerId`** and nested **`kyc`** object
- **List** then **create** a virtual account via MCP
- Return **`accountNumber`**, **`accountName`**, **`institution`**, and **`paymentMethodId`**
- Respect the **label vs amount** mutual exclusivity rule

## Extensions

- **Dynamic invoice VA:** Use `create_virtual_account` with `amount: 50000` (NGN 50,000.00) and no label for a one-time collection window.
- **Business-owner VA:** Omit `customerId` to mint for the business owner instead of an end customer.
- **Multi-currency:** Repeat for USD with label `TREASURY` for treasury segregation.
- **Reconciliation:** Ask how incoming deposits appear in `list_transactions` with `channel: ["VIRTUAL_BANK_ACCOUNT"]` and `currency: ["NGN"]`.

## Related

- [Onboarding flow](onboarding-flow.md)
- [Collections](collections.md)
