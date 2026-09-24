---
name: evm-dex-sqd
description: >-
  Query EVM DEX market data (pools, swaps, OHLC candles, token flow, pool
  activity) through SQD Portal MCP. Use when the user asks about Uniswap,
  Aerodrome, DEX prices, pool charts, swap tape, liquidity events, or EVM
  on-chain market data via SQD/Squid Portal.
---

# EVM DEX via SQD Portal MCP

Living skill for chat-sized EVM DEX investigation. Prefer SQD MCP tools; do not
duplicate the full Portal catalog — discover schemas with `GetDynamicTools`
before calling.

**Adapt on the go:** when a pattern works, fails, or needs a new default, append
a dated note to [adaptations.md](adaptations.md) before ending the turn.

## MCP namespaces

Use whichever is ready: `SQD` or `plugin-sqd-SQD`. Same tool names.

## Default workflow

```
Task progress:
- [ ] 1. Resolve network
- [ ] 2. Resolve entities (token / pool / protocol)
- [ ] 3. Pick tool by question type
- [ ] 4. Call with tight timeframe; check _coverage / _pagination
- [ ] 5. Page with next_cursor if needed
- [ ] 6. Report facts + caveats; update adaptations.md if learned
```

### 1. Network

If unsure of the Portal name:

```text
portal_list_networks  { "vm": "evm", "query": "<chain>", "limit": 10 }
```

Common names:

| Common | Portal network |
|--------|----------------|
| Ethereum | `ethereum-mainnet` |
| Base | `base-mainnet` |
| Arbitrum | `arbitrum-one` |
| Optimism | `optimism-mainnet` |
| Polygon | `polygon-mainnet` |
| BSC | `binance-mainnet` |
| Avalanche | `avalanche-mainnet` |

Optional freshness check: `portal_get_network_info` / `portal_get_head`.

### 2. Resolve entities

Never hardcode token/pool addresses from memory when a symbol or name is given:

```text
portal_resolve_entity  { "network": "base-mainnet", "kind": "token", "query": "USDC" }
portal_resolve_entity  { "network": "base-mainnet", "kind": "pool", "query": "..." }
portal_resolve_entity  { "network": "base-mainnet", "kind": "protocol", "query": "uniswap" }
```

If multiple matches, pick the canonical one or ask. Keep ambiguity explicit.

### 3. Tool routing (DEX-focused)

| User need | Tool | Notes |
|-----------|------|--------|
| Pool price chart / OHLC + trade tape | `portal_evm_get_ohlc` | Prefer swap sources over sync |
| Recent swaps / Sync / Mint / Burn logs | `portal_evm_query_logs` | `event`: `swap`, `sync`, `mint`, `burn`, … |
| Token moved? | `portal_evm_query_token_transfers` | Faster than raw Transfer logs |
| What is this pool/router doing? | `portal_evm_get_contract_activity` | Contract-centric summary |
| Raw txs around a trade | `portal_evm_query_transactions` | Evidence pivot |
| Wallet / LP flow | `portal_get_wallet_summary` | Cross-asset overview |
| Activity trend (not candles) | `portal_get_time_series` | Counts/metrics, not OHLC |

**OHLC sources** (`portal_evm_get_ohlc.source`):

- `uniswap_v2_swap` — pair address → `pool_address`
- `uniswap_v3_swap` — pool address → `pool_address` (default)
- `uniswap_v4_swap` — `pool_id` (bytes32) or full v4 key
- `aerodrome_slipstream_swap` — Slipstream pool → `pool_address`
- `uniswap_v2_sync` — reserve-derived; prefer swap when available

Default OHLC shape:

```json
{
  "network": "base-mainnet",
  "source": "uniswap_v3_swap",
  "pool_address": "0x...",
  "duration": "1h",
  "interval": "auto",
  "price_in": "auto",
  "include_recent_trades": true,
  "recent_trades_limit": 10
}
```

### 4. Query hygiene

- Prefer `timeframe` / `duration` over huge block ranges for interactive answers.
- Always filter logs by `addresses` and/or `event` / topics.
- Respect MCP limits (logs often max ~25/page). Use `cursor` / `_pagination.next_cursor`.
- Check `_coverage` before claiming completeness.
- Use `decode: true` on logs when you need human-readable swap fields.
- For chat answers use compact/summary presets when available; expand only for evidence.

### 5. Answer format

1. One-line verdict (price move, volume, anomaly).
2. Network + pool/token identifiers used.
3. Key numbers with window and interval.
4. Coverage caveats (lag, pagination, sampled).
5. Optional next probe (deeper window, other fee tier, counterpart pool).

## Out of scope (hand off)

| Need | Hand off |
|------|----------|
| Full raw export / NDJSON | Portal Stream API / curl (see SQD `portal` plugin skill) |
| Durable indexer / API | Pipes / Squid |
| Non-EVM DEX (Solana, Hyperliquid) | Broader SQD portal skill — not this skill’s default |

## Progressive disclosure

- Living playbook: [adaptations.md](adaptations.md)
- Event aliases & example calls: [reference.md](reference.md)
