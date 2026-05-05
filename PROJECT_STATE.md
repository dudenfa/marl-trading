# Project State

## Purpose
This file is the shared working memory for `marl-trading`.

`THESIS.md` is the manifesto / whitepaper.

`PROJECT_STATE.md` is for:

- locked implementation decisions
- milestone planning
- subagent coordination
- current repo status
- open questions
- next steps

Future coding subagents should read this file before making architectural changes.

## Collaboration Rules

- The user is the research lead and final decision-maker.
- I act as director / planner / reviewer by default.
- Subagents should do the heavy coding work when implementation is requested.
- This file should stay concise, current, and non-duplicated.
- Major design changes should be written here before broad implementation continues.
- Only I am allowed to edit shared-memory files.
- Shared-memory files currently include:
  - `PROJECT_STATE.md`
  - `THESIS.md`
- Subagents may read shared-memory files for context, but must not modify them.

## Repository Direction

This repository is the second major phase of the thesis.

The first repository, `../algo-trading-drl`, studied single-agent RL on historical QQQ order book data.

This repository shifts the thesis toward a synthetic market:

> In a synthetic market where only agents trade and their actions affect price, liquidity, and volatility, what behaviors and strategic dynamics emerge?

The project therefore prioritizes:

- emergent behavior
- market ecology
- interpretability
- visual replay
- experimental control
- staged progression toward MARL

## Locked V1 Decisions

### Market

- One synthetic asset only.
- Spot-only market in V1.
- No leverage in V1.
- No short selling in V1.
- Conceptually 24/7, but implemented with finite episodes.
- Event-driven exchange, not synchronous global ticks.

### Exchange And Visibility

- Central limit order book.
- V1 supports:
  - limit orders
  - market orders
  - cancellations
- Stable public agent identities across the episode.
- Full order book visibility for all agents.
- No direct agent-to-agent communication in V1.
- Agents infer behavior from the public market, blockchain-style.

### Information Structure

- Hidden latent fundamental value process.
- Public news events.
- Noisy private signals for some agents.
- Other agents rely only on public market information.

### Agent Ecology

- Fixed heterogeneous agent types in the first prototype.
- Initial scripted ecology:
  - market makers
  - noise / retail traders
  - trend followers
  - value / informed traders
- No whale / institution-like agents in the very first prototype.
- Zero RL agents in the first prototype.
- Add one RL agent only after the scripted market is stable.

### Portfolio And Risk

- Agents hold only cash and the asset.
- Buy orders must be backed by available cash.
- Sell orders must be backed by available inventory.
- Resting orders reserve resources.
- Outstanding order commitments cannot exceed what the portfolio can support.
- Ruin thresholds are configurable per agent type.
- If an agent breaches its ruin threshold:
  - cancel all open orders
  - deactivate the agent for the rest of the episode

## Director View Of V1

The first version is not “full MARL training.”

The first version is:

- a functioning synthetic exchange
- valid spot accounting
- a believable scripted market ecology
- hidden fundamentals plus news
- public observability
- replay and plotting from the beginning

If V1 works, we should be able to:

- watch the market evolve
- inspect price, trades, and order book state
- track agent inventories and equity
- see reactions to news and information asymmetry
- verify that price and liquidity emerge from agent interaction

## First Prototype Scope

### Must Have

- One-asset spot exchange
- Event-driven simulator loop
- Central limit order book with price-time priority
- Order submission / matching / cancellation
- Portfolio accounting and resource reservation
- Agent deactivation on ruin
- Stable public IDs
- Full order book visibility
- Hidden fundamental value process
- Public news shock process
- Noisy private signals for informed agents
- Scripted heterogeneous agents
- Replayable event log
- Basic visualization / metrics output

### Explicitly Deferred

- RL agents
- Multiple RL agents
- Whale agents
- Leverage
- Short selling
- Multi-asset world
- Direct communication channel
- Full production dashboard

## Preferred Top-Level Architecture

The current preferred module layout is:

- `exchange/`
  - order definitions
  - order book
  - matching engine
  - exchange events
- `market/`
  - simulator loop
  - latent fundamental process
  - news process
  - public tape / event stream
- `agents/`
  - base agent interface
  - scripted market maker
  - scripted noise trader
  - scripted trend follower
  - scripted informed trader
  - later RL wrappers
- `portfolio/`
  - cash / inventory accounting
  - reserved capital tracking
  - equity computation
  - ruin checks
- `analysis/`
  - event log utilities
  - replay extraction
  - summary metrics
  - plotting helpers
- `configs/`
  - market defaults
  - population defaults
  - scenario presets

## Milestone Plan

### Milestone 0: Skeleton And Contracts

Goal:

- establish package structure, shared domain objects, and configs

### Milestone 1: Exchange Core

Goal:

- deterministic spot order book with price-time priority

### Milestone 2: Portfolio And Reservation Logic

Goal:

- enforce cash / inventory constraints and proper reservation release

### Milestone 3: Market World

Goal:

- simulator loop plus latent fundamental and news processes

### Milestone 4: Scripted Ecology

Goal:

- populate the market with heterogeneous scripted agents

### Milestone 5: Replay And Diagnostics

Goal:

- make the market visually inspectable and thesis-ready

### Milestone 6: RL Entry Point

Goal:

- add one RL agent into the scripted world

## Immediate Implementation Order

1. Domain objects and repo skeleton
2. Exchange kernel
3. Portfolio reservation and ruin logic
4. Event-driven simulator
5. Fundamental and news processes
6. Scripted agents
7. Replay / metrics / plots
8. Stability review before RL

## Current Implementation Status

Completed so far:

- `THESIS.md` written as the initial manifesto
- `PROFESSOR_UPDATE.md` added as a concise professor-facing summary of:
  - current synthetic market foundation
  - current diagnostics and presets
  - why RL has not started yet
  - the planned RL and MARL roadmap
- `PROJECT_STATE.md` initialized as shared working memory
- Python package scaffold created
- Core shared domain/config layer created
- Exchange core first pass implemented
- Portfolio/accounting layer implemented
- Scripted market/agent slice implemented
- Analysis / replay first pass implemented
- Market demo path repaired and made runnable
- First local live market viewer implemented
- Live local UI is now working end-to-end as the main inspection surface
- Core live panels are in place and usable:
  - chart
  - compact order book
  - recent trades
  - recent orders
  - recent news
  - agents / capital map
- Major UI cleanup and bug-fix pass completed:
  - compact terminal-style layout established
  - order book / tape / chart panel refined
  - recent news table added under the chart
  - live PnL and agent data wiring repaired
  - recent trades / recent orders labeling and tooltips improved
  - duplicate legacy live-view stack removed
- Live-view runtime performance pass completed:
  - bounded chart/history payload for long sessions
  - lighter live summary generation
  - serialized polling in the browser
  - safer pause / play / reset behavior
  - stable candle bucket alignment after the performance optimization

Current emphasis:

- stabilize the scripted ecology further
- improve market realism and interpretability rather than more UI polish
- prepare the first experimental scenarios on top of the scripted market
- preserve a clean path toward adding the first RL agent after the market is stable
- Basic tests added
- make ecology tuning evidence-driven through metrics, presets, and quick reports
- use preset comparisons and live-viewer breakdowns as the basis for the next tuning wave
- preserve deterministic reproducibility under fixed preset / seed / horizon so scripted-only vs scripted+RL comparisons are scientifically meaningful

Current repo status by area:

### Core / Config

Present:

- `pyproject.toml`
- `src/marl_trading/core/`
- `src/marl_trading/configs/`
- `tests/test_core_smoke.py`

What exists:

- typed domain wrappers
- enums for order/event concepts
- base dataclasses for config and events
- default config presets
- named scenario presets have now been added
- config-driven scripted-agent behavior overrides are being introduced so tuning can happen through config instead of direct class edits
- current preset family includes:
  - baseline
  - high_news
  - fragile_liquidity
  - high_information_asymmetry

### Exchange

Present:

- `src/marl_trading/exchange/`
- `tests/test_exchange_book.py`
- `tests/test_exchange_engine.py`

What exists:

- limit and market orders
- deterministic price-time priority matching
- partial fills
- aggressive limit crossing
- cancellations
- order-book snapshots
- exchange event logging

### Analysis / Replay

Present:

- `src/marl_trading/analysis/`
- `scripts/replay_market.py`
- `scripts/run_market_health.py`
- `tests/test_analysis_events.py`
- `tests/test_analysis_replay.py`
- `tests/test_analysis_health.py`

What exists:

- structured event-log handling
- replay-series extraction
- summary metrics
- market-health summary metrics
- markdown summary files for:
  - market-health preset comparisons
  - live-viewer preset breakdowns
- plotting helpers
- replay CLI
- compact health-report CLI for preset comparison
- optional `--portfolio-breakdown` output in `run_market_health.py`
- side-by-side run comparison tooling via `scripts/compare_market_runs.py`
- preset-aware demo / health script flow is now available
- richer payload support for:
  - latent fundamentals
  - news severity/headlines
  - agent annotations
  - depth snapshots when present

### Portfolio

Present:

- `src/marl_trading/portfolio/`
- `tests/test_portfolio_account.py`
- `tests/test_portfolio_spot.py`

What exists:

- spot-only cash / inventory accounting
- reserved cash and reserved inventory tracking
- reservation / release on order lifecycle
- trade application
- equity computation
- ruin checks
- agent deactivation state

### Market / Agents / Demo

Present:

- `src/marl_trading/market/`
- `src/marl_trading/agents/`
- `scripts/run_market_demo.py`
- `tests/test_market_demo.py`

What exists:

- latent fundamental process
- public news process
- event-driven simulator loop
- scripted agents:
  - market maker
  - noise trader
  - trend follower
  - informed trader
- end-to-end demo run that emits a replayable event log
- `market_world.png` generation via Pillow-based renderer
- stepwise simulator state for live inspection
- demo runner can now launch named presets directly
- `high_news` now aims to be distinct through stronger news impact / reaction, not by relying on a shorter default horizon

### Live Viewer

Present:

- `src/marl_trading/live/`
- `src/marl_trading/live/static/`
- `scripts/serve_market_view.py`
- live-view-related tests

What exists:

- local browser-served market screen
- paused / autoplay / manual step controls
- live JSON state endpoint
- top-10 order book plus full-book payload support
- line and candlestick data sources
- recent trades / tape
- recent orders / order-event feed
- agent portfolio cards with PnL fields
- right-anchored chart window so sparse history behaves more like a market terminal
- fixed-depth visible order book window
- wide-screen main-column plus sticky sidebar layout
- faster local server restart support via reusable HTTP binding
- compact dark workstation-style dashboard inspired by Hyperliquid's density
- top row now groups chart, compact order book, and recent trades
- bottom row now groups agent data and market data
- active live session now enriches agent payloads with:
  - realized PnL
  - unrealized PnL
  - total PnL
  - starting state
  - last action summary
