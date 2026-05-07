# Agent Catalog

This file is the canonical registry for trained RL checkpoints in `marl-trading`.

Use it to understand:

- what each checkpoint is
- how it was trained
- which environment it belongs to
- whether it is promoted, experimental, or archived
- what its known strengths or pathologies are

This file covers trained RL checkpoints only. It does not document the scripted agents.

## Promoted Agents

### `rl_01_v1`

- Checkpoint: `checkpoints/maskableppo_baseline_trend_01_multiseed8_sellpressurev3_100k.zip`
- Algorithm: `maskable_ppo`
- Training setup:
  - 4-scripted market
  - replaces `trend_01`
  - multiseed training (`1..8`)
  - improved sell-pressure scripted ecology
- Status: promoted benchmark / preferred frozen opponent
- Behavior:
  - aggressive and active
  - characteristic market-buy plus limit-sell style
  - strong generalization even when moved into richer 5-agent and 6-agent environments
- Key note:
  - still the strongest learned trading agent we have so far
  - do not claim it consistently beat `informed_01` in absolute end results

### `rl_02_softinv_b`

- Checkpoint: `checkpoints/maskableppo_baseline_rl_02_vs_frozen_rl_01_v1_softinv_b_6agent_100k.zip`
- Algorithm: `maskable_ppo`
- Training setup:
  - 6-agent market
  - frozen `rl_01_v1`
  - 4 scripted baseline agents
  - learning agent added as `rl_02`
- Reward shaping:
  - `reward_equity_delta_coefficient = 0.1`
  - `reward_inventory_penalty = 0.002`
  - `reward_inventory_risk_penalty = 0.00005`
  - `reward_inactivity_penalty = 0.01`
- Status: promoted `rl_02` candidate
- Behavior:
  - meaningfully more active than prior `rl_02` versions
  - closer to `rl_01` style
  - can carry inventory and compete rather than instantly flattening
- Key note:
  - best second-agent candidate so far
  - still not clearly stronger than `rl_01_v1`
  - looks stronger as a competitor than as a market-quality improver

## Experimental Agents

### `rl_01_v2_5agent`

- Checkpoint: `checkpoints/maskableppo_baseline_rl_01_v2_5agent_100k.zip`
- Algorithm: `maskable_ppo`
- Training environment:
  - 5-agent market
  - scripted baseline plus added `rl_01`
- Reward shape:
  - realized-PnL-centered setup
  - inactivity penalty `0.01`
  - no explicit inventory penalties
- Observed behavior:
  - became too passive
  - overused passive quoting
  - did not preserve the stronger, more active `rl_01_v1` style
- Why not promoted:
  - inferior to `rl_01_v1` as a benchmark / frozen opponent

### `rl_01_v2_maskfix`

- Checkpoint: `checkpoints/maskableppo_baseline_rl_01_v2_maskfix_5agent_100k.zip`
- Algorithm: `maskable_ppo`
- Training environment:
  - 5-agent market
  - retrained after the inventory-mask / reservation fix
- Reward shape:
  - same broad shaping family as `rl_01_v2_5agent`
- Observed behavior:
  - infrastructure bug was removed
  - policy style still diverged from preferred `v1`
  - remained too passive relative to the stronger `v1` benchmark
- Why not promoted:
  - useful for validation, but not the preferred competitive agent

### `rl_02_base_6agent`

- Checkpoint: `checkpoints/maskableppo_baseline_rl_02_vs_frozen_rl_01_v1_6agent_100k.zip`
- Algorithm: `maskable_ppo`
- Training environment:
  - first asymmetric 6-agent setup
  - frozen `rl_01_v1` plus 4 scripted agents
- Reward shape:
  - pre-reward-redesign
  - no added inventory penalty pressure
- Observed behavior:
  - exploited the old reward structure
  - bought out supply, starved the market, and sat on inventory
  - contributed to flat / stuck late-market regimes
- Why not promoted:
  - this run mainly diagnosed a reward-pathology problem

### `rl_02_rewardfix`

- Checkpoint: `checkpoints/maskableppo_baseline_rl_02_vs_frozen_rl_01_v1_rewardfix_6agent_100k.zip`
- Algorithm: `maskable_ppo`
- Training environment:
  - 6-agent market against frozen `rl_01_v1`
