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
- plotting helpers
- replay CLI
- compact health-report CLI for preset comparison
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
- recent agent actions
- agent portfolio cards with PnL fields
- right-anchored chart window so sparse history behaves more like a market terminal
- clearer on-chart trade markers
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

### Not Built Yet

- stronger ecology tuning so the market remains healthy longer
- more robust simulator tests
- whale agent phase
- RL integration

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

Limitations:

- `pytest` is not installed in the current sandbox, so the test suite was not run through the test runner
- the replay CLI's richer figure path still depends on `matplotlib`, which is not installed in the current sandbox
- the live market no longer dies immediately from the earlier settlement bug, but the ecology still needs tuning for richer and more persistent behavior

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
- exact private-signal noise model
- exact episode horizon definition
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

The next coding milestone should be:

> tune and stress-test the scripted market ecology so the synthetic market stays active, interpretable, and experimentally useful over longer runs.

That should happen before adding any RL agent.

Implementation rule for that next step:

- do not start another major UI redesign pass unless a market-analysis need forces it
- focus on market behavior, not cosmetics
- keep using the current `exchange/`, `analysis/`, and `live/` contracts rather than introducing new parallel schemas

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