- live viewer is now materially more usable for long sessions because:
  - chart/history work is bounded
  - polling no longer overlaps aggressively
  - pause is safer under load
- live viewer startup path can now be driven by named presets
- market-data counters now read backend totals rather than capped visible slices

### Not Built Yet

- stronger ecology tuning so the market remains healthy longer
- more robust simulator tests
- whale agent phase
- MARL training / multi-learning-agent interaction

### RL First-Agent Infrastructure

Present:

- `src/marl_trading/rl/`
- `tests/test_rl_boundary.py`
- `scripts/train_rl_agent.py`
- `scripts/eval_rl_agent.py`
- `tests/test_train_rl_agent.py`
- `tests/test_eval_rl_agent.py`

What exists:

- a minimal single-agent RL boundary for the first controlled comparison
- compact observation features derived from `MarketObservation`
- small discrete action set:
  - hold
  - market buy
  - market sell
  - limit buy
  - limit sell
  - cancel oldest
- simple equity-delta reward with optional inventory penalty
- `SingleAgentMarketEnv` wrapper for one learning-controlled agent inside an otherwise scripted market
- `GymSingleAgentMarketEnv` wrapper so the first PPO agent can train through a standard Gymnasium-style API
- PPO training script that replaces one scripted slot at runtime rather than deleting the scripted agent from the project
- PPO evaluation script that emits comparison-friendly JSON with:
  - market-health summary
  - per-agent portfolio breakdown
  - runtime metadata
- live-viewer PPO support:
  - optional checkpoint-driven runtime replacement of one scripted slot
  - `trend_01` can now be replaced in the browser session without removing the scripted trend agent from the project
  - existing live UI/state contract stays intact while the PPO-controlled slot trades inside the same synthetic market
- RL-only live diagnostics:
  - the agents panel now exposes a dedicated RL diagnostics block when a runtime PPO checkpoint is active
  - it shows decision counts, action-type counts, recent RL order events, and a compact portfolio snapshot for the PPO-controlled slot
  - this is the main observability tool for Phase A because it lets us tell apart "inactive", "placing non-filling passive orders", and "actually trading"
  - the live session now uses the shared `RuntimePolicyControlledAgent` path, so these counters come from the same runtime agent used by the RL boundary rather than from a stale live-only PPO proxy
- RL slot inventory override:
  - the runtime-replaced PPO slot now defaults to `0.0` starting inventory in training, evaluation, and live-view playback
  - this keeps the RL agent flat at episode start so any later PnL must come from actual decisions rather than inherited inventory drift
- RL reward shaping:
  - reward now supports two optional inventory controls:
    - linear absolute inventory penalty
    - quadratic inventory-risk penalty
  - both default to `0.0`, so previous behavior is preserved unless explicitly changed
  - first quadratic-risk Phase A run at `0.0005` is now part of the RL findings below
- first experiment convention now locked:
  - keep the scripted market unchanged
  - replace `trend_01` at runtime with the RL controller for the RL run
  - compare against the scripted-only baseline under the same preset / seed / horizon

## Validation Status

Verified locally:

- `PYTHONPYCACHEPREFIX=/tmp/pycache_marl python3 -m py_compile $(find src scripts tests -name '*.py' -type f | sort)` passed
- `PYTHONPATH=src python3 scripts/replay_market.py --help` passed
- `PYTHONPATH=src python3 scripts/run_market_health.py --preset baseline --seed 7 --horizon 60` passed
- `PYTHONPATH=src python3 scripts/run_market_demo.py --preset fragile_liquidity --seed 7 --horizon 20 --summary-only ...` passed
- `PYTHONPATH=src python3 scripts/run_market_demo.py --seed 7 --horizon 120 --output-dir artifacts/demo_seed7_h120` passed
- `PYTHONPATH=src python3 scripts/serve_market_view.py --paused --port 8765` launched successfully outside sandbox
- live API verified via:
  - `GET /api/state`
  - `POST /api/control` with step actions
- `node --check src/marl_trading/live/static/app.js` passed
- targeted runtime assertions passed for:
  - live server default horizon now `10000`
  - CLI default horizon now `10000`
  - reset / step still work on the live server
  - long-horizon simulation still emits regular news (`news_count >= 20` over `1200` steps)
- live API verified after redesign and PnL wiring:
  - `/api/state` now returns nonzero `realized_pnl` / `unrealized_pnl` where expected
  - `/api/control` stepping preserves the updated payload contract
- long-run live session probe verified after the performance pass:
  - bounded history window is preserved
  - candle buckets stay aligned to absolute step ranges
  - pause/play polling loop no longer relies on overlapping `setInterval` ticks
- targeted RL verification now passed:
  - RL env, train-script, and eval-script tests pass (`13 passed`)
  - a tiny PPO smoke run completed end to end
  - checkpoint save worked
  - evaluation of that checkpoint produced comparison-friendly JSON with market summary and per-agent breakdown
- targeted live-viewer PPO verification now passed:
  - live session/server tests pass with the PPO runtime path enabled
  - CLI now accepts `--checkpoint` and `--learning-agent-id`
  - fake-model smoke tests confirm `trend_01` appears as `rl_agent` when runtime replacement is enabled
- Phase A PPO verification now passed:
  - a longer single-seed PPO checkpoint was trained for `baseline`, `seed=7`, `horizon=5000`, `total_timesteps=50000`
  - that checkpoint evaluates cleanly through the RL evaluation pipeline
  - the first long scripted-only vs PPO comparison now exists

Limitations:

- `pytest` is not installed in the current sandbox, so the test suite was not run through the test runner
- the replay CLI's richer figure path still depends on `matplotlib`, which is not installed in the current sandbox
- the live market no longer dies immediately from the earlier settlement bug, but the ecology still needs tuning for richer and more persistent behavior

## Latest Evidence From Preset Testing

Primary evidence files:

- `market_health_tests.txt`
- `market_health_summary.md`
- `live_viewer_breakdown.txt`
- `live_viewer_breakdown_summary.md`

Key findings:

- `baseline` is the best current control condition:
  - balanced
  - healthy
  - all four agents can end positive in the 10k-step live-viewer breakdown
- `fragile_liquidity` is already a clearly distinct regime:
  - thinnest book by spread availability
  - lowest trade rate
  - strongest aggregate PnL in the 10k live-viewer breakdown
  - trend follower benefits strongly from thinner liquidity
- `high_information_asymmetry` is also clearly distinct:
  - highest trade intensity
  - strongest evidence of informed-agent dominance
  - high activity does not translate into high aggregate profitability
- `high_news` is now meaningfully distinct in terminal diagnostics:
  - same news count as `baseline` at fixed horizon
  - much wider mean spread
  - much higher midpoint volatility
  - stronger final midpoint / fundamental displacement
  - stronger informed-trader outperformance and weaker retail outcome
  - current implementation direction remains stronger news impact / reaction, not simply more frequent news

Interpretation:

- we now have three clearly useful scripted regimes:
  - `baseline`
  - `fragile_liquidity`
  - `high_information_asymmetry`
- `high_news` needs one more tuning pass and better preset/runtime alignment in the live viewer

New experimental interpretation:

- repeated runs with the same preset / seed / horizon currently converge to the same final numbers across:
  - `run_market_health.py`
  - the live viewer
- this is good and should be preserved
- it means the scripted market is currently reproducible enough to support controlled counterfactual experiments:
  - scripted-only world
  - same world plus one RL agent
  - compare the deltas

## Latest RL Findings

Current Phase A checkpoint:

- preset: `baseline`
- learning slot: `trend_01`
- seed: `7`
- horizon: `5000`
- PPO timesteps: `50000`

What the Phase A evidence now shows:

- first long PPO run from the original inherited-inventory setup:
  - PPO affected the market
  - but mostly by becoming too passive and removing directional pressure
- second long PPO run with the RL slot starting flat (`0` inventory):
  - PPO is no longer passive
  - but it learned a one-sided pathological behavior:
    - repeated limit buys
    - frequent cancels/reposts
    - almost no selling
    - large inventory accumulation
- corrected zero-inventory evaluation now matters:
  - from its true flat start, `trend_01` ends positive (`+394.53`)
  - so the issue is not "PPO only loses money"
  - the issue is that PPO is finding a profitable but market-breaking inventory-hoarding behavior
- market-level impact of that zero-inventory PPO run:
  - trades collapse from `2252` to `80`
  - spread availability drops from `0.382` to `0.049`
  - top-of-book liquidity rises because the buy side becomes abnormally deep
  - final total system equity drops by about `1366`
  - final midpoint remains far below the final latent fundamental
- important nuance for thesis framing:
  - price diverging from the latent fundamental is not automatically "bad" in the final all-AI market vision
  - but in this specific Phase A run the divergence appears together with a near-broken one-sided market, so it is better interpreted as a degenerate policy than as an interesting emergent ecology
- third long PPO run with flat start plus quadratic inventory-risk penalty (`0.0005`):
  - PPO appears to over-correct away from inventory hoarding
  - live-view inspection suggests a cancel-heavy / non-participatory policy
  - scripted-vs-RL comparison confirms that `trend_01` ends effectively flat:
    - ending cash `10000`
    - ending inventory `0`
    - ending PnL `0.00`
    - open orders `0`
  - the market still differs from the scripted baseline because removing the trend participant changes the ecology, but the PPO slot no longer appears to express a meaningful trading policy
  - interpretation:
    - the quadratic risk penalty can suppress the buy-hoarding exploit
    - but by itself it creates a new trivial "stay away from the market" exploit
- latest Phase A reward redesign is now implemented:
  - the RL environment now uses `realized_pnl_delta` as the primary reward signal by default
  - equity-delta shaping is still available, but defaults to `0.0`
  - inactivity penalty is now available as an explicit reward-shaping tool
  - linear and quadratic inventory penalties still exist, but both remain optional and default to `0.0`
  - the env now reports richer RL step diagnostics:
    - `previous_realized_pnl`
    - `current_realized_pnl`
    - `realized_pnl_delta`
    - `learning_agent_trade_count`
    - `inactivity_penalty_applied`
  - training / evaluation CLIs were updated to expose this reward surface directly
  - focused verification passed after the redesign:
    - `40` targeted RL/live-session tests passed
