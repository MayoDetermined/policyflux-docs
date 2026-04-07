# Scenarios

The `policyflux.scenarios` package contains reusable comparative workflows.

Modules:

- `comparative_systems`
- `country_comparison`
- `lobbying_sweep`
- `party_discipline_sweep`
- `veto_player_sweep`

## What scenarios solve

Scenario modules package repeatable experiment patterns so you can:

- vary one institutional or behavioral factor systematically,
- preserve comparability across runs,
- produce interpretable summary outputs for analysis.

## Built-in scenario runners

Each scenario module exposes a `run()` function that executes a complete
structured experiment and returns typed result objects.

### Comparative systems

Compare presidential, parliamentary, and semi-presidential systems side by side:

```python
from policyflux.scenarios import comparative_systems

results = comparative_systems.run(num_actors=100, iterations=300, seed=42)
for r in results:
    print(f"{r.system}: passage rate {r.passage_rate:.1%}, avg vote share {r.avg_vote_share:.1%}")
```

### Lobbying intensity sweep

Sweep lobbying intensity from 0.0 to 1.0 across evenly-spaced levels:

```python
from policyflux.scenarios import lobbying_sweep

points = lobbying_sweep.run(num_actors=100, n_steps=10, seed=42)
for p in points:
    print(f"Intensity {p.lobbying_intensity:.1f}: passage rate {p.passage_rate:.1%}")
```

### Party discipline sweep

Sweep party discipline strength (returns two series: pro-bill and anti-bill):

```python
from policyflux.scenarios import party_discipline_sweep

results = party_discipline_sweep.run(num_actors=100, n_steps=10, seed=42)
for p in results["pro"]:
    print(f"Discipline {p.discipline_strength:.1f}: passage rate {p.passage_rate:.1%}")
```

### Veto player sweep

Sweep executive approval rating across presidential and semi-presidential systems:

```python
from policyflux.scenarios import veto_player_sweep

results = veto_player_sweep.run(num_actors=100, n_steps=9, seed=42)
for p in results["presidential"]:
    print(f"Approval {p.approval:.1f}: passage rate {p.passage_rate:.1%}")
```

### Country parliament comparison

Compare all 10 country parliament presets on identical bills:

```python
from policyflux.scenarios import country_comparison

results = country_comparison.run(n_bills=30, policy_dim=2, seed=42)
for r in results:
    print(f"{r.parliament_name}: {r.passage_rate:.1%} ({r.n_passed}/{r.n_bills} bills)")
```

Subset of countries:

```python
results = country_comparison.run(
    presets=["uk", "us", "germany", "sweden"],
    n_bills=50,
    policy_dim=2,
)
```

## Recommended scenario workflow

1. Choose a baseline (preset + fixed model scale).
2. Define a sweep variable range.
3. Run deterministic validation.
4. Run multi-seed or Monte Carlo confirmation.
5. Export and compare summary metrics.

## Practical scenario recipes

Use the following recipes as ready-made experiment templates.

### Recipe 1: Sensitivity to public support

Goal: quantify how sensitive passage rate is to changes in `public_support`.

1. Keep institutional preset fixed.
2. Sweep `public_support` over a compact range.
3. Repeat each point for multiple seeds.
4. Compare mean and min/max passage rate per support level.

```python
from statistics import mean

from policyflux import build_engine, create_parliamentary_config

supports = [0.40, 0.50, 0.60, 0.70]

for support in supports:
    rates = []
    for seed in range(10, 20):
        cfg = create_parliamentary_config(
            num_actors=120,
            policy_dim=2,
            iterations=220,
            seed=seed,
        ).with_flat(public_support=support)
        eng = build_engine(cfg)
        eng.run()
        rates.append(eng.pass_rate)

    print(
        f"support={support:.2f} -> "
        f"min={min(rates):.1%}, mean={mean(rates):.1%}, max={max(rates):.1%}"
    )
```

### Recipe 2: Institutional A/B comparison

