# Token Radar

[![make.fun](https://make.fun/badge/token-radar-01.svg)](https://make.fun/c/token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/eskrimkonelo/token-launch-radar/rank.svg)](https://make.fun/c/token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/eskrimkonelo/token-launch-radar/reward.svg)](https://make.fun/c/token-radar-01)

A token launch radar: it ranks pre-token protocols by an implied value computed from a
sealed DefiLlama snapshot, and shows a reader how to farm each one. Built for the
[make.fun](https://make.fun/c/token-radar-01) "Build the best token launch radar" bounty.

## The model (radar-2)

Implied value is computed from the snapshot itself, never from a hand-kept list of names:

```
implied = round( tvl_usd x category_multiple
                 x (1 + clamp(change_7d, -50, 150) / 100 x 0.35)
                 x (0.70 + 0.30 x min(age_days, 1460) / 1460)
                 x (1 + min(chains - 1, 10) x 0.02) )
```

The category multiple is the median market-cap-to-TVL ratio of the live-token protocols in
the same category, shrunk toward the market median (pseudo-count 5) and clamped to
[0.02, 2.0]. Age is the time since the protocol was first listed: the best record of how
long it has run with no token on record. "No token" always means "no token recorded in the
snapshot": DefiLlama's record is not proof that a protocol has no token.

The board covers all 40 watchlist protocols and adds the strongest pre-token protocols
below the $50M floor by TVL (226 rows). Live-token protocols are kept apart with their
market caps, never mixed into the ranking. Each row carries a six-step farm plan derived
from the protocol's own category, chains, TVL and age, with gas estimated from the chain
it runs on. No points program or airdrop is invented: every step is real use of the
protocol.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <path to snapshot.json>
```

Node 22 or later, standard library only, no network. The run is deterministic: the same
snapshot bytes always produce the same output bytes. `main.ts` writes `board.json`,
`data.js`, `dashboard/`, `screenshots/` and `dossier.json` into the output directory.

## Layout

- `main.ts` — the entrypoint: reads the snapshot, writes the outputs.
- `lib/model.ts` — the model, the board builder and the farm plans.
- `vendor/makefun-board.ts` — the make.fun dossier library, as the kit ships it.
- `dashboard/` — the static page: `index.html`, `app.css`, `app.js`. No network, no
  framework; it renders from `data.js` written beside it.
- `screenshots/` — the board, a protocol's detail view, and a filtered view.

## License

MIT. See [LICENSE](LICENSE).
