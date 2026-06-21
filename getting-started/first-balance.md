# First balance check

Verify Afriex MCP is connected, authenticated, and returning live wallet data before you build anything else.

## Use case

You just connected Afriex MCP in Cursor (or another client) and need confidence that your API key works, you are on the intended environment, and your business wallet balances are readable.

This is the “hello world” for Afriex MCP — no customers, no transactions, no risk.

## Business outcome

A one-screen **wallet snapshot**: environment confirmation, balance table for USD/NGN/GBP, and a clear pass/fail on MCP connectivity.

## Prompt

```
You have access to Afriex MCP. Help me verify my setup end-to-end.

Goal: confirm MCP auth works and return a wallet snapshot I can paste into Slack.

1. Call session_info — report environment, base URL, and masked API key prefix.
2. If not authenticated, call authenticate with environment "development" (sandbox API).
3. Call get_balance with currencies "USD,NGN,GBP" (comma-separated string).
4. Summarize balances in a markdown table.
5. If all balances are zero and environment is development, call topup_balance (amount 500, currency USD), then get_balance again to confirm the credit.

Rules:
- Use Afriex MCP tools only — do not invent HTTP endpoints or JSON shapes.
- On failure, show the error verbatim and suggest fixes (wrong key, production key on sandbox URL, missing authenticate step).
```

## Expected outcome

The assistant should:

- Confirm **development** (`https://sandbox.api.afriex.com`) or **production** (`https://api.afriex.com`) via `session_info`
- Pass **`currencies`** as a comma-separated string to `get_balance`
- Return a balance map, e.g. `USD: 500.00`, `NGN: 0.00`
- On development with zero balance, successfully **top up** test USD via `topup_balance`
- Avoid inventing HTTP endpoints or JSON shapes

## Extensions

- **Production read-only:** Repeat with `authenticate` `environment: "production"` and skip `topup_balance` (development only).
- **FX context:** Follow up with `get_rates` `fromSymbols: "USD"`, `toSymbols: "NGN,GBP"`.
- **Ops dashboard:** Ask the assistant to scaffold a minimal script that prints balances on a cron schedule using MCP tool calls as reference.

## Related

- [First customer](first-customer.md)
- [Treasury agent](../ai-agents/treasury-agent.md)