Goal: compare two systems under identical model scale and randomness policy.

1. Pick two presets (for example presidential vs parliamentary).
2. Fix `num_actors`, `policy_dim`, `iterations`, and seed range.
3. Run both systems for each seed.
4. Compare average deltas in passage rate.

```python
from statistics import mean

from policyflux import build_engine, create_parliamentary_config, create_presidential_config

delta = []
for seed in range(1, 21):
    a = create_presidential_config(num_actors=100, policy_dim=2, iterations=250, seed=seed)
    b = create_parliamentary_config(num_actors=100, policy_dim=2, iterations=250, seed=seed)

    ea = build_engine(a)
    eb = build_engine(b)
    ea.run()
    eb.run()
    delta.append(ea.pass_rate - eb.pass_rate)

print(f"avg_delta(presidential - parliamentary) = {mean(delta):+.2%}")
print(f"min_delta={min(delta):+.2%}, max_delta={max(delta):+.2%}")
```

### Recipe 3: Stability versus policy dimensionality

Goal: test whether conclusions remain stable as `policy_dim` increases.

1. Keep all parameters fixed except `policy_dim`.
2. Run a small seed sweep for each dimension.
3. Compare central tendency and spread.

```python
from statistics import mean

from policyflux import build_engine, create_presidential_config

for dim in [1, 2, 3, 4]:
    rates = []
    for seed in range(30, 40):
        cfg = create_presidential_config(
            num_actors=120,
            policy_dim=dim,
            iterations=240,
            seed=seed,
        )
        eng = build_engine(cfg)
        eng.run()
        rates.append(eng.pass_rate)

    print(f"policy_dim={dim}: mean={mean(rates):.1%}, span={(max(rates)-min(rates)):.1%}")
```

### Recipe 4: Lobbying effect size table

Goal: produce a compact table of effect sizes for reporting.

1. Define baseline lobbying intensity and intervention levels.
2. Keep seed and model scale aligned.
3. Report delta from baseline for each level.

```python
from policyflux import build_engine, create_parliamentary_config

levels = [0.0, 0.2, 0.4, 0.6, 0.8]
baseline = None

print("level,pass_rate,delta_vs_baseline")
for level in levels:
    cfg = create_parliamentary_config(
        num_actors=100,
        policy_dim=2,
        iterations=220,
        seed=42,
    ).with_flat(include_lobbying=True, lobbyist_strength=level)

    eng = build_engine(cfg)
    eng.run()

    if baseline is None:
        baseline = eng.pass_rate
    delta = eng.pass_rate - baseline
    print(f"{level:.1f},{eng.pass_rate:.4f},{delta:+.4f}")
```

These recipes are intended as starting points. For larger studies, run more seeds,
record metadata, and keep one independent variable per experiment family.

## Manual sweep template

```python
from policyflux import build_engine, create_parliamentary_config

supports = [0.45, 0.55, 0.65, 0.75]

for support in supports:
    config = create_parliamentary_config(
        num_actors=100,
        policy_dim=2,
        iterations=200,
        seed=42,
    ).with_flat(public_support=support)

    engine = build_engine(config)
    engine.run()
    print(f"public_support={support:.2f} -> pass_rate={engine.pass_rate:.1%}")
```

## Multi-seed scenario robustness

```python
from policyflux import build_engine, create_presidential_config

def evaluate(public_support: float) -> float:
    rates = []
    for seed in range(1, 16):
        config = create_presidential_config(
            num_actors=120,
            policy_dim=2,
            iterations=220,
            seed=seed,
        ).with_flat(public_support=public_support)
        engine = build_engine(config)
        engine.run()
        rates.append(engine.pass_rate)
    return sum(rates) / len(rates)

print(evaluate(0.55), evaluate(0.65))
```

## Interpreting scenario outputs

Prioritize:

- direction of change (increase/decrease),
- effect magnitude,
- stability across seeds,
- consistency across institutional presets.

For parameter design details, see [Configuration](configuration.md) and [Presets](presets.md).
