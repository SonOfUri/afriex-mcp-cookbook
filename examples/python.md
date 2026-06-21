# Python scripts example

CLI and cron-friendly automation — MCP for exploration, SDK for execution.

## Use case

Ops and backend devs need **scripts**, not web apps:

- Daily **balance report** emailed or posted to Slack
- **Batch export** of transactions to CSV
- One-off **development top-up** before demo

**Pattern:** use **Afriex MCP in Cursor** to figure out filters and fields, then implement a **Python script** with `afriex` SDK for repeatable runs.

## Business outcome

An **`afriex_ops.py` CLI** with `balances`, `transactions-export`, and `topup` subcommands plus a cron-ready README snippet.

## Prompt

```
Create a Python ops CLI using the afriex SDK (not raw HTTP). Use Afriex MCP as reference for correct query parameters.

Goal: afriex_ops.py with three subcommands ready for cron.

Script: afriex_ops.py (click or argparse):

1. balances
   - Lists USD, NGN, GBP balances (table to stdout)

2. transactions-export
   - Args: --days 7 --status FAILED,PENDING --output txns.csv
   - Paginates list_transactions (page 0+, limit 50 max per page) until done or max 500 rows
   - CSV: transactionId, status, type, reference, sourceAmount, sourceCurrency, destinationAmount, destinationCurrency, createdAt

3. topup (development/sandbox only)
   - Args: --amount 500 --currency USD
   - Refuses if AFRIEX_ENVIRONMENT is production
   - Calls balances.topup_sandbox

Requirements:
- Load .env: AFRIEX_API_KEY, AFRIEX_ENVIRONMENT=staging (SDK) — maps to development/sandbox API
- Use Afriex() context manager
- Dependencies: afriex, python-dotenv

Comment block at top mapping MCP tools → SDK methods:
- get_balance → client.balances.get
- list_transactions → client.transactions.list (status as list)
- topup_balance → client.balances.topup_sandbox

README snippet for cron: "0 7 * * * /path/venv/bin/python afriex_ops.py balances"
```

## Expected outcome

The assistant should:

- Deliver a **working CLI skeleton** with three subcommands
- Map **MCP tools → SDK methods** in comments
- Implement **pagination** with limit ≤ 50
- Guard **topup** to non-production only
- Note SDK `staging` vs MCP `development` naming

## Extensions

- **Async version:** `AsyncAfriex` for high-volume export.
- **MCP-only mode:** Run [Operations agent](../ai-agents/operations-agent.md) ad hoc in Cursor without a script.
- **CI:** GitHub Actions with `AFRIEX_API_KEY` secret — no `.env` in repo.

## Related

- [Operations agent](../ai-agents/operations-agent.md)
- [Payment status](../payments/payment-status.md)