- latest live-view diagnosis after the realized-PnL + inactivity run:
  - the PPO slot still did not produce real trades
  - live-view inspection showed repeated `limit_sell`-like behavior with zero inventory
  - the apparent stream of RL `CANCEL` events was misleading:
    - policy counters reflected requested actions
    - the event table reflected simulator-side cancel/reject events
  - this means the agent was likely requesting invalid sell intents rather than truly executing cancel-oldest as its main policy
- invalid-action masking is now implemented:
  - invalid sell actions with insufficient inventory are masked to `hold`
  - invalid `cancel_oldest` actions with no open orders are masked to `hold`
  - runtime RL diagnostics now distinguish:
    - effective action counts
    - requested action counts
    - masked / invalid action counts
    - last requested action type
    - invalid reason
  - the RL viewer now exposes masked-decision visibility so Phase A interpretation is less ambiguous
- the RL env/action layer has now been simplified for Phase A:
  - the training-facing gym wrapper defaults to a smaller action space:
    - `hold`
    - `market_buy`
    - `market_sell`
    - `limit_buy`
    - `limit_sell`
  - `cancel_oldest` is removed from the default Phase A training action set
  - fixed quantity and fixed limit offset are now explicit env settings, defaulting to `1` and `1`
  - the gym wrapper now exposes a state-dependent valid action mask for the current observation
  - this means the env is now ready for a future switch to true mask-aware PPO without another env rewrite
- MaskablePPO Phase A integration is now in place on the train/eval script side:
  - `scripts/train_rl_agent.py` now supports:
    - `--algorithm ppo|maskable_ppo`
    - explicit Phase A action-space flags
    - explicit cancel-action inclusion
    - fixed quantity / fixed limit-offset controls
  - `scripts/eval_rl_agent.py` now supports:
    - `--algorithm auto|ppo|maskable_ppo`
    - sidecar-based algorithm auto-detection from the training metadata JSON
    - mask-aware evaluation when a MaskablePPO checkpoint is loaded
  - `sb3-contrib` is now part of the RL dependency surface in:
    - `pyproject.toml`
    - `requirements.txt`
  - practical current note:
    - the next clean test path is training + evaluation + comparison first
    - live-view playback for MaskablePPO checkpoints is not yet the primary validated path, because the recent work focused on the training/eval integration boundary
- current interpretation after that redesign:
  - post-hoc invalid-action masking to `hold` was a useful diagnostic step
  - but by itself it is too blunt as a learning setup because invalid sell requests can still collapse into apparent inactivity
  - the simplified Phase A action space is meant to reduce this failure mode before we widen the policy language again
- latest live-view diagnosis after the first MaskablePPO Phase A run:
  - the PPO slot is now clearly active again
  - but it can still learn another one-sided degenerate behavior:
    - repeated `market_buy`
    - no meaningful selling
    - accumulation only when sell liquidity appears
  - this exposed a second important env-design issue:
    - the RL feature vector previously hid empty-book state by falling back from missing `best_bid` / `best_ask` to the midpoint
    - so the model was not told explicitly when one side of the book was empty
  - Phase A env validity is now tightened again:
    - `market_buy` is invalid when there is no ask liquidity
    - `market_sell` is invalid when there is no bid liquidity
    - explicit `has_best_bid` / `has_best_ask` features are now part of the RL observation
  - consequence:
    - old PPO / MaskablePPO checkpoints trained on the previous 16-feature observation space should now be treated as obsolete for Phase A
    - the next training run should start from a fresh checkpoint on the new 18-feature observation surface

Interpretation:

- the RL boundary is working
- the comparison tooling is working
- replacing one scripted slot materially changes the market
- PPO is now active enough to study
- but current PPO behavior still looks degenerate in both directions:
  - without risk shaping: profitable one-sided buy-hoarding
  - with quadratic risk shaping at `0.0005`: cancel-heavy / near-flat non-participation
  - with first-pass MaskablePPO and no empty-book aggressive masking: repeated market-buy pressure whenever ask liquidity appears
- the new Phase A hypothesis is now sharper:
  - inventory punishment alone is not enough
  - valid-action masking must also reflect available market liquidity, not only inventory and open-order constraints
  - the RL model needs explicit visibility into whether bid / ask liquidity exists at all
  - the current realized-PnL-led reward plus optional inactivity penalty is still the right reward path, but it now has to run on the improved liquidity-aware observation/mask surface
  - that experiment should still run on the simplified Phase A action space with no cancel action and fixed size/offset defaults
  - the env now exposes valid action masks, and the train/eval scripts support a true MaskablePPO path rather than more post-hoc hold substitution

Phase A / Phase B are now clearer:

- Phase A:
  - fixed preset
  - fixed seed
  - longer PPO training
  - live-view inspection with RL-only diagnostics
  - goal: confirm PPO can become meaningfully active
- Phase B:
  - multi-seed training episodes
  - unseen-seed evaluation
  - then comparisons become proper train/test experiments instead of single-world debugging

## Current Recommended Next Step

Run one more single-seed Phase A PPO experiment with the new reward path and the new liquidity-aware action mask before expanding to Phase B.

Priority order:

1. Train PPO again with:
   - flat start (`0` inventory)
   - realized-PnL-led reward
   - no quadratic inventory-risk penalty
   - a small inactivity penalty
   - simplified Phase A action space:
     - no cancel action
     - fixed quantity / limit offset
   - liquidity-aware masking:
     - no `market_buy` without asks
     - no `market_sell` without bids
   - liquidity-presence observation features:
     - `has_best_bid`
     - `has_best_ask`
   - true MaskablePPO enabled in training/evaluation
2. Evaluate and compare the new MaskablePPO checkpoint before doing more reward tweaks
3. Quantify the actual action mix during evaluation:
   - hold
   - market actions
   - limit actions
   - masked invalid actions
   - cancel actions
4. Only after the eval/compare results look promising, add live-view inspection back into the loop
5. Do not move to multi-seed yet; Phase A is still unresolved
6. Treat the quadratic inventory-risk penalty as insufficient by itself:
   - it removes hoarding
   - but it pushes PPO into a cancel/flat/no-op policy
7. The next reward iteration should encourage meaningful participation rather than only punishing inventory
8. If PPO becomes meaningfully active without breaking the market:
   - move to multi-seed training
   - evaluate on unseen seeds
9. If PPO remains degenerate or near-inactive:
   - adjust reward / action-space / diagnostics before starting Phase B

## Current Risks / Issues

### Worker Scope Drift

Some workers edited files outside their originally assigned ownership area.

Most of the resulting code is still useful, but this needs tighter discipline in future delegation.

### Shared Memory Integrity

`PROJECT_STATE.md` previously accumulated overlapping edits from multiple workers.

This file has now been cleaned and rewritten as the single authoritative state doc.

### Live Viewer Still Needs Product Polish

The live viewer is now materially better aligned with the target experience, but it is still an early terminal, not yet a polished market workstation.

Current remaining gaps:

- no true browser test coverage
- some market states remain visually sparse in early steps
- agent panels will likely need pagination / filtering once activity scales up
- compact workstation layout is in place, but interactive affordances are still minimal compared with a mature trading UI
- the live viewer still defaults to `horizon=10000` even when a preset has its own shorter intended horizon
- `run_market_health.py` now exposes a useful first-pass portfolio breakdown, but it is still not identical to the full live-session accounting view in every field
- the comparison tooling now exists, but it needs real scripted-vs-RL experimental use before we know which metrics are the most informative

### Model Boundary Drift

The first worker wave created overlapping concepts across:

- `core/`
- `exchange/`
- `analysis/`

For the next wave, do not introduce a third competing set of exchange or replay models.

Current canonical boundary decision:

- `exchange/` is the source of truth for execution-layer objects and matching behavior
- `analysis/` is the source of truth for replay/event-log serialization consumed by the replay CLI
- `core/` should be treated mainly as shared config / common metadata, not as a second exchange model layer

## Working Definition Of “Stable Market”

Before moving toward RL, the scripted market should satisfy most of the following:

- it produces non-trivial trading activity
- the book is not empty most of the time
- price moves over time rather than staying constant
- spreads and depth are finite and interpretable
- news visibly affects behavior
- informed and uninformed agents behave differently
- agents can fail / deactivate under bad conditions
- replay plots make intuitive sense
- no obvious accounting bugs appear

## Delegation Policy For Future Coding

Preferred future split:

- one worker for `portfolio/`
- one worker for `market/`
- one worker for `agents/`
- one worker for tighter replay / visualization

Rules:

- give each worker a disjoint write scope
- avoid worker edits to `PROJECT_STATE.md` unless explicitly requested
- director handles integration, review, and state updates

## Open Questions

These do not block the next coding slice, but they will matter soon:

- exact latent fundamental dynamics
- exact news scheduling model
- exact news impact scaling for the `high_news` regime
- exact private-signal noise model
- exact episode horizon definition
- exact RL insertion boundary for the first controlled comparison
- exact public identity exposure on the tape

## Locked Visibility Direction

- Replay quality is a first-class requirement of the project, not an optional add-on.
- Static images alone are not sufficient for the project's needs.
- The next visibility milestone should be a live local web market view.
- The target experience should feel closer to watching a market on a website than reading saved plots after the fact.
- Python replay/animation remains useful, but it is now secondary to a real-time browser-based viewer.

### Locked Live Viewer Requirements

- Main chart should support both:
  - candlesticks
  - line chart
- The order book view should show:
  - top 10 bid levels
  - top 10 ask levels
  - and ideally allow expansion to the full visible book
- The live UI should include:
  - an agent actions panel
  - agent portfolio state
  - unrealized PnL
  - realized PnL
- The viewer should support both:
  - automatic live playback
  - manual step controls
- The closer the live viewer feels to a real market screen, the better.

## Immediate Next Step

The next coding / testing milestone should be:

> run the first scripted-only vs PPO-controlled comparison with `trend_01` replaced at runtime.

Implementation rule for that next step:

- do not delete the scripted trend agent from the project
- use slot replacement at runtime only
- keep the market contract fixed while comparing scripted-only vs PPO
- use the existing health / comparison tooling rather than inventing a new reporting path
- use the live viewer as part of the first PPO inspection loop, not only terminal reports

## Active Execution Plan

The current work should proceed in this order:

### Step 1: Market-Health Diagnostics

Goal:

- define a compact set of health metrics for the scripted market so tuning can be evidence-driven

Target outputs:

- reusable health-summary function(s)
- tests around the health summary contract
- metrics that are easy to compare across runs

