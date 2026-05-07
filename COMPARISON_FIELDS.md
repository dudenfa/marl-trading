# Comparison Fields Reference

This file explains every field shown by:

- `scripts/compare_market_runs.py`
- the `summary` section emitted by `scripts/run_market_health.py`
- the per-agent `portfolio_breakdown` / `agents` section emitted by `scripts/eval_rl_agent.py`

It is meant to be the quick reference for interpreting comparison output later.

## Comparison Format

`compare_market_runs.py` prints:

- `left`: the first run
- `right`: the second run
- `delta = right - left`
- `%`: percentage change relative to the absolute left value

So:

- positive `delta` means the right run is higher
- negative `delta` means the right run is lower

For example:

- `Trades: left=5000 right=5300 delta=+300`
means the right run had 300 more trades.

## Important Caveat

Not every field is a live tick-by-tick metric.

Some fields are reconstructed after the run from the saved event stream and marked portfolio path. Those are still useful, but they may not exactly match a moment you visually caught in the live viewer if the viewer updated at a slightly different point in the step.

The main field where this matters is:

- `Max Equity Drawdown From Start Replay`

That name explicitly says it is replay-derived.

## Summary Metrics

These are run-level metrics for the whole market.

### Events

- Total number of events in the event log.
- Includes trades, orders, cancels, snapshots, news, and session markers if present.

### Steps

- Number of environment steps in the run.
- Usually equals the requested horizon.

### Trades

- Number of trade events executed in the run.
- This is one of the main activity metrics.

### News

- Number of news events generated in the run.

### Snapshots

- Number of order book snapshots captured in the event log.

### Agents

- Number of unique agents present in the run.

### Coverage

- Snapshot coverage ratio.
- Formula:
  - `snapshot_count / event_count`
- Tells you how much of the event log has order-book state attached.

### Spread Availability

- Fraction of snapshots where a valid spread exists.
- Formula:
  - snapshots with both best bid and best ask
  - divided by total snapshot count

Low values usually mean the book is often one-sided or empty at the top.

### Mean Spread

- Average bid-ask spread across snapshots where spread exists.
- Units: price

### Midpoint Return Volatility Bps

- Volatility of midpoint returns across snapshots.
- Units: basis points

Higher values mean more price movement from one midpoint observation to the next.

### Top Of Book Occupancy

- Same idea as spread availability in practice:
  - fraction of snapshots where top of book exists on both sides

### Mean Top Of Book Liquidity

- Average sum of best bid size and best ask size across valid top-of-book snapshots.

### Active Agent Mean

- Mean number of active agents across the run.

### Mean Total Equity

- Average marked total equity of the whole market over the run.

### Final Total Equity

- Marked total equity of the whole market at the end of the run.

### Final Midpoint

- Final midpoint of the order book.
- Units: price

### Final Fundamental

- Final latent fundamental value.
- Units: price

This is the model’s internal reference value, not necessarily an executable market price.

## Per-Agent Fields

These are shown once per agent in the comparison output.

## Identity Fields

### `agent_id`

- Agent identifier, such as `maker_01`, `rl_01`, `rl_02`.

### `agent_type`

- High-level role/class of the agent, such as:
  - `market_maker`
  - `noise_trader`
  - `trend_follower`
  - `informed_trader`
  - `rl_agent`

## End-State Portfolio Fields

### Equity

- Final marked equity at the end of the run.
- Formula:
  - `cash + inventory * mark_price`

The mark price uses the executable-mark logic in analysis, not latent fundamental fallback.

### Free Equity

- Final equity after accounting for reserved cash / reserved inventory tied up in open orders.

This is the more liquid version of equity.

### Cash

- Final cash balance.

### Cash Delta

- Final cash minus starting cash.

### Inventory

- Final inventory at the end of the run.

### Inventory Delta

- Final inventory minus starting inventory.

### Available Cash

- Cash not currently reserved by resting buy orders.

This may show as `n/a` when the source payload does not include it.

### Available Inventory

- Inventory not currently reserved by resting sell orders.

This may show as `n/a` when the source payload does not include it.

### Open Orders

- Number of resting open orders at the end of the run.

