# Market Health Summary

Source: [market_health_tests.txt](./market_health_tests.txt)

All runs below use `seed=7`.

## Important Caveat

- These runs manually override `--horizon`, so the preset's own default horizon is not what drives the result here.
- News cadence currently depends on runtime horizon, not just preset name.
- That means `high_news` should be interpreted as "stronger news-sensitive behavior" in these tests, not necessarily "more frequent news events."

## Comparison Table

| Preset | Horizon | Trades | Trades / 1k steps | News | Spread avail. | Mean spread | Final midpoint | Final fundamental | Final total equity | Runtime (s) |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| baseline | 1,000 | 472 | 472.0 | 16 | 0.259 | 0.1771 | 101.0100 | 101.0938 | 49,494.94 | 0.185 |
| baseline | 10,000 | 4,466 | 446.6 | 166 | 0.368 | 0.2437 | 113.2450 | 113.5063 | 50,669.59 | 7.743 |
| baseline | 20,000 | 8,323 | 416.2 | 333 | 0.414 | 0.2548 | 122.2900 | 123.0771 | 51,569.25 | 33.558 |
| baseline | 30,000 | 12,579 | 419.3 | 500 | 0.396 | 0.2484 | 159.3700 | 159.2488 | 54,980.78 | 89.819 |
| baseline | 50,000 | 20,799 | 416.0 | 833 | 0.394 | 0.2414 | 202.2050 | 202.2342 | 59,007.27 | 354.253 |
| baseline | 100,000 | 39,816 | 398.2 | 1,666 | 0.407 | 0.2417 | 290.5700 | 291.4061 | 67,392.18 | 1,947.592 |
| fragile_liquidity | 1,000 | 330 | 330.0 | 16 | 0.081 | 0.1147 | 98.2800 | 98.5296 | 49,261.79 | 0.184 |
| fragile_liquidity | 10,000 | 3,372 | 337.2 | 166 | 0.080 | 0.1354 | 119.6100 | 119.6082 | 51,243.17 | 6.810 |
| fragile_liquidity | 20,000 | 6,803 | 340.2 | 333 | 0.084 | 0.1343 | 129.9100 | 130.2140 | 52,240.12 | 30.664 |
| fragile_liquidity | 30,000 | 9,973 | 332.4 | 500 | 0.083 | 0.1356 | 165.5950 | 167.7065 | 55,764.41 | 80.805 |
| fragile_liquidity | 100,000 | 30,950 | 309.5 | 1,666 | 0.087 | 0.1351 | 347.5350 | 348.1259 | 72,723.84 | 1,839.635 |
| high_information_asymmetry | 1,000 | 496 | 496.0 | 16 | 0.276 | 0.1889 | 99.3650 | 99.1602 | 49,519.38 | 0.182 |
| high_information_asymmetry | 10,000 | 4,958 | 495.8 | 166 | 0.262 | 0.2161 | 99.4050 | 100.4735 | 49,645.46 | 8.055 |
| high_information_asymmetry | 20,000 | 9,839 | 492.0 | 333 | 0.267 | 0.2097 | 129.1450 | 129.1770 | 52,400.99 | 35.695 |
| high_information_asymmetry | 30,000 | 14,624 | 487.5 | 500 | 0.269 | 0.2135 | 171.1750 | 171.7919 | 56,492.03 | 89.777 |
| high_information_asymmetry | 100,000 | 47,202 | 472.0 | 1,666 | 0.277 | 0.2145 | 314.3000 | 314.5143 | 70,193.37 | 2,104.649 |
| high_news | 1,000 | 465 | 465.0 | 16 | 0.378 | 0.2414 | 100.4050 | 100.2257 | 49,421.21 | 0.179 |
| high_news | 10,000 | 4,454 | 445.4 | 166 | 0.395 | 0.2832 | 103.8900 | 103.7953 | 49,756.75 | 7.358 |
| high_news | 20,000 | 8,408 | 420.4 | 333 | 0.397 | 0.3053 | 126.9350 | 127.3696 | 51,972.74 | 40.553 |
| high_news | 30,000 | 12,744 | 424.8 | 500 | 0.385 | 0.2927 | 157.6050 | 158.6024 | 54,908.63 | 88.739 |
| high_news | 100,000 | 40,141 | 401.4 | 1,666 | 0.402 | 0.2495 | 330.0600 | 330.6853 | 71,084.42 | 1,865.587 |

## Quick Read

- `baseline`: balanced reference. Trade rate gradually decreases with horizon, but spread availability stabilizes around `0.39-0.41`.
- `fragile_liquidity`: clearly thinner book. Lowest spread availability in every run and also the lowest trade rate.
- `high_information_asymmetry`: most active preset by trade rate. It consistently produces the highest `trades / 1k steps`.
- `high_news`: does not increase news count by itself when you manually override horizon, because news cadence depends on runtime horizon. It does, however, produce the widest mean spread among the presets here.

## What This Suggests For Live Testing

- `baseline`: use it as the reference world. If another preset does not feel visually different from this one, it probably needs stronger tuning.
- `fragile_liquidity`: you should expect a visibly thinner order book, fewer trades, and a less resilient market.
- `high_information_asymmetry`: you should expect the most active tape and a stronger sense that informed behavior is shaping the market.
- `high_news`: you should look less for "more news rows" and more for stronger market reaction around news events when they happen.

## Most Useful Cross-Preset Comparisons

### At 10,000 steps

| Preset | Trades / 1k | Spread avail. | Mean spread |
| --- | ---: | ---: | ---: |
| baseline | 446.6 | 0.368 | 0.2437 |
| fragile_liquidity | 337.2 | 0.080 | 0.1354 |
| high_information_asymmetry | 495.8 | 0.262 | 0.2161 |
| high_news | 445.4 | 0.395 | 0.2832 |

### At 100,000 steps

| Preset | Trades / 1k | Spread avail. | Mean spread | Final midpoint |
| --- | ---: | ---: | ---: | ---: |
| baseline | 398.2 | 0.407 | 0.2417 | 290.5700 |
| fragile_liquidity | 309.5 | 0.087 | 0.1351 | 347.5350 |
| high_information_asymmetry | 472.0 | 0.277 | 0.2145 | 314.3000 |
| high_news | 401.4 | 0.402 | 0.2495 | 330.0600 |

## Current High-Level Takeaways

- `fragile_liquidity` is already a clearly distinct regime.
- `high_information_asymmetry` is also clearly distinct and currently the most active market ecology.
- `high_news` is distinct in spread behavior, but may still need stronger differentiation in the live viewer.
- `baseline` remains a good control condition for future MARL experiments.
