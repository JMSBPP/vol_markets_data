# Default Algebra Integral pool

Selected 2026-09-24 by: Algebra Integral partner docs → DefiLlama 24h volume
(among SQD-indexed Integral DEXes) → GeckoTerminal top pool → live
`IVolatilityOracle` check.

| Field | Value |
|-------|--------|
| Protocol | **SwapX Algebra** (Algebra Integral 1.0) |
| Docs | https://docs.algebra.finance/algebra-integral-documentation/overview-faq/partners/integral-1.0/swapx |
| Chain | Sonic (chain id `146`) |
| Portal network | `sonic-mainnet` |
| DefiLlama slug | `swapx-algebra` (~$223k 24h volume at selection) |
| AlgebraFactory | `0x8121a3F8c4176E9765deEa0B95FA2BDfD3016794` |
| PluginFactory | `0x11F0Ccf4aC81878B81EBc907b8B9a9ECd089a227` |
| **Default pool** | `0x5C4B7d607aAF7B5CDE9F09b5F03Cf3b5c923AEEa` |
| Pair | wS / USDC (token0 `0x039e2fB66102314Ce7b64Ce5Ce3E5183bc94aD38`, token1 `0x29219dd400f2Bf60E5a23d13Be72B486D4038894`) |
| Fee tier (GT label) | 0.2% |
| **Default plugin** | `0x20430C07DfeF00391b72f00Ae865D3aa31F723d1` |
| RPC | `https://rpc.soniclabs.com` |

## Single-block RV sample (length-1 series)

| Field | Value |
|-------|--------|
| Block | `79840362` |
| Timestamp | `1790261911` |
| `volatilityCumulative` @ 0s | `6834226799834` |
| `volatilityCumulative` @ 86400s | `6820817481214` |
| `realizedVol` = Δ / WINDOW | **`155200`** (Algebra `uint88` WINDOW average) |

WINDOW = `1 days` = `86400` seconds (Algebra `VolatilityOracle` library).
