# Adaptations (living playbook)

Append short dated entries when this skill’s defaults should change. Newest first.

Format:

```md
## YYYY-MM-DD — short title

- **Context:** what was asked
- **Tried:** tools + key args
- **Result:** what worked / failed
- **New default:** rule to apply next time
```

---

## 2026-09-24 — SwapX Algebra default + single-block RV

- **Context:** Find Integral protocol using volatility-oracle-plugin; highest-volume pool; length-1 RV series.
- **Tried:** DefiLlama 24h volume among Integral partners (SwapX Algebra Sonic ~$223k beat Kim/Fenix/Treble); Algebra docs factory `0x8121…6794`; GeckoTerminal top pool wS/USDC `0x5C4B…AEEa` (~$135k h24); SQD `portal_evm_query_logs` swaps on `sonic-mainnet`; `cast` `plugin()` → `0x2043…23d1`, `getTimepoints([0,86400])` at block `79840362`.
- **Result:** `realizedVol = 155200` (WINDOW avg). Oracle initialized (`timepointIndex=9407`).
- **New default:** Skill defaults to SwapX Algebra wS/USDC on Sonic; RV via RPC `getTimepoints`, discovery via SQD. See [defaults.md](defaults.md).

## Seed — 2026-09-24

- **Context:** Skill bootstrap for EVM DEX via SQD MCP.
- **Tried:** N/A (initial).
- **Result:** Defaults = resolve entities → OHLC for charts, logs for swap evidence, contract activity for pool snapshots.
- **New default:** Prefer `uniswap_v3_swap` OHLC; fall back to logs if candles empty; always note `_coverage`.