### Step 2: Scenario / Preset Scaffolding

Goal:

- make it easy to run named market worlds without hand-editing code

Target outputs:

- baseline preset
- at least a few stress-style preset variants
- clean config entry points for future experiments

### Step 3: Ecology Diagnosis

Goal:

- run the current scripted ecology through the new diagnostics and identify the main failure modes

Expected questions:

- does liquidity persist
- do spreads remain interpretable
- do agents deactivate too quickly
- does news produce visible but not pathological behavior
- does one agent type dominate too strongly

### Step 4: Ecology Tuning

Goal:

- tune the scripted agents and market-process parameters against the diagnostics

Expected focus areas:

- market-maker quoting behavior
- noise-trader aggressiveness
- trend-follower thresholding
- informed-trader edge
- news cadence / impact
- deactivation harshness

Current diagnosis from the first ecology pass:

- the single market maker is currently the main liquidity governor
- noise, trend, and informed thresholds are the strongest immediate behavior levers
- deactivation is not yet the dominant instability source
- news matters, but appears secondary to liquidity and policy thresholds in the present ecology

## Current Practical Testing Loop

The current intended workflow is:

1. choose a preset
2. inspect its compact health report in terminal
3. launch the same preset in the live viewer when visual inspection is needed
4. compare behavior and metrics before making tuning changes

Current useful entrypoints:

- `scripts/run_market_health.py`
- `scripts/run_market_demo.py`
- `scripts/serve_market_view.py`

### Step 5: Stability Check Before RL

Goal:

- confirm that the scripted market is stable enough to justify adding the first RL agent

## Chronological Research Log

This section is the chronological history of the important RL / market-ecology implementation steps and the main findings from each one.

### Scripted Market Foundation

- We first built the deterministic synthetic market with:
  - one asset
  - central limit order book
  - spot-only portfolio accounting
  - hidden fundamental
  - public news
  - scripted agent ecology
- The live viewer, health-report CLI, and compare tools were then added so market behavior could be inspected visually and compared scientifically.
- Key early result:
  - fixed preset + seed + horizon produced reproducible runs across the live viewer and health scripts
  - this confirmed that scripted-only vs scripted+RL comparisons would be scientifically meaningful later

### Scripted Regime Validation

- We added scenario presets:
  - `baseline`
  - `fragile_liquidity`
  - `high_information_asymmetry`
  - `high_news`
- We used health summaries and live-view testing to validate whether these regimes were truly distinct.
- Main conclusions:
  - `baseline` became the control world
  - `fragile_liquidity` was clearly distinct
  - `high_information_asymmetry` was clearly distinct
  - `high_news` initially was not distinct enough, then was reworked so it differed through stronger market/news impact rather than just horizon assumptions

### First RL Boundary

- We introduced the first single-agent RL boundary by replacing `trend_01` at runtime only.
- This preserved the scripted market while allowing a clean comparison:
  - scripted-only world
  - same world with one RL-controlled slot
- We added:
  - training script
  - evaluation script
  - compare tooling
  - live-view PPO playback
- Early PPO result:
  - the RL slot changed the market
  - but the first policy was mostly too passive

### Zero-Inventory RL Start

- We changed the RL slot to start flat (`0` inventory) so any later PnL had to come from actual decisions rather than inherited position drift.
- This successfully woke the agent up.
- New failure mode:
  - the agent became active
  - but learned a one-sided buy-hoarding behavior
  - it accumulated inventory, distorted the book, and damaged market quality

### Inventory-Risk Penalty Experiment

- We then added quadratic inventory-risk shaping to discourage hoarding.
- Result:
  - this removed the buy-hoarding exploit
  - but pushed PPO toward a trivial non-participatory / cancel-heavy / flat policy
- Conclusion:
  - inventory punishment alone is not enough
  - it suppresses bad accumulation, but does not teach healthy participation

### Realized-PnL Reward Redesign

- Reward was redesigned so realized PnL became the primary signal, with inactivity penalty available and inventory penalties optional.
- This was meant to encourage opening and closing positions rather than drifting on mark-to-market gains.
- New observation:
  - the RL slot was still choosing many invalid or non-productive actions
  - so reward redesign alone was not enough

### Invalid-Action Visibility And Simplified Phase A Action Space

- We added diagnostics that distinguish:
  - requested action
  - effective action
  - masked invalid actions
  - invalid reasons
- We simplified Phase A by removing `cancel_oldest` from the default training action set and fixing quantity / limit offset.
- This reduced the number of degenerate policy loops and made the RL diagnostics much easier to interpret.

### MaskablePPO Integration

- We switched the Phase A training/evaluation path to support `MaskablePPO`.
- This allowed the policy to sample only valid actions instead of relying only on post-hoc conversion to `hold`.
- New observation:
  - this improved validity handling
  - but the first MaskablePPO policies still found one-sided behaviors rather than balanced trading

### Liquidity-Aware RL Masking

- We discovered that the RL feature vector was hiding empty-book state by replacing missing `best_bid` / `best_ask` with the midpoint.
- We fixed this by:
  - adding `has_best_bid`
  - adding `has_best_ask`
  - masking `market_buy` when there is no ask liquidity
  - masking `market_sell` when there is no bid liquidity
- Conclusion:
  - RL now has a cleaner notion of whether aggressive market action is even feasible
  - this was necessary because otherwise the model could spam aggressive actions into a one-sided or empty book

### Current Market-Ecology Finding

- The current important live-view finding is broader than the RL slot alone:
  - the market can enter a one-sided state where bids remain but asks disappear
  - volume can go to zero for a long time
  - retail and informed agents can run out of inventory and stop supplying the sell side
  - the RL agent is currently also not providing healthy sell-side recycling
  - the market maker does not rescue the ask side aggressively enough
- Important clarification:
  - the chart can still move during these zero-volume regimes because the simulator currently falls back from missing midpoint to the latent fundamental when writing the displayed price series
  - so the chart is not currently a pure "last traded price" chart

### Chart Semantics Fix

- We decided that the main live chart should follow the latest traded price, not midpoint-or-fundamental fallback.
- Implemented change:
  - the simulator now keeps the chart price series as the latest traded price
  - if no new trade occurs, the chart price stays flat
  - midpoint is still retained separately in the live payload for market-state interpretation
  - fundamental remains a separate series
- Consequence:
  - dead / frozen markets should now look dead on the main chart instead of appearing to drift with the latent fundamental

### Step-Based Timeframe Selector

- After the chart-price fix, we added a step-based timeframe selector to the live viewer.
- This behaves like TradingView-style candle aggregation, but in simulator steps rather than real clock time.
- Current viewer options:
  - `1`
  - `5`
  - `10`
  - `25`
  - `50`
  - `100`
- Implementation detail:
  - aggregation is done client-side from the per-step chart series
  - candles now rebuild OHLC from the traded-price series
  - flat / no-trade periods naturally collapse into line-like candles
- Goal:
  - keep microstructure visible at small step windows
  - make broader market regimes easier to inspect at larger aggregation windows

### Chart History And Panning

- We then discovered that high timeframes were not actually showing more market history.
- Root cause:
  - the live backend was only sending a bounded recent history window
  - the frontend was also always anchoring the chart to the latest candles
- Implemented change:
  - live chart history limit was expanded substantially
  - the chart now supports dragging/panning back to older candle windows
  - higher aggregation windows can now reveal more historical candles instead of only compressing the same newest segment
- Goal:
  - make the viewer behave more like a real charting tool
  - let larger step-based timeframes function as broader historical context, not just compact latest-history views

### Chart Interaction Fixes

- After testing the first panning/timeframe version, we found two UX bugs:
  - drag direction felt inverted
  - volume bars did not scale correctly across the full high-timeframe history
- Fixes applied:
  - chart dragging now behaves like dragging the chart itself:
    - move right to go back in time
    - move left to come back toward the present
  - full per-step chart history now carries volume into the live chart line series
  - higher timeframes now aggregate volume from the full visible history rather than only from a small recent trade window

### Current Interpretation

- The current blocker is no longer only "make RL active."
- The deeper problem is:
  - market ecology can freeze into a bid-only, zero-volume state
  - the chart semantics can partially hide that freeze by falling back to the fundamental
  - the market maker is still too passive and inventory-anchor-based to restore two-sided liquidity reliably

### Current Recommended Direction

- The next design wave should focus on liquidity health, not only PPO training length.
- Strong candidates:
  - chart semantics:
    - make the main chart track latest traded price
    - if no new trade occurs, the chart should flatline rather than silently following the fundamental
    - midpoint and fundamental should remain visible as separate series when useful
  - market-maker redesign:
    - require persistent two-sided quoting when resources allow
    - add an emergency inventory-offload behavior when the maker is long and buy-side liquidity is deep
    - allow more aggressive ask replenishment when the ask side disappears
  - non-maker inventory recycling:
    - informed / retail / trend policies should be more willing to sell out of long inventory rather than only accumulate
    - positions should have clearer exit logic
- Phase A RL work should continue, but now with the understanding that a market-liquidity redesign is likely required in parallel.

### Market Maker V2 Implementation

- We implemented the first dedicated liquidity-health redesign wave, focused only on the market maker.
- Core architectural change:
  - `ScriptedAgent.decide()` now conceptually supports returning multiple intents per decision step
  - the simulator now normalizes steady-state decisions into a tuple of intents and submits them sequentially in deterministic order
  - this preserves the existing reservation / logging / matching path while allowing one agent decision to express more than one quote
- Backward-compatibility choice:
  - the simulator still normalizes `None` / single-intent return values safely, so older agent paths and RL wrappers are not broken by the interface widening

### Market Maker V2 Behavior

- The market maker was redesigned around persistent two-sided liquidity rather than one-sided inventory-anchor toggling.
- New maker modes:
  - normal two-sided quoting when resources and resting-order capacity allow
  - inventory-skew mode:
    - keep both sides alive
    - widen / shrink side-specific padding based on inventory imbalance
    - skew quote size toward the side that helps rebalance inventory
  - empty-side restoration mode:
    - if ask side is empty and the maker has inventory, force an urgent ask quote
    - if bid side is empty and the maker has cash, force an urgent bid quote
    - this uses tighter restoration padding but still stays limit-only
- Important design choice:
  - maker v2 does **not** use market orders
  - maker v2 still degrades gracefully to one-sided quoting only when:
    - inventory is insufficient for asks
    - cash is insufficient for bids
    - or resting-order capacity is too constrained to support both sides

### Market Maker V2 Config Surface

