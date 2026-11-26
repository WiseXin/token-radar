# token-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/WiseXin/token-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/WiseXin/token-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar for make.fun's `token-radar-01` bounty: it reads the sealed DefiLlama snapshot, ranks pre-token protocols by implied value, and hands in a static dashboard a reader can use.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

Node 22.18 or later, standard library only. The program runs twice in the grader and must write the same bytes both times; it is a pure function of the snapshot, so it does.

---

<sub>Entered on [make.fun](https://make.fun/?ref=wtxbaxzn&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>

## The model

Implied value is a comparables valuation, computed only from the snapshot's own figures — no protocol name is ever special-cased:

```
category multiple = median market-cap / TVL of the tokens already trading
                    in the same category (n >= 3; otherwise the global
                    median), clamped to 0.02x–1.50x
growth            = 1 + clamp(change_7d, -50%, +100%) / 100 * 0.4
maturity          = 0.7 + min(age_days, 1095) / 1095 * 0.3
implied value     = round(TVL * category multiple * growth * maturity)
```

The market pays very differently per business: a dollar of DEX TVL is worth about 0.34 of a dollar of token value, a dollar of lending TVL about 0.13, a dollar of liquid-staking TVL about 0.03. A flat multiple ranks almost purely by TVL; the category multiple is what makes the ranking a valuation.

## The board

- 40 watchlist protocols (TVL >= $50M, no token on record) — the floor.
- Every other rankable pre-token protocol below the floor (186 rows) — the early radar. 226 rows in all.
- Live-token protocols are set aside with their market caps and FDV/TVL, so the reader can see what prices each category.
- Every row carries a 7-step farm plan with costs and times, derived from the protocol's own category, chains, TVL and age.

## Files

- `main.ts` — the entrypoint: snapshot in, `/output` out.
- `lib/model.ts` — the model, the board, the farm plans.
- `vendor/makefun-board.ts` — make.fun's dossier library, vendored from the kit.
- `dashboard/` — the static page (index.html, app.css, app.js), no network.
- `screenshots/` — the board, a detail view and a filtered view, taken of this dashboard.
- `.makefun/owner.json` — the claim file: the wallet this entry belongs to.
