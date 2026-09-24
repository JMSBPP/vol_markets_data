# EVM DEX SQD — reference

## Common log event aliases

Pass to `portal_evm_query_logs.event` (merged with topic0):

| Alias | Typical use |
|-------|-------------|
| `swap` | Uniswap-style trades |
| `sync` | V2 reserve updates |
| `mint` / `burn` | LP add/remove (protocol-specific) |
| `transfer` | ERC-20 / LP token moves |
| `deposit` / `withdrawal` | Vault / wrapper flows |
| `approval` | Allowance changes |

Prefer aliases over memorizing topic0 hashes. Use `decode: true` for fields.

## Example: recent pool swaps (logs)

```json
{
  "network": "base-mainnet",
  "timeframe": "1h",
  "addresses": ["0x<pool>"],
  "event": "swap",
  "decode": true,
  "limit": 20,
  "scan_order": "latest"
}
```

## Example: Uniswap v3 OHLC on Base

```json
{
  "network": "base-mainnet",
  "source": "uniswap_v3_swap",
  "pool_address": "0x<pool>",
  "duration": "24h",
  "interval": "1h",
  "price_in": "auto",
  "include_recent_trades": true
}
```

## Example: Uniswap v4 OHLC

Prefer `pool_id` when known. Otherwise pass pool key fields:

```json
{
  "network": "base-mainnet",
  "source": "uniswap_v4_swap",
  "pool_id": "0x<bytes32>",
  "duration": "1h",
  "interval": "5m",
  "include_recent_trades": true
}
```

Or derive with `currency0_address`, `currency1_address`, `fee`, `tick_spacing`, optional `hooks_address`.

## Example: resolve then OHLC

1. `portal_resolve_entity` — token or pool
2. Confirm network with `portal_list_networks` if ambiguous
3. `portal_evm_get_ohlc` with resolved `pool_address` / `pool_id`
4. If empty candles: verify source matches protocol (v2 vs v3 vs Slipstream), then try `portal_evm_query_logs` with `event: "swap"` for evidence

## Coverage checklist

Before claiming “no trades” or “complete history”:

- [ ] Correct Portal network name
- [ ] Correct pool address / pool_id for that chain
- [ ] Source matches pool type
- [ ] `_coverage` / freshness acceptable
- [ ] Pagination exhausted (`next_cursor` absent)
