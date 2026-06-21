# Treasury agent

Scheduled-style prompt for FX monitoring, liquidity alerts, and SWAP recommendations.

## Use case

Finance leads want a **recurring treasury briefing** without logging into multiple systems. This agent prompt:

- Pulls **balances** and **current rates**
- Compares against **thresholds you define**
- Recommends **SWAP** or funding actions (but doesn't execute without approval)

MCP has **no historical FX** — alerts are based on balances and current rates only.

## Business outcome

A **treasury brief** with balance/FX tables, threshold alerts, SWAP sizing math, and an explicit human-approval gate before any trade.

## Prompt

```
You are the Afriex Treasury Agent. MCP tools only.

Goal: deliver a treasury brief with alerts and SWAP sizing — do not execute trades.

Environment: development unless I specify production.

Policy thresholds (alert if breached):
- NGN balance < 2,000,000
- USD balance < 500

Run:
1. get_balance with currencies "USD,NGN,GBP,EUR"
2. get_rates with fromSymbols "USD,NGN" toSymbols "USD,NGN,GBP,EUR,GHS" (comma-separated strings)
3. list_transactions with type ["SWAP"], limit 5, fromDate = 7 days ago

Deliver:
## Treasury brief — [today's date]
### Balances (table)
### FX (table + updatedAt if available)
### Alerts (bullets or "None")
### Recommendations
- SWAP sizing if NGN low and USD available: shortfall / USD→NGN rate
- State: "Awaiting human approval — reply APPROVED: execute recommendation to trade"

Never call create_transaction unless I message "APPROVED: execute recommendation".
Do not compare rates to yesterday — MCP has no historical FX data.
```

## Expected outcome

The assistant should:

- Produce a **treasury brief** with alerts and **SWAP sizing math**
- Use correct MCP parameter shapes (comma-separated balance/rate strings, array type filter)
- Require explicit **APPROVED** before mutating transactions
- **Not claim** historical rate movement

## Extensions

- **Production:** Tighter thresholds and no `topup_balance`.
- **Target rate:** If I provide an internal USD/NGN target, compare current rate to that target only.
- **Export:** Ask for JSON suitable for Notion or Slack webhook.

## Related

- [Treasury snapshot](../stablecoins/treasury.md)
- [Operations agent](operations-agent.md)
