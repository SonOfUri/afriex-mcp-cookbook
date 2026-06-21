# OpenClaw — getting started

Connect Afriex MCP to OpenClaw and run your first authenticated tool call.

## Use case

You're building with **OpenClaw** and want Afriex payment tools available to your agent — balances, customers, transactions — without writing API glue code first.

This recipe gets you from zero to a confirmed **`get_balance`** call inside an OpenClaw agent session.

## Business outcome

Working **OpenClaw MCP config** plus a verified balance read — proof that Afriex tools are callable from your agent runtime.

## Prompt

```
Help me connect Afriex MCP to OpenClaw and verify it works.

Goal: working MCP config + confirmed get_balance for USD and NGN.

Context:
- Afriex sandbox API key in environment variable AFRIEX_API_KEY.
- Afriex MCP server: https://mcp.afriex.com/mcp (see https://docs.afriex.com/mcp/connecting)

Tasks:
1. Show OpenClaw MCP server configuration pointing to Afriex MCP with headers:
   - x-afriex-api-key: (from env)
   - x-afriex-environment: development
   Or document authenticate tool as first step if headers are not supported.
2. Explain authenticate(apiKey, environment: "development") if explicit auth is required.
3. Run session_info — report environment, base URL, masked key prefix.
4. get_balance with currencies "USD,NGN" (comma-separated string).
5. Troubleshooting checklist for 401: wrong key, production key on sandbox URL, MCP not restarted after env change.

Keep instructions aligned with https://docs.afriex.com/mcp/connecting. Ask which transport I use (stdio vs HTTP) if config shape depends on it — do not invent install commands.
```

## Expected outcome

The assistant should:

- Reference **official Afriex MCP connection docs**
- Show **`x-afriex-api-key`** and **`x-afriex-environment: development`** headers or **`authenticate`** flow
- Walk through **`session_info`** → **`get_balance`** with comma-separated **`currencies`**
- Include a **401 troubleshooting** checklist

## Extensions

- **Production:** Separate profile with production key and `x-afriex-environment: production`.
- **Secrets:** Use OpenClaw secret store — never commit API keys.
- **Next step:** [Payment agent](payment-agent.md).

## Related

- [First balance check](../getting-started/first-balance.md)
- [Afriex MCP docs](https://docs.afriex.com/mcp/introduction)
