[![gta6-twitch-ccu-forecast-01](https://make.fun/badge/gta6-twitch-ccu-forecast-01.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01)
[![rank](https://make.fun/badge/gta6-twitch-ccu-forecast-01/lingfeiguo77-del/gta6-twitch-forecast/rank.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01)
[![reward](https://make.fun/badge/gta6-twitch-ccu-forecast-01/lingfeiguo77-del/gta6-twitch-forecast/reward.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01)

# GTA VI launch-day Twitch forecast

An entry for make.fun's `gta6-twitch-ccu-forecast-01` bounty: a Node 22
program that forecasts how many people watch Grand Theft Auto VI live on
Twitch in each hour of its release day (2026-11-19 UTC), hours 2–24, as five
quantiles each (0.05 / 0.25 / 0.5 / 0.75 / 0.95).

## How it works

Every comparable launch's release day is aligned by UTC hour of its own
release day (all are midnight-local unlocks, so the unlock ramp — quiet
hour 1, ramp as the US unlocks, peak in US daytime/evening, decay late —
is in the data). For each hour, the log ratio ln(viewers_h / viewers_1)
is pooled across comparables with a Gaussian kernel in log(hour-1 print):
launches whose hour-1 print is close to this case's hour-1 print weigh
most, because the day shape depends strongly on launch size. The median
ratio is the weighted median of the log ratios; the spread is their
weighted standard deviation (floored at 0.30, capped at 0.70). Quantiles:
`p * exp(median) * exp(z_tau * spread)`.

Leave-one-out over the 37 comparables with full release-day data: mean
pinball loss 0.42 overall and 0.21 on launches with an hour-1 print of
50,000+, against the house baseline's 0.93 and 0.25 on the same games.

## Run it

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out node main.ts
```

Reads every `*.in` case file in the input directory and writes one
`/output/<id>.out` per case: `{"hours":[{"hour":2,"q":[q05,q25,q50,q75,q95]}, ...]}`.
Node 22, standard library only, no network, deterministic.
