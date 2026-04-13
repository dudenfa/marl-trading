# Live Viewer Breakdown Summary

Source: [live_viewer_breakdown.txt](/Users/dude/Desktop/uni/tirocinio/marl-trading/live_viewer_breakdown.txt)

These results appear to come from the live viewer default runtime horizon of `10,000` steps, which matches the default in [server.py](/Users/dude/Desktop/uni/tirocinio/marl-trading/src/marl_trading/live/server.py).

## Market-Level Comparison


| Preset                     | Trades | News | Final total equity | Net total PnL | Realized PnL | Open orders (final) | Leader      |
| -------------------------- | ------ | ---- | ------------------ | ------------- | ------------ | ------------------- | ----------- |
| baseline                   | 4,466  | 166  | 50,669.59          | +1,269.59     | +1,243.96    | 4                   | maker_01    |
| high_news                  | 4,454  | 166  | 49,756.75          | +356.75       | +408.96      | 5                   | maker_01    |
| fragile_liquidity          | 3,372  | 166  | 51,243.17          | +1,843.17     | +1,746.90    | 1                   | maker_01    |
| high_information_asymmetry | 4,958  | 166  | 49,645.46          | +45.46        | -82.57       | 3                   | informed_01 |


## Agent-Level Outcome Summary


| Preset                     | Best agent by PnL     | Worst agent by PnL    | Most inventory accumulated | Biggest inventory reduction |
| -------------------------- | --------------------- | --------------------- | -------------------------- | --------------------------- |
| baseline                   | maker_01 `+571.41`    | retail_01 `+103.94`   | informed_01 `+11` units    | retail_01 `-10` units       |
| high_news                  | maker_01 `+242.78`    | retail_01 `-131.41`   | informed_01 `+30` units    | retail_01 `-20` units       |
| fragile_liquidity          | trend_01 `+643.11`    | informed_01 `+275.71` | trend_01 `+21` units       | maker_01 `-15` units        |
| high_information_asymmetry | informed_02 `+176.52` | retail_01 `-60.11`    | informed_02 `+6` units     | informed_01 `-4` units      |


## Per-Agent Comparison


| Preset                     | Agent       | Final equity | PnL     | Realized | Unrealized | Cash delta | Inventory delta |
| -------------------------- | ----------- | ------------ | ------- | -------- | ---------- | ---------- | --------------- |
| baseline                   | maker_01    | 14,571.41    | +571.41 | +561.10  | +10.31     | +144.67    | -1              |
| baseline                   | informed_01 | 12,164.95    | +364.95 | +352.18  | +12.78     | -1,126.73  | +11             |
| baseline                   | retail_01   | 12,103.94    | +103.94 | +107.05  | -3.11      | +968.88    | -10             |
| baseline                   | trend_01    | 11,829.28    | +229.28 | +223.63  | +5.65      | +13.18     | 0               |
| high_news                  | maker_01    | 14,242.78    | +242.78 | +263.47  | -20.69     | +298.56    | -2              |
| high_news                  | informed_01 | 11,993.85    | +193.85 | +219.70  | -25.85     | -2,988.32  | +30             |
| high_news                  | retail_01   | 11,868.59    | -131.41 | -131.41  | +0.00      | +1,868.59  | -20             |
| high_news                  | trend_01    | 11,651.53    | +51.53  | +57.20   | -5.66      | +821.17    | -8              |
| fragile_liquidity          | maker_01    | 14,493.44    | +493.44 | +471.16  | +22.28     | +1,503.24  | -15             |
| fragile_liquidity          | retail_01   | 12,430.90    | +430.90 | +430.50  | +0.41      | +756.39    | -6              |
| fragile_liquidity          | trend_01    | 12,243.11    | +643.11 | +579.18  | +63.93     | -2,182.39  | +21             |
| fragile_liquidity          | informed_01 | 12,075.71    | +275.71 | +266.06  | +9.65      | -77.24     | 0               |
| high_information_asymmetry | informed_01 | 14,255.59    | -44.41  | -59.52   | +15.11     | +348.96    | -4              |
| high_information_asymmetry | maker_01    | 13,973.47    | -26.53  | -87.70   | +61.16     | -145.95    | +1              |
| high_information_asymmetry | informed_02 | 10,976.52    | +176.52 | +140.88  | +35.64     | -434.85    | +6              |
| high_information_asymmetry | retail_01   | 10,439.89    | -60.11  | -76.23   | +16.12     | +231.84    | -3              |


## What It Suggests

- `baseline`: healthy and balanced. Everyone ends positive, and the maker remains the top performer.
- `high_news`: weaker aggregate market outcome than baseline. The informed trader accumulates a very large inventory, while retail gets fully sold out and ends negative.
- `fragile_liquidity`: despite fewer trades, this run produced the strongest aggregate PnL. The trend follower benefited the most, which suggests thinner liquidity is creating larger exploitable directional moves.
- `high_information_asymmetry`: highest trade count but almost flat aggregate outcome. This looks like the most internally competitive regime, where informed advantage increases activity but not overall market-wide profitability.

## Most Important Cross-Preset Reads

- Best aggregate outcome: `fragile_liquidity`
- Weakest aggregate outcome: `high_information_asymmetry`
- Highest activity: `high_information_asymmetry`
- Strongest retail damage: `high_news`
- Strongest directional inventory build: `high_news / informed_01`
- Strongest trend capture: `fragile_liquidity / trend_01`

## Caveat

- The raw text file contains a formatting typo for `baseline / informed_01 / free equity start` (`11.800,00`), so that one start value should not be trusted literally.

