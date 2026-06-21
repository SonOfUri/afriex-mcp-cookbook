# Stablecoin settlement

Plan moving value from USDC inflows to local fiat payouts.

## Use case

You collect **USDC** on-chain but pay staff and vendors in **NGN** or **KES**. Settlement planning covers:

1. On-chain **deposit detection** (crypto wallet + transactions)
2. **FX** and balance visibility
3. **SWAP** or accumulated fiat balance
4. **WITHDRAW** to local payment methods

This recipe is a planning prompt — execution steps are separated for safety.

## Business outcome

A numbered **settlement runbook** with a mermaid decision tree your ops team can follow before each payroll run.

## Prompt

```
Using Afriex MCP, outline a USDC → NGN settlement runbook for our payroll product.

Goal: deliver a runbook + mermaid decision tree — plan first, execute only on my approval.

Phase 1 — Observe (development for balances/rates; note production-only steps):
1. get_crypto_wallet asset USDC — note this is production-only; if we are in development, document the limitation instead of faking addresses.
2. get_balance currencies "USD,NGN"
3. get_rates fromSymbols "USD" toSymbols "NGN"

Phase 2 — Plan (no execution):
- How we detect USDC deposits: list_transactions channel ["CRYPTO"] and TRANSACTION.UPDATED webhooks
- When to SWAP USD→NGN vs when NGN is already sufficient
- Preconditions for WITHDRAW: customerId, paymentMethodId, sufficient balance, meta.idempotencyKey + meta.reference

Phase 3 — Execute only if I reply "execute sandbox settlement":
- topup_balance USD if needed (development only)
- create_transaction SWAP: sourceCurrency USD, destinationCurrency NGN, sourceAmount "100", meta { idempotencyKey, reference: "settlement-cookbook-001" }
- get_transaction until terminal status

Output: numbered runbook + mermaid decision tree.
```

## Expected outcome

The assistant should:

- Combine **crypto**, **balance**, **rates**, and **transaction** tools in a **runbook**
- Separate **plan** vs **execute** phases
- Include **mermaid** decision tree
- Respect **SWAP** rules (one amount side, meta recommended)
- Not fabricate crypto wallet addresses in development

## Extensions

- **USDT path:** Duplicate for USDT asset.
- **Multi-country:** Add KES payout leg using `list_institutions` MOBILE_MONEY KE.
- **Fees:** Ask assistant to note FX is informational from `get_rates` only.

## Related

- [USDC](usdc.md)
- [Payouts](../payments/payouts.md)
