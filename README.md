# vol_markets_data

Private skill repo for **EVM DEX market data** via SQD Portal MCP.

## Global skill

The skill lives in `skills/evm-dex-sqd/` and is linked into Cursor personal skills:

```text
~/.cursor/skills/evm-dex-sqd → this repo/skills/evm-dex-sqd
```

Invoke by name (`evm-dex-sqd`) or when asking about EVM DEX prices, pools, swaps, OHLC, or SQD Portal DEX queries.

## Adapt on the go

When a query pattern works better (or fails), append a short note to
`skills/evm-dex-sqd/adaptations.md`. That file is the living playbook.

## Prerequisites

- SQD Portal MCP enabled in Cursor (`SQD` or `plugin-sqd-SQD` namespace)
- Prefer MCP tools over inventing curl/API calls for chat-sized answers