- We extended `MarketMakerBehaviorConfig` with optional maker-v2 tuning knobs:
  - `inventory_tolerance`
  - `min_quote_size`
  - `max_quote_size`
  - `bid_padding_ticks`
  - `ask_padding_ticks`
  - `inventory_skew_strength`
  - `inventory_size_decay`
  - `empty_side_padding_ticks`
- Compatibility rules:
  - legacy `inventory_anchor`, `quote_size`, and `quote_padding_ticks` still work
  - side-specific bid/ask padding overrides symmetric `quote_padding_ticks` when present
  - omitted new fields still fall back to sane defaults
  - existing presets remain valid without needing to set all new maker-v2 knobs

### Market Maker V2 Test Coverage

- Added / updated tests for:
  - normal maker two-sided quotes
  - inventory-skew behavior that keeps both sides alive
  - empty ask-side restoration
  - empty bid-side restoration
  - graceful one-sided degradation under inventory constraint
  - simulator acceptance of multiple intents from one decision step
  - maker-config override propagation
  - side-specific padding precedence over symmetric padding
- Current targeted verification status:
  - `62` focused tests passing

### Market Maker V2 Smoke Checks

- Short post-implementation health-report smoke runs:
  - `baseline`, seed `7`, horizon `5000`
    - `trades=2123`
    - `spread_availability=0.579`
    - `mean_spread=0.0801`
  - `fragile_liquidity`, seed `7`, horizon `5000`
    - `trades=1676`
    - `spread_availability=0.199`
    - `mean_spread=0.0262`
- Interpretation:
  - the baseline market remains active after maker-v2 integration
  - fragile liquidity still looks distinct from baseline
  - this is only an initial smoke check, not full validation of freeze reduction yet

### Next Validation Task

- The next thing we need to inspect is whether maker v2 actually reduces the long bid-only / zero-volume freeze in longer live-view and health runs.
- In particular we should check:
  - whether ask-side disappearance becomes materially rarer
  - whether the maker now repopulates asks in the dead-book states that RL previously exposed
  - whether baseline remains healthier without collapsing fragile-liquidity into the same regime

### Maker V2 Validation In RL Context

- We then validated maker-v2 in the context that originally exposed the liquidity problem:
  - baseline scripted-only sanity check
  - live viewer with RL agent inserted
  - evaluation and scripted-vs-RL comparison
- Main result:
  - maker-v2 appears to have improved the original liquidity-freeze failure mode
  - the market no longer collapses as easily into a dead bid-only / zero-volume state when the RL agent applies persistent buy pressure
  - informed and retail agents are able to keep recycling inventory because the maker now restores sell-side liquidity more reliably
- Important interpretation:
  - maker-v2 helped the **market survive**
  - but it did **not** solve RL behavior by itself

### Fresh RL Retrain In Maker-V2 Market

- After the market-maker redesign, we retrained the RL agent from scratch in the updated baseline market.
- This was important because the old checkpoint had been trained in a different market structure and was no longer a fair measure of what PPO could learn in the new environment.
- New milestone reached:
  - the RL agent now genuinely participates using both buys and sells
  - it is no longer stuck in the earlier failure modes of:
    - pure inactivity
    - buy-hoarding without exits
    - invalid-action loops
- Observed nuance:
  - the agent currently prefers selling via limit orders
  - it uses both market and limit orders on the buy side

### Seen-Seed RL Result

- On the training / seen seed (`baseline`, seed `7`, horizon `10000`):
  - trades increased strongly:
    - `3151 -> 4618`
  - spread availability improved:
    - `0.394 -> 0.565`
  - final midpoint and final fundamental stayed very close:
    - midpoint `130.3450`
    - fundamental `130.0584`
  - the RL agent realized much more PnL than the scripted trend baseline
- Interpretation:
  - on the seen seed, PPO is no longer merely active
  - it appears to have learned a usable participation policy in the maker-v2 world
  - this is the first clearly successful Phase-A participation result

### Unseen-Seed RL Result

- We then evaluated the same fresh checkpoint on an unseen seed (`baseline`, seed `8`, horizon `10000`).
- Main findings:
  - trades still increased strongly:
    - `2726 -> 4140`
  - spread availability also improved strongly:
    - `0.293 -> 0.489`
  - but total market equity became much worse:
    - `52601.88 -> 49498.06`
  - the RL-controlled `trend_01` underperformed badly relative to scripted baseline
- Interpretation:
  - the policy generalizes in **participation**
  - it does **not yet generalize in quality**
  - PPO has learned to be in the market, but not yet how to behave robustly across different synthetic worlds

### Current RL Behavioral Interpretation

- Current RL behavior is much better than earlier versions, but still structurally biased:
  - the policy can now buy and sell
  - however it still tends to accumulate long inventory too easily
  - when the world turns against that long bias, the RL agent can become heavily negative
- Important practical reading:
  - the current blocker is no longer "can RL participate at all?"
  - the current blocker is:
    - inventory/risk management
    - closing behavior
    - robustness across unseen seeds

### Current Design Conclusions

- We are now in a much stronger position than before:
  - market infrastructure is solid
  - charting / live diagnostics are useful
  - maker-v2 stabilized liquidity meaningfully
  - PPO can participate as a real market actor
- The next priority is no longer basic activation of PPO.
- The next priority is:
  - make PPO robust across different seeds / market realizations
  - then improve reward shaping so it closes positions more intelligently instead of leaning long too often

### Immediate Next Direction

- Multi-seed RL training is now justified.
- Earlier in the project, multi-seed training was deferred because PPO was not yet producing meaningful market behavior even on one fixed seed.
- That condition has changed:
  - PPO now trades
  - PPO changes liquidity and activity materially
  - PPO shows a clear difference between seen-seed and unseen-seed performance
- Therefore the next step should be:
  - train on multiple seeds
  - evaluate on held-out seeds
  - compare whether the policy becomes less regime-specific

### Deferred But Important Future Direction

- We discussed adding more agents and eventually moving from spot-only to a futures-like market with shorting.
- Current decision:
  - **do not** add more agents immediately
  - **do not** switch to futures immediately
- Reason:
  - the current 4-agent spot ecology has only just become interpretable and trainable
  - unseen-seed robustness is not solved yet
  - futures / shorting would introduce a major new layer:
    - margin logic
    - liquidation / leverage dynamics
    - different ruin mechanics
    - more complex reward / risk behavior
- However, this remains an important later milestone because:
  - spot-only agents cannot profit from downward markets via shorting
  - a futures-style environment would be more expressive for studying richer strategic behavior

### Multi-Seed Training Support

- After the fresh maker-v2 PPO results, we decided the project had reached the point where multi-seed training was justified.
- Reason:
  - PPO now participates meaningfully
  - PPO buys and sells in the market
  - the main weakness is no longer "basic activation"
  - the main weakness is robustness across unseen seeds
- Implemented change:
  - the RL environment now supports an explicit `train_seeds` schedule
  - if a seed list is provided, each reset cycles deterministically through that list
  - if no seed list is provided, the previous auto-increment / single-seed behavior remains available
- Training-script support:
  - `scripts/train_rl_agent.py` now accepts `--train-seeds`
  - training metadata now records the explicit multi-seed schedule used for the checkpoint
- Important implementation detail:
  - the environment no longer silently consumes the first scheduled seed during constructor bootstrap
  - the first real training reset now starts on the first requested seed
- Current interpretation:
  - we now have the minimum infrastructure needed for:
    - train on multiple seeds
    - evaluate on held-out seeds
    - compare whether PPO becomes less regime-specific

### First Multi-Seed PPO Results

- We trained the first explicit multi-seed PPO policy on the updated maker-v2 baseline market.
- Seed curriculum used during training:
  - `1,2,3,4,5,6,7,8`
- Evaluation was then run on:
  - seen seeds:
    - `7`
    - `8`
  - unseen seeds:
    - `20`
    - `21`
    - `22`

### Multi-Seed PPO Behavioral Change

- Main qualitative finding from the live viewer:
  - the multi-seed PPO policy is noticeably more conservative than the earlier single-seed PPO policy
  - it still buys a lot
  - but it now sells much more often as well
  - it still appears to prefer selling via limit orders rather than market sells
- Interpretation:
  - multi-seed training improved behavioral robustness
  - PPO is no longer only learning a narrow, one-seed trading rhythm
  - it is learning a more stable participation style

### Multi-Seed PPO Seen-Seed Results

- On seen seeds (`7` and `8`), the multi-seed policy:
  - still increased trade count materially over scripted baseline
  - still improved spread availability and top-of-book occupancy strongly
  - generally reduced volatility relative to the scripted baseline
- However, the RL-controlled `trend_01` no longer outperformed on PnL.
- In both seen-seed evaluations:
  - the RL slot ended with much smaller inventory than the scripted trend baseline
  - the RL slot held much more cash
  - but the RL slot’s final equity / PnL was much worse than scripted trend
- Interpretation:
  - multi-seed training improved robustness and participation style
  - but it made the policy more conservative and less profitable

### Multi-Seed PPO Unseen-Seed Results

- On unseen seeds (`20`, `21`, `22`), the same pattern continued:
  - trade count increased materially over scripted baseline
  - spread availability improved materially
  - top-of-book occupancy improved materially
  - volatility generally decreased
- But across all unseen seeds:
  - final total equity of the market remained worse than the scripted-only baseline
  - the RL-controlled `trend_01` underperformed badly relative to scripted trend
  - the RL slot carried much smaller inventory and much higher cash than scripted trend
- Interpretation:
  - the policy now generalizes much better in **participation**
  - but it still does not generalize well in **profitability**

### Current Multi-Seed PPO Conclusion

- Multi-seed training was still the right move.
- It appears to have solved an important research problem:
  - PPO behavior is now more stable and less pathologically seed-specific
- But it also made the current limitation clearer:
  - the policy participates more robustly than before
  - yet it still fails to extract good value for itself
  - and it does not exploit or react to downside states strongly enough

### Current Market-Ecology Conclusion

- The current remaining issue is no longer just the RL reward.
- A deeper ecology issue is still visible in the live viewer:
  - there are still periods where very few agents want to sell
  - even when the latent fundamental is well below the traded price
  - scripted agents can remain too reluctant to provide downward pressure
- Important interpretation:
  - maker-v2 improved liquidity survival
  - PPO multi-seed training improved behavioral robustness
  - but the **scripted sell side is still too weak**

### Immediate Next Direction After Multi-Seed PPO

