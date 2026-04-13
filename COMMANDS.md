# Commands For Meeting

This file collects the most useful commands to run the project.

All commands below assume you are inside:

`/marl-trading`

## 1. Activate The Environment

Use this first, so Python can see the installed dependencies.

```bash
source .venv/bin/activate
```

## 2. Start The Live Viewer

This launches the local web dashboard so you can show the synthetic market live.

```bash
PYTHONPATH=src python3 scripts/serve_market_view.py --paused --port 8765
```

Then open:

[http://127.0.0.1:8765](http://127.0.0.1:8765)

What it does:

- starts the live market viewer
- starts paused
- lets you use play / pause / step controls in the browser

## 3. Start The Live Viewer With A Specific Preset

Use this if you want to show a specific market regime.

### Baseline

```bash
PYTHONPATH=src python3 scripts/serve_market_view.py --preset baseline --paused --port 8765
```

### Fragile Liquidity

```bash
PYTHONPATH=src python3 scripts/serve_market_view.py --preset fragile_liquidity --paused --port 8765
```

### High Information Asymmetry

```bash
PYTHONPATH=src python3 scripts/serve_market_view.py --preset high_information_asymmetry --paused --port 8765
```

### High News

```bash
PYTHONPATH=src python3 scripts/serve_market_view.py --preset high_news --paused --port 8765
```

What it does:

- launches the same viewer
- but with a specific market regime already loaded

## 4. Run A Compact Market Health Report

This prints a short numerical summary in the terminal.

### Baseline

```bash
PYTHONPATH=src python3 scripts/run_market_health.py --preset baseline --seed 7 --horizon 10000
```

### High News

```bash
PYTHONPATH=src python3 scripts/run_market_health.py --preset high_news --seed 7 --horizon 10000
```

What it does:

- runs the market without opening the browser
- prints key metrics such as:
  - trades
  - news count
  - spread
  - final midpoint
  - final fundamental
  - final total equity

## 5. Run Market Health With Per-Agent Breakdown

This is useful if you want to show what happened to each participant.

### Baseline

```bash
PYTHONPATH=src python3 scripts/run_market_health.py --preset baseline --seed 7 --horizon 10000 --portfolio-breakdown
```

### High News

```bash
PYTHONPATH=src python3 scripts/run_market_health.py --preset high_news --seed 7 --horizon 10000 --portfolio-breakdown
```

What it does:

- prints the normal market summary
- also prints per-agent end-state metrics such as:
  - cash
  - inventory
  - equity
  - PnL
  - open orders

## 6. Compare Two Runs Directly

This is one of the most important commands because it shows how two market regimes differ.

### Baseline vs High News

```bash
PYTHONPATH=src python3 scripts/compare_market_runs.py 'preset=baseline seed=7 horizon=10000' 'preset=high_news seed=7 horizon=10000'
```

### Baseline vs Fragile Liquidity

```bash
PYTHONPATH=src python3 scripts/compare_market_runs.py 'preset=baseline seed=7 horizon=10000' 'preset=fragile_liquidity seed=7 horizon=10000'
```

### Baseline vs High Information Asymmetry

```bash
PYTHONPATH=src python3 scripts/compare_market_runs.py 'preset=baseline seed=7 horizon=10000' 'preset=high_information_asymmetry seed=7 horizon=10000'
```

What it does:

- runs two market experiments
- compares them side by side
- shows:
  - trades
  - spreads
  - midpoint
  - final fundamental
  - total equity
  - per-agent differences

## 7. Save A Health Report To A File

Useful if you want to keep the output.

```bash
PYTHONPATH=src python3 scripts/run_market_health.py --preset baseline --seed 7 --horizon 10000 --portfolio-breakdown --output documents/baseline_health_report.txt
```

What it does:

- runs the report
- saves the text output into a file

## 8. Save A JSON Health Report

Useful if later you want machine-readable results.

```bash
PYTHONPATH=src python3 scripts/run_market_health.py --preset baseline --seed 7 --horizon 10000 --portfolio-breakdown --json --output documents/baseline_health_report.json
```

What it does:

- saves the report as JSON instead of plain text
- useful for future automated comparisons

## 9. Save A Comparison Report

```bash
PYTHONPATH=src python3 scripts/compare_market_runs.py 'preset=baseline seed=7 horizon=10000' 'preset=high_news seed=7 horizon=10000' --output documents/baseline_vs_high_news.txt
```

What it does:

- compares two runs
- saves the comparison into a text file

## 10. Quick Smoke Test Of The RL Environment Boundary

This does not train RL yet.
It just proves that the RL environment wrapper exists and can step.

```bash
PYTHONPATH=src python3 - <<'PY'
from marl_trading.configs.defaults import default_simulation_config
from marl_trading.rl import SingleAgentEnvConfig, SingleAgentMarketEnv, RLAction, RLActionType

env = SingleAgentMarketEnv(
    config=default_simulation_config(),
    env_config=SingleAgentEnvConfig(learning_agent_id="maker_01"),
    horizon=32,
)

obs = env.reset(seed=7, horizon=32)
obs2, reward, done, info = env.step(RLAction(RLActionType.HOLD))

print("reset_len:", len(obs))
print("step_len:", len(obs2))
print("reward:", reward)
print("done:", done)
print("action:", info["applied_action"])
print("step_index:", info["step_index"])
PY
```

What it does:

- creates the first RL-compatible environment
- resets it
- performs one action
- shows that the RL boundary is already implemented

## 11. Stop The Live Viewer

If the viewer is running in the terminal, stop it with:

```bash
Ctrl+C
```

## Suggested Minimal Meeting Flow

If you want a simple order for the meeting:

1. activate the environment
2. start the live viewer
3. show one preset live
4. run one market health report
5. run one comparison command
6. explain that RL boundary exists, but training has not started yet

The three most important commands are probably:

```bash
PYTHONPATH=src python3 scripts/serve_market_view.py --preset baseline --paused --port 8765
```

```bash
PYTHONPATH=src python3 scripts/run_market_health.py --preset baseline --seed 7 --horizon 10000 --portfolio-breakdown
```

```bash
PYTHONPATH=src python3 scripts/compare_market_runs.py 'preset=baseline seed=7 horizon=10000' 'preset=high_news seed=7 horizon=10000'
```

