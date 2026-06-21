# USDC wallets

Get or create USDC deposit addresses for crypto-native collections.

## Use case

You want customers to fund their balance (or your business treasury) via **USDC** on supported networks. Afriex exposes **`get_crypto_wallet`** per asset — **USDT** and **USDC** only.

Typical product patterns:

- Display a **deposit address** on a “Add funds” screen
- Operate **treasury** with on-chain inflows reconciled in Afriex

**Important:** `get_crypto_wallet` is **production-only** per MCP. It will fail in development — plan a production smoke test for live addresses.

## Business outcome

A **deposit address card** per network (address + network label + UX warnings) for business and/or customer-scoped wallets.

## Prompt

```
Using Afriex MCP, help me set up USDC deposit addresses for our collections flow.

Goal: produce a deposit address card for our "Add funds" UI (business + optional customer scope).

Business context:
- Cross-border payroll platform
- Business-level USDC wallet (no customerId) AND customer-level wallet for customerId "[CUSTOMER_ID or omit]"
- Asset: USDC only

Steps:
1. authenticate environment "production" — get_crypto_wallet is production-only.
2. get_crypto_wallet with asset USDC and no customerId. List every address and its network from the response.
3. If customerId provided, repeat get_crypto_wallet with asset USDC and that customerId.
4. For each address, specify which network (e.g. ETHEREUM, TRON) and warn users to send only USDC on the matching network.
5. Describe reconciliation: list_transactions with channel ["CRYPTO"] and/or TRANSACTION.UPDATED webhooks (Dashboard-configured URL).

Do not generate fake addresses — only report MCP/API responses. If development environment is active, explain why the call fails and outline production smoke-test steps.

Add a short compliance & UX note: show asset, network, and any memo/tag fields from the response.
```

## Expected outcome

The assistant should:

- Call **`get_crypto_wallet`** with `asset: "USDC"` in **production**
- Return **`addresses[]`** with **`address`** and **`network`** per chain
- Differentiate **business vs customer-scoped** wallets
- Outline **reconciliation** via `list_transactions` or webhooks
- Handle **development environment limitation** honestly

## Extensions

- **USDT parity:** Duplicate for `asset: "USDT"`.
- **Treasury view:** Combine with `get_balance` `currencies: "USD,NGN"` and `get_rates` — see [treasury.md](treasury.md).
- **Payout after collection:** Chain to `create_transaction` WITHDRAW once fiat balance is available.

## Related

- [Settlement](settlement.md)
- [Payment links](../payments/payment-links.md)
