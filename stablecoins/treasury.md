# Treasury (multi-currency + FX)

Snapshot balances and exchange rates for treasury and finance decisions.

## Use case

Treasury needs a quick answer to:

- “How much do we hold in **USD, NGN, GBP, EUR**?”
- “What's today's **USD/NGN** rate?”
- “Do we need a **SWAP** before tomorrow's payout run?”

This recipe combines **`get_balance`** and **`get_rates`** — no transactions unless you explicitly ask. MCP provides **current rates only** — no historical FX or intraday change alerts.

## Business outcome

An **executive treasury snapshot** (balance table, FX table, 3-bullet commentary) suitable for a leadership standup or Slack post.

## Prompt

```
Act as a treasury analyst with Afriex MCP access.

Goal: produce a treasury snapshot for leadership I can paste into our finance Slack channel.

Requirements:
1. authenticate environment "development" unless I specify production.
2. get_balance with currencies "USD,NGN,GBP,EUR" (comma-separated string).
3. get_rates with fromSymbols "USD" and toSymbols "NGN,GBP,EUR,GHS,KES" (comma-separated strings).
4. Present:
   - Balances table (currency → amount)
   - FX table (USD → each target currency rate)
   - updatedAt from rates if returned
5. Brief commentary (3 bullets max):
   - NGN liquidity vs assumed 5M NGN weekly payout need
   - USD coverage for USD-denominated obligations
   - Suggested action: SWAP, topup (development only), or no action

Do not execute SWAP or topup without my confirmation. Do not claim historical rate changes — MCP has no historical FX tool.
```

## Expected outcome

The assistant should:

- Call **`get_balance`** with comma-separated **`currencies`**
- Call **`get_rates`** with comma-separated **`fromSymbols`** / **`toSymbols`**
- Format executive-friendly **tables**
- Give **actionable commentary** without executing trades
- Include rate **timestamp** when returned

## Extensions

- **Execute SWAP:** Follow-up with `create_transaction` type SWAP, one of sourceAmount/destinationAmount only, plus meta.idempotencyKey and meta.reference.
- **Stablecoin view:** Add `get_crypto_wallet` USDC/USDT in production.
- **Scheduled report:** Pair with [Treasury agent](../ai-agents/treasury-agent.md).

## Related

- [USDC wallets](usdc.md)
- [Settlement](settlement.md)
