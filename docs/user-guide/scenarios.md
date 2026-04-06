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
