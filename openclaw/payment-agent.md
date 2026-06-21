# OpenClaw payment agent

Define an OpenClaw agent that monitors Afriex balances and drafts payout actions for human approval.

## Use case

You want a **persistent payment operations agent** in OpenClaw that:

- Runs on a **schedule** (e.g. every morning)
- Checks **balances** and **failed transactions**
- **Drafts** payout or top-up actions but **never executes** without explicit human approval

This pattern keeps autonomous agents useful without unattended money movement.

## Business outcome

An **OpenClaw agent definition** (system prompt, tool policy, schedule) plus a sample ops report from live MCP data.

## Prompt

```
Design an OpenClaw agent named "afriex-ops" that uses Afriex MCP tools.

Goal: agent spec + sample first-run ops report — no unattended money movement.

Agent charter:
- Role: Payment operations assistant for a fintech startup.
- Environment: development unless x-afriex-environment or authenticate sets production.
- Allowed MCP tools: session_info, get_balance, get_rates, list_transactions, list_customers, topup_balance, create_transaction, create_payment_method — money movement only after human sends "APPROVE".

Behavior (each run):
1. Produce ops report (markdown) like the operations-agent cookbook recipe:
   - get_balance currencies "USD,NGN,GBP"
   - list_transactions status ["FAILED","PENDING"], last 48h, limit 20
   - get_rates fromSymbols "USD" toSymbols "NGN"
2. If NGN < 1_000_000 AND USD > 200, draft SWAP create_transaction payload (USD→NGN, sourceAmount "100", meta idempotencyKey + reference) under "## Pending approval" — DO NOT call create_transaction.
3. If user messages "APPROVE" + draft reference, execute only that drafted call.

Deliverables:
- OpenClaw agent definition (system prompt + tool allowlist + schedule e.g. cron "0 8 * * *")
- Example first-run ops report structure (populate with live MCP if connected)
- Safety rules: max single payout amount, no delete_customer, mask PII

Reference exact Afriex MCP tool names and parameter shapes from https://docs.afriex.com/mcp/introduction.
```

## Expected outcome

The assistant should:

- Output an **OpenClaw agent spec**: system prompt, tool policy, schedule
- Embed the **operations checklist** from [Operations agent](../ai-agents/operations-agent.md)
- Enforce **APPROVE gate** before `create_transaction` / `topup_balance`
- Include **safety limits** and **PII masking**
- Use correct MCP parameter conventions

## Extensions

- **Slack/Telegram:** Add OpenClaw channel config to deliver ops report.
- **Payout drafts:** Extend to draft WITHDRAW payloads using [Payouts](../payments/payouts.md).
- **Webhooks:** Note Dashboard webhook config for production — MCP cannot verify deliveries.

## Related

- [Operations agent](../ai-agents/operations-agent.md)
- [Treasury agent](../ai-agents/treasury-agent.md)
