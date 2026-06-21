# Afriex MCP Prompt Cookbook

Production-quality prompts and recipes for building with the [Afriex Business API](https://docs.afriex.com) through **MCP** (Model Context Protocol) in AI-native development tools.

This is **not** an SDK. This is **not** an application. It is a curated library of copy-paste prompts that help you and your AI assistant ship real payment workflows faster.

---

## What is Afriex MCP?

Afriex MCP exposes the Afriex Business API as tools your AI assistant can call — create customers, check balances, initiate transactions, manage virtual accounts, and more — without you writing raw HTTP requests in chat.

Available MCP tools (26):

| Category | Tools |
| -------- | ----- |
| Session | `authenticate`, `session_info` |
| Balances | `get_balance`, `topup_balance` (development only) |
| Customers | `create_customer`, `get_customer`, `list_customers`, `update_customer_kyc`, `delete_customer` |
| Transactions | `create_transaction`, `get_transaction`, `list_transactions` |
| Virtual accounts | `create_virtual_account`, `list_virtual_accounts` |
| Payment methods | `create_payment_method`, `get_payment_method`, `list_payment_methods`, `delete_payment_method`, `resolve_payment_method` |
| Institutions | `list_institutions`, `resolve_institution_codes` |
| Treasury | `get_rates`, `get_pool_account`, `get_crypto_wallet` (production only) |
| Checkout | `create_checkout_session` |
| Webhooks | `trigger_webhook` (development only; no verify tool — verify in app code) |

**Not exposed via MCP:** listing checkout sessions, webhook signature verification, historical FX rates.

### MCP parameter conventions

Recipes assume these shapes (see [official MCP docs](https://docs.afriex.com/mcp/introduction)):

- **Environment:** `authenticate` uses `"development"` (sandbox API) or `"production"`. HTTP header: `x-afriex-environment`.
- **`get_balance`:** `currencies: "USD,NGN,GBP"` (comma-separated string).
- **`get_rates`:** `fromSymbols: "USD"`, `toSymbols: "NGN,GBP,EUR"` (comma-separated strings).
- **`list_transactions` / `list_customers`:** `limit` max **50**; `status`, `type`, `channel`, and `currency` filters are **arrays**.
- **`update_customer_kyc`:** requires `customerId` and a `kyc` object (e.g. `{ "BVN": "...", "DATE_OF_BIRTH": "...", "COUNTRY": "NG" }`).
- **`create_payment_method`:** requires an `institution` object with at least `institutionCode` or `institutionName`.
- **`trigger_webhook`:** requires `event` and `entityId` (transaction/customer/payment-method ObjectId, or checkout session UUID).

Connect MCP in [Cursor](https://docs.afriex.com/mcp/connecting), Claude Desktop, Claude Code, OpenClaw, VS Code Agent Mode, or any MCP-compatible client. See the [MCP introduction](https://docs.afriex.com/mcp/introduction).

---

## Who is this cookbook for?

- Developers new to Afriex or MCP
- AI-native builders using Cursor, Claude Code, or OpenClaw
- Fintech engineers prototyping collections, payouts, and treasury flows
- Founders and agent developers automating payment operations

---

## How to use these prompts

1. **Pick a recipe** from the categories below.
2. **Copy the prompt** block verbatim (or adapt placeholders like `YOUR_BUSINESS`).
3. **Paste into your MCP-enabled AI client** with Afriex MCP connected and authenticated.
4. **Review generated code** before running in production — you stay in control.

**Tips:**

- Use a **development (sandbox)** API key until flows are validated end-to-end.
- Ask the assistant to **call Afriex MCP tools** rather than guessing API shapes.
- For webhook handlers, configure your callback URL in the Afriex Dashboard — MCP cannot set webhook URLs or verify signatures.
- **Production-only rails:** `get_crypto_wallet` and live virtual-account rails may fail in development; plan production smoke tests for those flows.

---

## Recommended AI tools

| Tool | Best for |
| ---- | -------- |
| [Cursor](https://cursor.com) | Full-stack implementation in your repo |
| [Claude Code](https://docs.anthropic.com/en/docs/claude-code) | Terminal-native agent workflows |
| [OpenClaw](https://docs.afriex.com) | Dedicated payment / ops agents |
| VS Code Agent Mode | Lightweight MCP experiments |
| Windsurf | MCP-enabled editing |

Setup guide: [Connecting MCP clients](https://docs.afriex.com/mcp/connecting)

---

## Recipe status

| Status | Recipes |
| ------ | ------- |
| ✅ Complete | **All recipes** (getting started through examples) |

Contributors: copy [`_template.md`](_template.md) for new recipes.

---

## Prompt categories

### Getting started

| Recipe | Description |
| ------ | ----------- |
| [First balance check](getting-started/first-balance.md) | Verify MCP auth and read wallet balances |
| [First customer](getting-started/first-customer.md) | Create a customer with valid KYC fields |
| [First transaction](getting-started/first-transaction.md) | Sandbox payout or swap flow |

### Virtual accounts

| Recipe | Description |
| ------ | ----------- |
| [Create a virtual account](virtual-accounts/create-account.md) | Static or dynamic VA for collections |
| [Onboarding flow](virtual-accounts/onboarding-flow.md) | Customer + VA end-to-end |
| [Collections](virtual-accounts/collections.md) | Reconcile incoming VA deposits |

### Payments

| Recipe | Description |
| ------ | ----------- |
| [Payment links](payments/payment-links.md) | Hosted checkout sessions |
| [Payment status](payments/payment-status.md) | Track transactions and references |
| [Payouts](payments/payouts.md) | Withdraw to bank or mobile money |

### Stablecoins

| Recipe | Description |
| ------ | ----------- |
| [USDC wallets](stablecoins/usdc.md) | Get or create USDC deposit addresses |
| [Treasury](stablecoins/treasury.md) | Multi-currency balance and FX view |
| [Settlement](stablecoins/settlement.md) | Stablecoin → fiat payout patterns |

### AI agents

| Recipe | Description |
| ------ | ----------- |
| [Operations agent](ai-agents/operations-agent.md) | Daily balance and transaction monitoring |
| [Treasury agent](ai-agents/treasury-agent.md) | FX and liquidity checks |
| [Support agent](ai-agents/support-agent.md) | Look up customers and payment status |

### OpenClaw

| Recipe | Description |
| ------ | ----------- |
| [Getting started](openclaw/getting-started.md) | Wire Afriex MCP into OpenClaw |
| [Payment agent](openclaw/payment-agent.md) | Autonomous payment assistant |

### Example applications

| Recipe | Description |
| ------ | ----------- |
| [Next.js](examples/nextjs.md) | App router + checkout |
| [Python / FastAPI](examples/fastapi.md) | API + webhooks |
| [Python scripts](examples/python.md) | CLI automation |
| [Laravel](examples/laravel.md) | PHP integration patterns |

---

## Recipe format

Every file follows the same structure:

- **Use case** — the business problem
- **Business outcome** — the concrete deliverable (report, receipt, UI copy, code scaffold)
- **Prompt** — copy-paste ready, outcome-driven
- **Expected outcome** — MCP tools called and success criteria
- **Extensions** — follow-ups grounded in real MCP capabilities

---

## Contributing

Recipes should be specific, production-oriented, and grounded in real Afriex MCP capabilities. Avoid toy examples and generic “build a payment app” prompts.

---

## Links

- [Afriex Business API docs](https://docs.afriex.com)
- [Afriex MCP server](https://docs.afriex.com/mcp/introduction)

## License

MIT — see [LICENSE](LICENSE).
