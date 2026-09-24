# vol_markets_data

Private skill repo for **EVM DEX market data** via SQD Portal MCP, with an
Algebra Integral volatility-oracle default (SwapX on Sonic).

## Global skill

The skill lives in `skills/evm-dex-sqd/` and is linked into Cursor personal skills:

```text
~/.cursor/skills/evm-dex-sqd → this repo/skills/evm-dex-sqd
```

Invoke by name (`evm-dex-sqd`) or when asking about EVM DEX prices, pools,
swaps, OHLC, Algebra realized vol, or SQD Portal DEX queries.

**Default pool:** SwapX Algebra wS/USDC on Sonic — see
[`skills/evm-dex-sqd/defaults.md`](skills/evm-dex-sqd/defaults.md).

## Adapt on the go

When a query pattern works better (or fails), append a short note to
`skills/evm-dex-sqd/adaptations.md`. That file is the living playbook.

## Prerequisites

- SQD Portal MCP enabled in Cursor (`SQD` or `plugin-sqd-SQD` namespace)
- Prefer MCP tools for discovery; use `cast call` / RPC for Algebra plugin RV
- Prefer MCP over inventing curl/API calls for chat-sized swap/log answers
