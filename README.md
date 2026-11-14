# Token Radar — model radar-2

Every protocol in the DefiLlama snapshot that records no token, ranked by what its
launch would be worth. Built for the make.fun competition "Build the best token launch
radar" (token-radar-01).

## Model (radar-2)

`implied = round(tvl_usd x catMultiple x growthMult x maturityMult x chainMult)`

- **catMultiple** — the median market-cap/TVL ratio of the snapshot's own live-token
  protocols in the same category, clamped to 0.05–1.0x; the global median when a
  category has no live peer. Derived at runtime from the snapshot, never hardcoded.
- **growthMult** — `1 + clamp(change_7d, -60, 120) / 100 x 0.5`
- **maturityMult** — `0.6 + min(age_days, 1095) / 1095 x 0.4` (unknown age: 365 days)
- **chainMult** — `1 + min(chains, 8) x 0.01`

Excluded from the board, by the watchlist's own rule applied to every row: bridges,
canonical bridges, liquid staking/restaking receipts, restaked BTC, exchanges,
versioned pools, and names carrying wrapped, staked or staking. 198 protocols rank;
144 pre-token entries are set aside this way. All 40 watchlist protocols are covered.

## Run

```sh
node main.ts --input <snapshot.json> --output <dir>
```

Node 22, standard library only. Reads the snapshot, writes `board.json`, `data.js`,
`dashboard/`, `screenshots/` and `dossier.json` (last). Deterministic: two runs write
byte-identical output.

"Records no token" means the snapshot entry carries no market cap, no CoinGecko id
and no symbol. It is a statement about the snapshot, not a claim that the protocol
has no token, and this radar does not predict whether or when a token is issued.