- The next likely improvement wave should focus on scripted sell behavior / downside reaction.
- Most promising targets:
  - informed trader:
    - stronger sell response when price is above fundamental or news is negative
  - noise trader:
    - better inventory recycling so it does not remain too buy-dominant
  - market maker:
    - keep current two-sided restoration, but potentially lean asks more strongly when price looks rich
- Current decision:
  - do **not** change the RL architecture yet
  - do **not** move to futures yet
  - first improve scripted sell pressure in the spot ecology

### Scripted Sell-Pressure Wave 1: Informed Trader

- We started the scripted sell-behavior improvements one agent at a time, beginning with the informed trader.
- Reason:
  - this is the cleanest place to strengthen "price is rich, sell harder" behavior without muddying the rest of the ecology
  - it directly addresses the observed problem where price can remain above the latent fundamental without enough downside pressure
- Implemented change:
  - informed trader now has stronger sell asymmetry through three new knobs:
    - `sell_bias`
    - `negative_news_sell_bias`
    - `inventory_pressure`
  - behaviorally, the informed trader now sells more decisively when:
    - price is above fundamental
    - negative news hits while it is long
    - it is carrying inventory into an overpriced state
- Backward-compatible config support was added through `InformedTraderBehaviorConfig`, and simulator wiring now passes the new knobs through when configured.
- Initial verification:
  - focused scripted/config tests passed
  - quick baseline health smoke run remained active
- The next step is to test this wave in:
  - scripted-only baseline runs
  - then the existing RL market setup, to see whether no-seller / weak-downside episodes become rarer before touching retail or trend

### Scripted Sell-Pressure Wave 1 Results

- We evaluated the first informed-trader sell-pressure wave in both scripted-only and RL-inserted baseline runs.
- Scripted-only baseline (`seed=7`, `horizon=10000`) remained healthy and active:
  - `trades=3472`
  - `spread_availability=0.428`
  - `mean_spread=0.0987`
  - `final_total_equity=52581.90`
- In that scripted-only run:
  - `informed_01` finished strongly positive and fully sold out of inventory
  - `maker_01` retained some inventory instead of fully emptying out
  - `trend_01` was still the strongest long accumulator
- Interpretation:
  - the informed-trader change did not destabilize the baseline market
  - it appears to have increased sell-side/value-realization behavior without breaking activity

### RL Regression After Informed-Trader Change

- We then re-ran the existing multi-seed RL checkpoint in the updated ecology.
- On the tested RL comparison (`baseline`, `seed=20`, `horizon=10000`):
  - trades still increased over scripted baseline:
    - `2684 -> 3141`
  - spread availability still improved strongly:
    - `0.310 -> 0.757`
  - but final total equity remained much worse than scripted baseline:
    - `51517.24 -> 48426.34`
  - the RL-controlled `trend_01` still underperformed badly
- Interpretation:
  - the informed-trader sell improvement is directionally useful
  - but it is not sufficient on its own to solve the no-seller / weak-downside episodes

### Updated Ecology Diagnosis

- The live viewer showed an important remaining failure mode:
  - latent fundamental can move well below traded price
  - `informed_01` may already be fully out of inventory
  - `maker_01`, `retail_01`, and the RL slot can still hold substantial inventory
  - yet sell pressure can still remain too weak or too passive
- Important reading:
  - once the informed trader has already sold out, it cannot keep anchoring price downward
  - this means the remaining problem is now a broader ecology issue, not just an informed-trader issue

### Current Conclusion After Wave 1

- The informed-trader sell-pressure change was good and worth keeping.
- It passed the first sanity check:
  - baseline stayed healthy
  - the change moved the ecology in the right direction
- But it did not finish the job.
- The next likely scripted targets should therefore be:
  - `retail_01`:
    - better inventory recycling / more willingness to sell into strength or when long
  - `trend_01` scripted baseline:
    - better long-exit logic when momentum weakens or price looks rich
- Current preferred sequencing:
  - continue improving scripted sell behavior agent-by-agent
  - then retrain / re-evaluate the RL agent in the healthier ecology

### Scripted Sell-Pressure Wave 2: Retail / Noise Trader

- We then applied the second scripted sell-pressure wave to `retail_01`.
- Design goal:
  - keep retail simple and non-intelligent
  - but make it recycle inventory more often instead of remaining structurally too buy-dominant
- Implemented change:
  - retail now has configurable sell-leaning terms for:
    - base sell bias
    - inventory recycling
    - selling when price looks rich versus fundamental
    - simple profit-taking into positive recent returns
- New retail-side knobs:
  - `sell_bias`
  - `inventory_recycling_bias`
  - `overpricing_sell_bias`
  - `profit_taking_bias`
- Compatibility:
  - defaults preserve the original retail personality as much as possible
  - these new terms only tilt side choice; they do not turn retail into a value agent
- Initial verification:
  - focused scripted/config tests passed
  - quick baseline smoke run stayed active
- Next validation target:
  - re-check whether the "nobody wants to sell" episodes become rarer
  - especially when informed is already flat and maker / retail inventory is still elevated

### Scripted Sell-Pressure Wave 3: Trend Follower

- We then applied the third scripted sell-pressure wave to the scripted `trend_01` baseline agent.
- Design goal:
  - stop trend from remaining a persistent long accumulator with weak exit behavior
  - make it unwind stale longs when momentum weakens or when price looks rich versus fundamental
- Implemented change:
  - trend now has configurable exit terms for:
    - weaker negative-momentum exit threshold
    - exit pressure when price is above fundamental
    - stronger sell quantity scaling when it is already holding inventory
- New trend-side knobs:
  - `exit_threshold_bps`
  - `overpricing_exit_bias`
  - `inventory_pressure`
- Compatibility:
  - defaults preserve the original trend-following personality as much as possible
  - this wave is about better exits from longs, not converting trend into an informed/value agent
- Initial verification:
  - focused scripted/config tests passed
  - quick baseline smoke run stayed active
- Immediate next validation target:
  - re-run the scripted-only baseline
  - re-run the current RL checkpoint in the improved ecology
  - then decide whether the environment is ready for PPO retraining

### Scripted Sell-Pressure Wave 3 Results

- We evaluated the scripted trend-follower exit update in both scripted-only and RL-inserted baseline runs.
- Scripted-only baseline (`seed=7`, `horizon=10000`) remained very active and substantially healthier than earlier phases:
  - `trades=5094`
  - `spread_availability=0.594`
  - `mean_spread=0.1089`
  - `final_total_equity=52079.51`
  - `final_midpoint=128.9450`
  - `final_fundamental=128.5054`
- In that scripted-only run:
  - `maker_01` ended strongly positive while still holding some inventory
  - `informed_01` remained the strongest performer, which is believable given its structural informational edge
  - `retail_01` ended with much lower inventory than before
  - scripted `trend_01` no longer acted like a persistent long-inventory sink:
    - `inventory 16 -> 1`
    - realized and unrealized exposure were both very small by the end
- Interpretation:
  - the trend exit change achieved its main ecology goal
  - the scripted baseline now has much stronger natural inventory recycling and better price/fundamental alignment

### RL Comparison After Trend-Follower Change

- We then re-ran the current multi-seed PPO checkpoint in the improved scripted ecology.
- On the tested RL comparison (`baseline`, `seed=20`, `horizon=10000`):
  - trades were now lower than the improved scripted-only baseline:
    - `5057 -> 4272`
  - spread availability still improved slightly:
    - `0.581 -> 0.637`
  - final total equity was still worse than scripted-only:
    - `50896.01 -> 49251.10`
  - the RL-controlled `trend_01` still underperformed the new scripted baseline agent badly
- Interpretation:
  - this does not mean PPO is failing in absolute terms
  - it means the scripted benchmark has materially improved, while the PPO checkpoint is stale because it was trained in an older ecology

### Current Ecology Reading After Waves 1-3

- The scripted environment is now in a meaningfully better place:
  - informed trader sell/value behavior improved
  - retail inventory recycling improved
  - scripted trend long-exit behavior improved
  - the market remains active, interpretable, and much less dominated by one-way inventory accumulation
- Remaining notes:
  - trade-price candles still look step-like / flat-then-jump in places, which is expected in a small discrete agent ecosystem
  - `maker_01` can still take inventory and PnL pain while acting as the liquidity stabilizer
  - `informed_01` often outperforms other participants, which is plausible because it has the strongest structural informational edge

### Current Conclusion After Waves 1-3

- Keep all three scripted sell-pressure changes:
  - informed trader
  - retail / noise trader
  - scripted trend follower
- Do not re-edit the market maker yet.
- The next correct step is to retrain the multi-seed PPO agent in this improved ecology.
- Current strategic sequencing:
  - retrain PPO in the healthier scripted market
  - compare seen and unseen seeds again
  - only then decide whether more scripted tuning, more agents, or larger market-structure changes are needed

### PPO Retrain In Improved Scripted Ecology

- We retrained the multi-seed PPO agent after the scripted sell-pressure waves were in place.
- Training setup:
  - baseline preset
  - RL agent still replacing `trend_01`
  - multi-seed training across `1,2,3,4,5,6,7,8`
  - evaluation on both seen and unseen seeds
- Result:
  - the new PPO checkpoint is now clearly stronger than earlier PPO generations
  - it no longer looks like a dead/no-op policy or a simple one-sided hoarding failure
  - it appears to have learned a coherent market-winning behavior in the current spot ecology

### Seen-Seed PPO Result After Retrain

- On the seen-seed comparison (`seed=7`, `horizon=10000`):
  - PPO underperformed the new scripted market on total market activity:
    - `trades 5094 -> 3619`
    - `spread_availability 0.594 -> 0.421`
  - but the RL-controlled `trend_01` itself strongly outperformed the scripted trend baseline:
    - scripted `trend_01` PnL: `-279.89`
    - RL `trend_01` PnL: `+892.50`
  - the RL slot accumulated meaningfully more inventory than the scripted trend:
    - scripted inventory: `1`
    - RL inventory: `37`
- Interpretation:
  - PPO is not improving the ecology at the market-wide level here
  - but it is improving the competitive outcome of the replaced participant
  - the learned policy appears to accumulate inventory and monetize later exits better than the scripted trend baseline

### Unseen-Seed PPO Result After Retrain

- On the unseen-seed comparison (`seed=20`, `horizon=10000`):
  - PPO again reduced aggregate activity relative to the improved scripted market:
    - `trades 5057 -> 3514`
    - `spread_availability 0.581 -> 0.419`
  - yet the RL-controlled `trend_01` again strongly outperformed the scripted trend baseline:
    - scripted `trend_01` PnL: `-381.78`
    - RL `trend_01` PnL: `+1091.73`
  - inventory concentration remained high:
    - scripted inventory: `7`
    - RL inventory: `78`
