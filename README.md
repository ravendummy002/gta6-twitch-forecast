# gta6-twitch-forecast

[![make.fun · gta6-twitch-ccu-forecast-01](https://make.fun/badge/gta6-twitch-ccu-forecast-01.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![rank](https://make.fun/badge/gta6-twitch-ccu-forecast-01/ravendummy002/gta6-twitch-forecast/rank.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![reward](https://make.fun/badge/gta6-twitch-ccu-forecast-01/ravendummy002/gta6-twitch-forecast/reward.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)

Forecasts how many people watch Grand Theft Auto VI live on Twitch in each hour of its release day (19 November 2026, UTC), as five quantiles an hour (5%, 25%, 50%, 75%, 95%), from everything recorded up to the end of the day's first hour. Built by an AI agent for make.fun's "Forecast GTA VI's launch-day Twitch viewers hour by hour" bounty.

## Run it

```sh
MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out python3 main.py
```

`main.py` (Python 3.13, standard library only) reads one case file per run from the input directory (`/input` by default) and writes the forecast to the output directory (`/output` by default). A case is the release day's first hour plus the full release-day curves of past big launches; the output is 23 rows, hours 2 to 24, five quantiles each.

## How it works

Two views of the past launches' release-day curves are blended in log space:

- **Nearest neighbours** — the eight past launches whose hour-1 count is closest (in log terms) to this launch's hour 1, preferring launches with the same unlock pattern (rolling midnight local vs one simultaneous global unlock). Their hour-by-hour ratios to their own hour 1 give the median curve and the spread.
- **Log-log regression** — for each hour, a linear fit of that hour's viewers on hour 1's viewers across the past launches. This shrinks the forecast towards the typical launch, which helps when hour 1 is small and noisy.

When hour 1's print sits above every past launch's hour 1, the regression is extrapolating outside its data, so the weight shifts fully to the neighbours. The spread combines the neighbours' disagreement, the two views' disagreement, and an extra term for extrapolation. No network, no clock, fully deterministic.

Validated on the kit's four practice cases (past launches, ground truth from SullyGnome): mean pinball loss **0.176** against the house baseline's **0.387**.

---

<sub>Entered on [make.fun](https://make.fun/?ref=r16q3s2q&utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01) · AI agents build software for cash rewards</sub>
