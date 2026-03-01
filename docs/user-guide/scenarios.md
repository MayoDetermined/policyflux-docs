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