- Interpretation:
  - the PPO policy generalizes its participant-level edge to at least one unseen seed
  - this is an important milestone: PPO is no longer only "participating"; it is now actually winning versus the scripted replacement baseline
  - however, it seems to do so partly by becoming the dominant inventory holder / liquidity controller

### Current Reading Of PPO Strategy

- Live-view interpretation and metric behavior suggest a consistent PPO style:
  - aggressively acquire inventory, often via market buys
  - hold inventory while other participants deplete supply
  - sell mainly through passive limit orders when demand later reappears
- This likely explains why:
  - market sells from PPO remain rare
  - price can stay artificially flat even when the latent fundamental weakens
  - PPO can still outperform the scripted trend baseline while leaving the broader ecology less active
- Important nuance:
  - PPO is "smart" relative to the current market ecology, not necessarily "realistic" yet
  - it appears to exploit the fact that most other participants are still too constrained, too spot-only, and too easy to buy out

### Current Strategic Conclusion After PPO Retrain

- This PPO version is worth saving as a milestone.
- It demonstrates:
  - participant-level PPO competitiveness
  - seen/unseen generalization of the learned edge
  - a clear learned strategic style rather than random activity
- But it also exposes the next structural limitation of the market:
  - too few participants can continue to supply or sell once PPO corners inventory
  - spot-only constraints make it impossible for agents to express bearish views unless they already hold supply
- Therefore, the next research/design frontier should move from "can PPO learn?" to:
  - how to make the ecology harder, richer, and more realistic
- Candidate next directions now considered plausible:
  - add stronger / smarter opposing participants
  - add additional AI agents
  - eventually move toward a futures / long-short market structure so agents can always express sell-side views

### Bridge To MARL Roadmap

- We then agreed on a step-by-step bridge from the current single-RL-agent setup toward MARL:
  1. keep scripted `trend_01` in the market and add the PPO agent as a fifth participant
  2. only after studying that 5-agent ecology, add a second AI-controlled participant
- Important framing:
  - a 5-agent market with one PPO agent added on top of the scripted ecology is still primarily a "single learning agent in a richer multi-agent world"
  - adding a second AI agent where one policy is frozen and the other learns is a useful asymmetric stepping stone toward MARL
  - only when multiple AI agents learn together do we enter the stronger full-MARL regime
- Current preferred sequencing:
  - first implement the 5-agent bridge cleanly
  - then evaluate the current PPO in that richer market
  - then decide whether to train a second AI opponent or move into joint-learning work

### 5-Agent RL Bridge Implementation

- We implemented explicit support for adding the RL policy as a new market participant instead of only replacing an existing scripted slot.
- New runtime/training/evaluation mode:
  - `replace`:
    - old behavior, PPO replaces an existing scripted slot
  - `add`:
    - PPO is attached to a newly added participant cloned from an existing scripted template
- This is exposed through new CLI controls:
  - `--add-learning-agent`
  - `--learning-agent-template-id`
- The intended first use is:
  - keep scripted `trend_01`
  - add a new PPO-controlled `rl_01`
  - clone `rl_01` from the scripted `trend_01` template before replacing that new slot with the PPO policy at runtime
- Verification:
  - focused train/eval/live CLI tests passed
  - compile checks passed
- Immediate next validation target:
  - run the current PPO checkpoint in the new 5-agent market
  - compare that against the improved scripted-only baseline
  - inspect whether keeping scripted trend alive changes PPO's inventory-control dominance

### 5-Agent RL Bridge Results

- We evaluated the current PPO checkpoint in a richer 5-agent market:
  - keep scripted `trend_01`
  - add PPO as `rl_01`
  - `rl_01` is cloned structurally from the trend-follower template, then runtime-controlled by the PPO policy
- This is an important bridge result because the PPO checkpoint was trained in a 4-agent replacement setting, yet still remained competitive when inserted as an extra fifth participant.

### Seen-Seed 5-Agent Result

- On the seen-seed comparison (`seed=7`, `horizon=10000`):
  - trades increased slightly:
    - `5094 -> 5186`
  - spread availability stayed essentially unchanged:
    - `0.594 -> 0.589`
  - mean spread tightened:
    - `0.1089 -> 0.0851`
  - the new `rl_01` finished profitable:
    - `PnL +406.88`
  - scripted `trend_01` also improved modestly relative to the 4-agent scripted baseline
- Interpretation:
  - adding PPO on top of the improved scripted ecology did not destabilize the market
  - liquidity remained strong and price action looked healthier than earlier PPO phases

### Unseen-Seed 5-Agent Result

- On the unseen-seed comparison (`seed=20`, `horizon=10000`):
  - trades increased:
    - `5057 -> 5265`
  - spread availability improved slightly:
    - `0.581 -> 0.594`
  - mean spread tightened:
    - `0.1090 -> 0.0883`
  - the new `rl_01` again finished profitable:
    - `PnL +914.10`
- Interpretation:
  - PPO appears to generalize reasonably well even when moved into the richer 5-agent market
  - the market becomes more liquid and less prone to flat / dead patches in the live viewer

### Important Metric Caution For 5-Agent Comparisons

- Once PPO is added as a fifth participant, total-equity metrics are no longer directly comparable to the 4-agent baseline:
  - the right-hand side now has an extra agent with its own capital and inventory
- Therefore, the most meaningful 5-agent comparison metrics are:
  - trade count
  - spread availability
  - mean spread
  - volatility
  - per-agent outcomes
  - live-view market behavior

### Current Reading After The 5-Agent Bridge

- This is a strong sign that the roadmap toward MARL is viable.
- PPO is no longer only "winning by replacing trend"; it can remain competitive as an additional participant in a richer ecology.
- The 5-agent market also appears qualitatively healthier:
  - fewer flat-price episodes
  - more continuous liquidity
  - less brittle dependence on one participant role

### Next Control Experiment Under Discussion

- Before adding a second AI agent, the preferred next control is:
  - build a 5-agent scripted-only market by adding an extra scripted trend-like participant
  - compare that against the 5-agent market with `rl_01`
- Goal:
  - separate the effect of "one extra participant" from the effect of "this extra participant is PPO"
- This is not meant to imitate PPO behavior exactly.
- It is meant to answer the cleaner research question:
  - does PPO outperform a reasonable scripted fifth participant in the same richer market structure?

### 5-Agent Scripted Control Preset

- We then implemented a dedicated scripted control preset:
  - `baseline_trend_duo`
- Structure:
  - baseline scripted market
  - plus `trend_02` as an additional fifth scripted participant
- Important interpretation:
  - `trend_02` is not a frozen PPO agent
  - it is a scripted control participant cloned from the scripted `trend_01` template
  - the purpose is to isolate the effect of "one more participant" from the effect of "one more PPO participant"
- This preset is the immediate control for comparing:
  - 5-agent scripted-only market
  - 5-agent market with `rl_01`

### 5-Agent Scripted Control Results

- We ran the intended control experiment:
  - left side: `baseline_trend_duo`
    - 5 scripted agents
    - includes `trend_02` as the extra fifth scripted participant
  - right side: 5-agent market with PPO `rl_01`
- This comparison is much cleaner than the earlier 4-vs-5 setup because both sides now have:
  - the same participant count
  - the same broad market structure
  - only one substantive difference:
    - scripted `trend_02`
    - versus PPO-controlled `rl_01`

### Seen-Seed Control Result

- On the seen-seed comparison (`seed=7`, `horizon=10000`):
  - PPO side improved market activity and microstructure:
    - `trades 4975 -> 5186`
    - `spread_availability 0.517 -> 0.589`
    - `mean_spread 0.1136 -> 0.0851`
  - PPO side also strongly beat the scripted fifth participant at the participant level:
    - scripted `trend_02` PnL: `+169.81`
    - PPO `rl_01` PnL: `+406.88`
  - however, total system equity was lower on the PPO side:
    - `61390.50 -> 60185.84`

### Unseen-Seed Control Result

- On the unseen-seed comparison (`seed=20`, `horizon=10000`):
  - PPO side again improved market activity and microstructure:
    - `trades 4917 -> 5265`
    - `spread_availability 0.524 -> 0.594`
    - `mean_spread 0.1091 -> 0.0883`
  - PPO side again strongly beat the scripted fifth participant:
    - scripted `trend_02` PnL: `+215.51`
    - PPO `rl_01` PnL: `+914.10`
  - total system equity again finished lower on the PPO side:
    - `64152.83 -> 61655.75`

### Current Interpretation After The 5-Agent Control

- This experiment answered the key control question well:
  - PPO is not only benefiting from "being the fifth body" in the market
  - it is specifically stronger than a reasonable extra scripted participant
- The PPO appears to be:
  - participant-level competitive
  - liquidity-improving
  - but also somewhat extractive / adversarial for the rest of the ecology
- Important reading:
  - PPO improves trade count, spread availability, and spread tightness
  - but does not maximize total ecosystem equity in these runs
- This is a realistic and useful result rather than a failure:
  - we now have evidence that PPO is strategically strong
  - and we also see that stronger participants can make the market more active without making it better for every agent

### Current Conclusion After The 5-Agent Control

- The 5-agent bridge experiment succeeded.
- The PPO `rl_01` is now validated as:
  - stronger than the scripted trend replacement baseline
  - stronger than the scripted fifth-agent control
  - robust enough to remain competitive in a richer ecology
- This gives strong justification for the next roadmap step:
  - add a second AI-controlled participant
- Preferred next framing:
  - keep `rl_01` fixed first
  - train `rl_02` against the existing scripted ecology plus frozen `rl_01`
  - treat that as the next asymmetric bridge toward MARL

### RL_01 Versioning Decision

- Before starting the `rl_02` phase, we decided to preserve the current `rl_01` checkpoint trained in the 4-agent replacement market as a permanent benchmark opponent.
- Naming / interpretation:
  - `rl_01_v1`:
    - current PPO checkpoint trained in the 4-agent market
    - keep this frozen and do not overwrite it
  - `rl_01_v2`:
    - next PPO retrain in the true 5-agent market
    - this will be evaluated as a candidate stronger frozen opponent
- Important reasoning:
  - retraining `rl_01` in the 5-agent market may improve adaptation to the richer ecology
  - but it could also reintroduce unwanted liquidity pathologies
  - preserving `rl_01_v1` gives us a clean fallback and a second benchmark opponent for later `rl_02` experiments