- Reward shape:
  - executable mark-to-market fallback
  - `reward_equity_delta_coefficient = 0.0`
  - `reward_inventory_penalty = 0.01`
  - `reward_inventory_risk_penalty = 0.0005`
  - `reward_inactivity_penalty = 0.01`
- Observed behavior:
  - removed the inventory-hoarding exploit
  - but became too timid / too inventory-averse
  - behaved like a low-risk scalper rather than a real competitor
- Why not promoted:
  - healthy behaviorally, but not competitive enough

### `rl_02_softinv_a`

- Checkpoint: `checkpoints/maskableppo_baseline_rl_02_vs_frozen_rl_01_v1_softinv_a_6agent_100k.zip`
- Algorithm: `maskable_ppo`
- Training environment:
  - 6-agent market against frozen `rl_01_v1`
- Reward shape:
  - `reward_equity_delta_coefficient = 0.05`
  - `reward_inventory_penalty = 0.003`
  - `reward_inventory_risk_penalty = 0.0001`
  - `reward_inactivity_penalty = 0.01`
- Observed behavior:
  - softer than `rewardfix`, but still too passive
  - remained too weak to count as a meaningful second AI competitor
- Why not promoted:
  - Option B dominates it as the current `rl_02` candidate

## Archive / Early Baselines

These checkpoints are preserved for historical context and baseline comparison. They are not current promoted candidates.

| Checkpoint | Algorithm | Approximate purpose | Current status |
| --- | --- | --- | --- |
| `ppo_baseline_trend_01.zip` | `ppo` | earliest short-horizon baseline replacing `trend_01` | archived baseline |
| `ppo_baseline_trend_01_long.zip` | `ppo` | longer-horizon early PPO baseline | archived baseline |
| `ppo_baseline_trend_01_phaseA_50k.zip` | `ppo` | early PPO Phase A action-space experiment | archived baseline |
| `ppo_baseline_trend_01_realized_inactive1e-2_50k.zip` | `ppo` | realized-PnL with inactivity penalty | archived baseline |
| `ppo_baseline_trend_01_masked_realized_inactive1e-2_50k.zip` | `ppo` | naming suggests masked/realized variant; used as an early reward-shaping baseline | archived baseline |
| `ppo_baseline_trend_01_zeroinv_50k.zip` | `ppo` | zero starting inventory experiment | archived baseline |
| `ppo_baseline_trend_01_zeroinv_risk5e-4_50k.zip` | `ppo` | zero-inventory baseline with quadratic inventory risk term | archived baseline |
| `maskableppo_baseline_trend_01_phaseA_50k.zip` | `maskable_ppo` | early maskable Phase A baseline | archived baseline |
| `maskableppo_baseline_trend_01_liquidityaware_50k.zip` | `maskable_ppo` | liquidity-aware / improved microstructure baseline experiment | archived baseline |
| `maskableppo_baseline_trend_01_makerv2_50k.zip` | `maskable_ppo` | baseline trained after a stronger maker / ecology adjustment | archived baseline |
| `maskableppo_baseline_trend_01_multiseed8_100k.zip` | `maskable_ppo` | major multiseed baseline before sell-pressure improvements | archived baseline |
| `maskableppo_baseline_trend_01_multiseed8_sellpressurev3_100k.zip` | `maskable_ppo` | major multiseed improved-sell-pressure baseline; now used as `rl_01_v1` | promoted benchmark |

## Naming Conventions

Checkpoint names should stay interpretable. Current useful patterns are:

- market structure:
  - `5agent`
  - `6agent`
- opponent reference:
  - `vs_frozen_rl_01_v1`
- reward / system markers:
  - `maskfix`
  - `rewardfix`
  - `softinv_a`
  - `softinv_b`
- training-scale suffix:
  - `100k`
  - `50k`

Recommended naming style for future agents:

- include the market structure
- include the opponent lineage when the setup is asymmetric
- include any reward-system variant marker
- keep the timestep suffix at the end

Example:

- `maskableppo_baseline_rl_03_vs_frozen_rl_01_v1_rl_02_softinv_b_7agent_100k.zip`
