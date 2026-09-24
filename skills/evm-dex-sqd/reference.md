# EVM DEX SQD — reference

## Algebra Integral vs older Algebra oracles

| Generation | Volatility data | Interface |
|------------|-----------------|-----------|
| **Integral** (this skill default) | Plugin module `@cryptoalgebra/volatility-oracle-plugin` | `pool.plugin()` → `IVolatilityOracle` |
| v1.0 / v1.9 | In-core `DataStorageOperator` | Pool / operator `getAverages` / timepoints |

Integral default plugins compose dynamic fee + volatility oracle (Algebra docs:
plugins overview). Public production API exposes **cumulatives**, not
`getAverageVolatility` (that helper is internal / test-only).

## WINDOW-average realized volatility

From Algebra `VolatilityOracle` library: `WINDOW = 1 days = 86400`.

```text
(tickCums, volCums) = plugin.getTimepoints([0, 86400])
realizedVol = (volCums[0] - volCums[1]) / 86400   # integer division, uint88 units
```

Sanity views:

- `plugin.isInitialized()`
- `plugin.timepointIndex()`
- `plugin.lastTimepointTimestamp()`
- `plugin.timepoints(index)` for raw slots

### cast examples (Sonic / SwapX default)

```bash
RPC=https://rpc.soniclabs.com
POOL=0x5C4B7d607aAF7B5CDE9F09b5F03Cf3b5c923AEEa

cast call "$POOL" 'plugin()(address)' --rpc-url "$RPC"
PLUGIN=0x20430C07DfeF00391b72f00Ae865D3aa31F723d1

cast call "$PLUGIN" 'isInitialized()(bool)' --rpc-url "$RPC"
cast call "$PLUGIN" 'getTimepoints(uint32[])(int56[],uint88[])' '[0,86400]' \
  --rpc-url "$RPC" --block <N>
```

Length-1 series row shape:

```text
(block, timestamp, pool, plugin, volCum_0, volCum_86400, realizedVol)
```

## Common log event aliases

Pass to `portal_evm_query_logs.event` (merged with topic0):

| Alias | Typical use |
|-------|-------------|
| `swap` | Uniswap / Algebra-style trades |
| `sync` | V2 reserve updates |
| `mint` / `burn` | LP add/remove (protocol-specific) |
| `transfer` | ERC-20 / LP token moves |
| `deposit` / `withdrawal` | Vault / wrapper flows |
| `approval` | Allowance changes |

Prefer aliases over memorizing topic0 hashes. Use `decode: true` for fields.

## Example: recent swaps on default SwapX pool

```json
{
  "network": "sonic-mainnet",
  "timeframe": "1h",
  "addresses": ["0x5C4B7d607aAF7B5CDE9F09b5F03Cf3b5c923AEEa"],
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

Before claiming Algebra RV:

- [ ] Integral plugin (not v1 DataStorageOperator)
- [ ] `isInitialized() == true`
- [ ] `getTimepoints([0,86400])` succeeds (enough history)
- [ ] Block number recorded with the sample