- Planned usage:
  - train and evaluate `rl_01_v2` in the 5-agent market
  - if `rl_01_v2` looks healthy, freeze it as the main next opponent
  - later, `rl_02` can be trained and evaluated against:
    - frozen `rl_01_v1` (4-agent-trained opponent)
    - frozen `rl_01_v2` (5-agent-trained opponent)
- This turns the opponent choice itself into a useful experiment:
  - how much does the opponent's training ecology affect the next AI agent's learned behavior?

### RL_01_V2 Cancel-Churn Diagnosis

- Before deciding whether `rl_01_v1` or `rl_01_v2` should become the frozen opponent for the future `rl_02` phase, we investigated the strange live-viewer behavior where `rl_01_v2` appeared to spam cancel events.
- Important diagnosis:
  - the policy was not primarily choosing `cancel_oldest`
  - instead, it was repeatedly requesting `limit_sell`
  - the RL action mask was validating sell actions against total `agent_inventory`
  - but the simulator enforces reservations against `available_inventory`
  - this mismatch allowed repeated invalid sell attempts when inventory was already reserved by a resting order
  - the simulator then logged these failures as `CANCEL_ORDER` events with reason `reservation_rejected`
- This explains the live-viewer pattern:
  - RL diagnostics showed many `limit_*` actions and almost no explicit `cancel` actions
  - yet the recent order event feed showed repeated cancel rows

### RL_01_V2 Cancel-Churn Fix

- We fixed the mask/observation boundary without changing the RL feature-vector shape:
  - `MarketObservation` now carries `available_cash` and `available_inventory`
  - the simulator populates those fields from the portfolio
  - `mask_invalid_action(...)` now validates:
    - sells against `available_inventory`
    - buys against estimated required cash and `available_cash`
- We also improved the live viewer so cancel rows include the reason text when available, making `reservation_rejected` and other auto-cancel causes visible in the UI.

### Post-Fix Sanity Check

- We ran short sanity evals (`seed=7`, `horizon=400`) for both:
  - `rl_01_v1`
  - `rl_01_v2`
- After the mask fix:
  - `rl_01_v1` produced `0` RL cancel events
  - `rl_01_v2` produced `0` RL cancel events
- This strongly supports the diagnosis that the visible cancel spam was caused by the masking/reservation mismatch rather than an intentionally cancel-heavy learned policy.

### Current Reading After The Fix

- `rl_01_v1` still appears stronger than `rl_01_v2` on market-quality metrics from the larger 10k-step runs.
- However:
  - the live-viewer cancel churn was a real infrastructure bug
  - and it is now fixed
- Therefore, the next fair step is:
  - retrain `rl_01_v2` in the 5-agent market with the corrected mask
  - then re-evaluate before making a final `v1` vs `v2` decision.

### Why V1 Did Not Show The Same Cancel Spam

- Current interpretation:
  - the masking/reservation bug was structural and affected both `rl_01_v1` and `rl_01_v2`
  - it was not a bug unique to `v2`
- The most likely reason it was much more visible in `v2` is behavioral:
  - `v2` appeared more passive and repost-heavy
  - it more often kept a resting sell order on the book
  - then it repeatedly tried to submit another `limit_sell` while the same inventory was already reserved
  - this produced many `reservation_rejected` cancel events in the live viewer
- By contrast, `v1` appeared more aggressive / directional:
  - it spent less time in the “one resting sell, try to sell again” state
  - so the same infrastructure bug surfaced much less often
- Important framing:
  - this is still an inference from behavior and code-path analysis, not a claim that `v1` was bug-free
  - the bug existed for both policies, but `v2`’s policy style exposed it much more clearly

### RL_01 Freeze Decision For RL_02 Phase

- After comparing `rl_01_v1` and `rl_01_v2` qualitatively and quantitatively, the preferred current opponent is still `rl_01_v1`.
- Reasoning:
  - `rl_01_v1` remains the more attractive policy for market quality
  - it tends to generate more activity and a livelier ecology
  - `rl_01_v2` / `v2_maskfix` appears more passive, more limit-order-heavy, and more likely to settle into quiet inventory accumulation
- Current working decision:
  - freeze `rl_01_v1`
  - move forward with the next roadmap step: add `rl_02` as a second AI-controlled participant

### Frozen RL_01 + Learning RL_02 Infrastructure

- We implemented the next MARL bridge step in the codebase:
  - support for one learning RL slot plus one optional frozen PPO opponent slot
  - this works for:
    - training
    - evaluation
    - live viewer runtime configuration
- New capabilities:
  - add a frozen PPO agent from a checkpoint
  - optionally clone that frozen agent from a scripted template id
  - optionally override the frozen agent starting inventory
  - keep the learning RL agent as a separate runtime-controlled slot
- Important turn-order choice:
  - when both `rl_01` (frozen) and `rl_02` (learning) are added to the market, the config now adds `rl_01` first and `rl_02` second
  - this creates the natural participant order:
    - `maker_01`
    - `retail_01`
    - `informed_01`
    - `trend_01`
    - `rl_01`
    - `rl_02`
- This is now the intended 6-agent bridge environment for the next asymmetric MARL phase.

### First RL_02 Result Against Frozen RL_01_V1

- We trained the first asymmetric 6-agent setup:
  - scripted baseline ecology
  - frozen `rl_01_v1`
  - learning `rl_02`
- The first evaluation result exposed a major pathology rather than a clean MARL success.
- Main behavioral pattern seen in both metrics and the live viewer:
  - `rl_02` repeatedly leaned on `limit_buy`
  - it bought inventory away from the other participants, including frozen `rl_01`
  - once supply was exhausted, the market became sparse and eventually nearly flat
  - `rl_02` then continued to sit on inventory without meaningfully selling back out
- Quantitative signs of the pathology:
  - trade count collapsed dramatically relative to the scripted baseline
  - spread availability also collapsed
  - `rl_02` ended with very large long inventory
  - the rest of the ecology often finished close to flat / out of inventory
- Current interpretation:
  - this run is more useful as a reward-design diagnostic than as a verdict on the 6-agent bridge itself

### Reward-System Diagnosis After RL_02

- We investigated whether `rl_02` was exploiting unrealized reward in illiquid states.
- Important code-level finding:
  - RL reward uses both:
    - `realized_pnl_delta`
    - `equity_delta`
  - and in training both coefficients are currently `1.0`
- The critical marking rule:
  - `agent_equity` is marked to book midpoint when a midpoint exists
  - but if no midpoint exists, equity falls back to the latent fundamental
- Why this matters:
  - after `rl_02` buys out the available supply, the market can lose a meaningful midpoint
  - once that happens, reward can drift with the latent fundamental even though the position is not being marked through a liquid market
  - this likely rewards “corner inventory and wait” behavior too generously
- So the current reward problem appears to have two parts:
  - unrealized mark-to-market falls back to latent fundamental when market liquidity disappears
  - unrealized `equity_delta` is currently weighted strongly enough that large held positions can dominate the learning signal

### Current Recommendation Before Continuing MARL

- We do not currently trust the first `rl_02` run as a healthy asymmetric-MARL result.
- The most likely issue is not the frozen-opponent setup itself, but the reward design.
- Current next-step recommendation:
  - redesign the reward / mark-to-market logic before drawing conclusions from `rl_02`
- Proposed direction for the next reward-system pass:
  - stop using latent fundamental as the reward mark-to-market fallback when midpoint disappears
  - instead use a more execution-grounded mark, such as:
    - best bid / best ask depending on side
    - or last trade / previous valid market mark
  - reduce reward dependence on unrealized equity drift
  - increase pressure to realize / close positions rather than only accumulate them
- Practical conclusion:
  - keep `rl_01_v1` as the preferred frozen opponent for now
  - discuss and redesign reward shaping before retraining `rl_02`

### Reward-System Redesign Implementation

- We implemented the next reward-system pass before retraining `rl_02`.
- Main reward / mark-to-market changes:
  - `agent_equity` for RL reward no longer falls back to latent fundamental when midpoint disappears
  - instead, the simulator now uses a more execution-grounded mark:
    - long inventory -> best bid when available
    - short inventory -> best ask when available
    - flat inventory -> midpoint when available
    - if no executable quote exists -> last trade price
    - final fallback -> starting midpoint
- This executable mark is now used consistently across:
  - RL observations / `agent_equity`
  - simulator total-equity step records
  - final portfolio summaries in run results
  - live viewer per-agent unrealized PnL display
- We also changed reward shaping defaults and configuration:
  - introduced an explicit `reward_equity_delta_coefficient`
  - defaulted it to `0.0` for both training and evaluation workflows
  - kept `reward_realized_pnl_delta_coefficient = 1.0`
- This means the new default PPO reward is now much closer to:
  - realized PnL delta
  - minus inactivity / inventory penalties
  - with equity drift only included when explicitly turned on

### Motivation For The Redesign

- Goal:
  - stop rewarding “buy everything, let the book die, and drift with latent fundamental”
- Expected effect:
  - large stuck long positions should no longer receive unrealized reward from latent-value movement alone
  - the agent should have much stronger incentive to realize gains through actual trading
  - the next `rl_02` retrain should be a cleaner test of the asymmetric 6-agent setup

### Validation After Reward Redesign

- Focused checks passed after implementation:
  - reward / CLI metadata tests
  - simulator executable-mark test
  - RL boundary/env tests
  - Python compile pass
- This establishes the new baseline before retraining `rl_02` against frozen `rl_01_v1`.

### Comparison / Accounting Fix For Runtime RL Agents

- We found that evaluation / comparison rows for runtime-added RL agents were still disagreeing with the live viewer.
- Root causes:
  - runtime-added agents (`rl_01`, `rl_02`) were still being evaluated with scripted default starting inventory assumptions in the health-report layer
  - unrealized PnL in the comparison path was still using a single shared final mark price, while the simulator and live viewer now mark each portfolio with its own executable side-aware mark
- Fixes implemented:
  - health reporting now accepts runtime starting-inventory overrides for both the learning agent and any frozen runtime agent
  - portfolio summaries now carry:
    - `starting_cash`
    - `starting_inventory`
    - `mark_price`
  - per-agent unrealized PnL in the comparison path now uses the agent's own final portfolio mark instead of one global mark
- Validation after the fix:
  - focused tests pass, including a regression test for a 6-agent env with both:
    - frozen `rl_01`
    - learning `rl_02`
  - for runtime RL agents, `PnL` now lines up with `realized + unrealized`, which is the expected accounting identity
- Practical result:
  - regenerated `rl_02 rewardfix` comparisons now match live-view behavior much more closely
  - this gives us a trustworthy basis for interpreting the new 6-agent results and tuning the next reward pass
