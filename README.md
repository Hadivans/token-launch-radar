# Token Launch Radar

A token launch radar for the make.fun `token-radar-01` bounty: it reads a sealed DefiLlama
snapshot of 758 protocols, ranks the 200 pre-token protocols by an implied value, and shows
each one with a concrete farm plan — no network, no framework, deterministic.

## How it works

`main.ts` (Node 22, standard library only) reads `/input/snapshot.json`, scores every
pre-token protocol with the model in `lib/model.ts`, and writes `board.json`, `data.js`,
`dashboard/`, `screenshots/` and `dossier.json` to `/output`.

The model (`radar-2`) prices each protocol as:

```
implied = TVL x categoryPeerMultiple x growthMultiplier x maturityMultiplier
```

- `categoryPeerMultiple` — the median market-cap / TVL of the live-token protocols in the
  same category (global live median when a category has fewer than 3 live peers), clamped
  to [0.02, 1.5]. A lending protocol is priced like lending trades, not at one flat multiple.
- `growthMultiplier` — half the 7-day TVL change, clamped to [-60%, +120%].
- `maturityMultiplier` — 0.6 for a new listing, rising to 1.0 at 3 years with no token on
  record. Age is how long the protocol has run with no token; it predicts nothing about
  whether or when a token launches.

The board covers all 40 watchlist protocols plus the 160 highest-TVL rankable pre-token
protocols below the $50M floor. Live-token protocols are never ranked; they are listed
separately with their market caps. Farm plans (6 steps each, with cost and time) are
derived per protocol from its category, chains, TVL and age — no protocol name is
special-cased, and no step promises an airdrop.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input /path/to/snapshot.json
```

Then open `out/dashboard/index.html` in a browser (it reads `out/data.js` beside it).

## make.fun

[![make.fun](https://make.fun/badge.svg)](https://make.fun/r/{referral_code})

Built for the make.fun token-radar-01 competition. Sign up with my referral link:
https://make.fun/r/{referral_code} — replace `{referral_code}` with the entrant's code
(see `GET /api/me/referrals`).