## PnL Fields

### PnL

- Final total PnL at the end of the run.
- Formula:
  - `ending_equity - starting_equity`

This should now be consistent with:

- `Realized + Unrealized`

for runtime RL agents too.

### Realized

- Realized PnL from closed trading outcomes.
- This is profit or loss already locked in by executed round trips / inventory reduction.

### Unrealized

- Mark-to-market PnL on remaining inventory at the end of the run.
- Formula:
  - `inventory * final_mark_price - remaining_cost_basis`

This is not realized cash profit yet.

## Risk / Path Fields

These fields describe what happened during the run, not just the ending state.

### Peak Equity

- Highest marked equity reached at any observed point during the run.

### Max Drawdown

- Largest drop from a previous equity peak.
- Formula:
  - `max(previous_peak_equity - current_equity)`

This is a peak-to-trough path-risk metric.

### Max Drawdown %

- Same as `Max Drawdown`, but normalized by the peak equity at the time of the drop.

### Max Equity Drawdown From Start Replay

- Worst amount equity fell below starting equity in the replay-derived analysis path.
- Formula:
  - `max(0, starting_equity - current_equity)`

Important:

- this is replay-derived
- it may not exactly match a moment you visually noticed in the live viewer
- use `Min Equity Delta` and `Max PnL Drawdown From Start` when you want a cleaner “worst below start” interpretation

### Min Equity Delta

- Lowest value of:
  - `current_equity - starting_equity`

This is often the clearest equity-based “worst moment vs start” field.

Example:

- starting equity `10000`
- path `10100 -> 10500 -> 9980 -> 10300`
- `Min Equity Delta = -20`

### Peak PnL

- Highest total PnL reached at any observed point during the run.

### Max PnL Drawdown

- Largest drop from a previous PnL peak.
- Formula:
  - `max(previous_peak_pnl - current_pnl)`

Important:

- this is a positive drawdown size
- it does not mean the agent necessarily went negative

Example:

- PnL path `0 -> 200 -> 140 -> 260`
- `Max PnL Drawdown = 60`

even though PnL never went below zero.

### Max PnL Drawdown From Start

- Worst amount total PnL fell below zero.
- Formula:
  - `max(0, -current_total_pnl)`

This is the field to use when you want:

- “how bad was the worst underwater PnL moment relative to starting balance?”

Example:

- PnL path `0 -> 50 -> -20 -> 30`
- `Max PnL Drawdown From Start = 20`

### Max Inventory

- Highest signed inventory reached during the run.

If the agent is mostly long-only, this is the largest long position.

### Min Inventory

- Lowest signed inventory reached during the run.

If the agent can go short, this captures the most negative inventory.

### Max |Inventory|

- Largest absolute inventory exposure reached during the run.
- Formula:
  - `max(abs(inventory))`

If the agent never goes short, this may match `Max Inventory`.

## Why Some Inventory Fields Look Redundant

For mostly long-only behavior, you may see:

- `Min Inventory = 0`
- `Max Inventory = 36`
- `Max |Inventory| = 36`

That is expected:

- the agent never went short
- so largest absolute inventory is the same as largest long inventory

## Practical Reading Guide

When judging an RL agent, a good quick read is:

### For profitability

- `PnL`
- `Realized`
- `Unrealized`
- `Peak PnL`

### For path risk

- `Max Drawdown`
- `Min Equity Delta`
- `Max PnL Drawdown`
- `Max PnL Drawdown From Start`

### For inventory behavior

- `Inventory`
- `Max Inventory`
- `Min Inventory`
- `Max |Inventory|`

### For market quality

- `Trades`
- `Spread Availability`
- `Mean Spread`
- `Mean Top Of Book Liquidity`
- `Midpoint Return Volatility Bps`

## Current Source Of Truth

If the metric list changes in the future, the source-of-truth code paths are:

- [comparison.py](/Users/dude/Desktop/uni/tirocinio/marl-trading/src/marl_trading/analysis/comparison.py)
- [health.py](/Users/dude/Desktop/uni/tirocinio/marl-trading/src/marl_trading/analysis/health.py)

This document should be updated whenever fields are added, removed, or renamed.
