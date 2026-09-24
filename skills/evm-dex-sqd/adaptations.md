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

## Seed — 2026-09-24

- **Context:** Skill bootstrap for EVM DEX via SQD MCP.
- **Tried:** N/A (initial).
- **Result:** Defaults = resolve entities → OHLC for charts, logs for swap evidence, contract activity for pool snapshots.
- **New default:** Prefer `uniswap_v3_swap` OHLC; fall back to logs if candles empty; always note `_coverage`.
