---
name: evm-dex-sqd
description: >-
  Query EVM DEX market data and Algebra Integral realized volatility via SQD
  Portal MCP plus RPC eth_call. Use when the user asks about Uniswap, Aerodrome,
  SwapX, Algebra volatility oracle, pool realized vol, DEX prices, swaps, OHLC,
  or SQD/Squid Portal EVM market data.
---

# EVM DEX via SQD Portal MCP

Living skill for chat-sized EVM DEX investigation. Prefer SQD MCP tools for
discovery and swap evidence; use RPC (`cast call`) for Algebra plugin view
reads. Do not duplicate the full Portal catalog — discover schemas with
`GetDynamicTools` before calling.

**Defaults:** see [defaults.md](defaults.md) — SwapX Algebra wS/USDC on Sonic.

**Adapt on the go:** when a pattern works, fails, or needs a new default, append
a dated note to [adaptations.md](adaptations.md) before ending the turn.

## MCP namespaces

Use whichever is ready: `SQD` or `plugin-sqd-SQD`. Same tool names.

## Default workflow

```
Task progress:
- [ ] 1. Resolve network (default: sonic-mainnet)
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
| Sonic (default) | `sonic-mainnet` |
| Ethereum | `ethereum-mainnet` |
| Base | `base-mainnet` |
| Arbitrum | `arbitrum-one` |
| Optimism | `optimism-mainnet` |
| Polygon | `polygon-mainnet` |
| BSC | `binance-mainnet` |
| Avalanche | `avalanche-mainnet` |

Optional freshness check: `portal_get_network_info` / `portal_get_head`.

### 2. Resolve entities

Never hardcode token/pool addresses from memory when a symbol or name is given
(except the baked defaults in [defaults.md](defaults.md)):

```text
portal_resolve_entity  { "network": "sonic-mainnet", "kind": "token", "query": "USDC" }
portal_resolve_entity  { "network": "sonic-mainnet", "kind": "protocol", "query": "swapx" }
```

If multiple matches, pick the canonical one or ask. Keep ambiguity explicit.

### 3. Tool routing (DEX-focused)

| User need | Tool | Notes |
|-----------|------|--------|
| **Algebra realized vol (single block)** | RPC `cast call` on plugin | See workflow below — not SQD |
| Recent swaps on default / any pool | `portal_evm_query_logs` | `event`: `swap`; default pool in defaults.md |
| Pool price chart / OHLC + trade tape | `portal_evm_get_ohlc` | Uniswap/Aerodrome sources; Algebra may need logs |
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

Default OHLC shape (non-Algebra):

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

### 4. Algebra volatility oracle (realized vol)

Integral **base pools** attach a default plugin that includes
`@cryptoalgebra/volatility-oracle-plugin` (docs: plugins overview). Algebra
v1.0/v1.9 keep the oracle in-core (`DataStorageOperator`) — out of scope for
this default.

**Single-block series (length 1):**

```
Task progress:
- [ ] Use defaults.md pool/plugin unless user names another
- [ ] Sanity: pool.plugin(), isInitialized(), timepointIndex()
- [ ] eth_call getTimepoints([0, 86400]) at block B
- [ ] realizedVol = (volCum[0] - volCum[1]) / 86400
- [ ] Report one row: block, timestamp, pool, plugin, realizedVol
```

```bash
RPC=https://rpc.soniclabs.com
POOL=0x5C4B7d607aAF7B5CDE9F09b5F03Cf3b5c923AEEa
PLUGIN=$(cast call "$POOL" 'plugin()(address)' --rpc-url "$RPC")
B=$(cast block-number --rpc-url "$RPC")
cast call "$PLUGIN" 'getTimepoints(uint32[])(int56[],uint88[])' '[0,86400]' \
  --rpc-url "$RPC" --block "$B"
```

Details: [reference.md](reference.md). Addresses: [defaults.md](defaults.md).

### 5. Query hygiene

- Prefer `timeframe` / `duration` over huge block ranges for interactive answers.
- Always filter logs by `addresses` and/or `event` / topics.
- Respect MCP limits (logs often max ~25/page). Use `cursor` / `_pagination.next_cursor`.
- Check `_coverage` before claiming completeness.
- Use `decode: true` on logs when you need human-readable swap fields.
- For chat answers use compact/summary presets when available; expand only for evidence.

### 6. Answer format

1. One-line verdict (price move, volume, RV, anomaly).
2. Network + pool/token/plugin identifiers used.
3. Key numbers with window and interval / WINDOW.
4. Coverage caveats (lag, pagination, sampled).
5. Optional next probe (deeper window, other fee tier, counterpart pool).

## Out of scope (hand off)

| Need | Hand off |
|------|----------|
| Full raw export / NDJSON | Portal Stream API / curl (see SQD `portal` plugin skill) |
| Durable indexer / API | Pipes / Squid |
| Non-EVM DEX (Solana, Hyperliquid) | Broader SQD portal skill — not this skill’s default |
| Algebra v1 in-core DataStorageOperator RV | Separate workflow; not this default |

## Progressive disclosure

- Baked defaults + RV sample: [defaults.md](defaults.md)
- Living playbook: [adaptations.md](adaptations.md)
- Event aliases, cast, WINDOW formula: [reference.md](reference.md)
