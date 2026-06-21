# Operations agent

An AI agent prompt for daily payment operations — balances, stuck transactions, and sandbox health checks.

## Use case

You're a fintech ops lead (or a solo founder wearing that hat). Every morning you need to know:

- Do we have enough **USD/NGN** liquidity?
- Are any transactions **stuck** in PENDING or PROCESSING?
- Did any **FAIL** overnight?
- Is MCP / API connectivity healthy?

Instead of clicking through dashboards, you run one **operations agent** prompt in Cursor or OpenClaw with Afriex MCP enabled.

## Business outcome

A **daily ops report** (markdown) with balance table, alerts, failed-transaction detail, and FX snapshot — ready to post to Slack.

## Prompt

```
You are my Afriex payment operations agent. You have Afriex MCP tools.

Goal: produce today's ops report in markdown for our #payments Slack channel.

Environment: development unless I say production.

Checklist:
1. session_info — confirm environment and that credentials work (mask API key).
2. get_balance with currencies "USD,NGN,GBP,EUR". Flag thresholds:
   - USD < 100
   - NGN < 500000
3. list_transactions with limit 20, page 0, status ["PENDING","PROCESSING","FAILED"], fromDate = 48 hours ago (ISO 8601).
4. For each FAILED transaction, summarize transactionId, type, source/destination currencies, amounts, meta.reference.
5. list_customers with limit 5 — verify API health only; mask emails (a***@domain.com).
6. get_rates with fromSymbols "USD" toSymbols "NGN,GBP" — include updatedAt if returned.

Output format:
## Ops summary — [date]
- Environment
- Balances table
- Alerts (threshold breaches, failed tx count)
- Failed transactions detail (if any)
- FX snapshot
- Recommended actions (e.g. topup_balance on development, investigate reference X)

Rules:
- MCP tools only. Never fabricate transaction IDs.
- On tool failure, show error and continue checklist.
- Do not execute topup_balance or create_transaction unless I say "execute fixes".
```

## Expected outcome

The assistant should:

- Produce a structured **markdown ops report**
- Use comma-separated strings for **`get_balance`** and **`get_rates`**
- Use **array** for **`list_transactions` status** filter
- **Flag** liquidity and failure conditions with actionable next steps
- **Mask PII** in summaries
- **Not mutate** money movement without explicit user approval

## Extensions

- **Production mode:** Rerun with `authenticate` `environment: "production"` and stricter thresholds.
- **Webhook failures:** Add “verify webhook URL in Afriex Dashboard” as a manual step (MCP cannot read delivery logs).
- **Auto-remediation (development):** Follow-up: “For failed tx with reference containing 'retry-me', draft replacement WITHDRAW payload” — human executes.
- **Schedule:** Wrap in OpenClaw — see [Payment agent](../openclaw/payment-agent.md).

## Related

- [First balance check](../getting-started/first-balance.md)
- [Treasury agent](treasury-agent.md)
- [Support agent](support-agent.md)
