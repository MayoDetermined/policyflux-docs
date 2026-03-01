# Engines

Available engine backends:

- `DeterministicEngine`
- `SequentialMonteCarlo`
- `ParallelMonteCarlo`

Standard usage:

```python
from policyflux import build_engine

engine = build_engine(config)
engine.run()
```

## Engine selection guide

| Backend | Best for | Trade-off |
| --- | --- | --- |
| `DeterministicEngine` | baseline validation and debugging | no distributional uncertainty |
| `SequentialMonteCarlo` | medium sweeps with traceable execution | slower wall-clock on large batches |
| `ParallelMonteCarlo` | larger experiments on multi-core machines | higher resource usage |

## Practical workflow

1. Start with deterministic baseline.
2. Validate configuration and metric behavior.
3. Move to Monte Carlo for uncertainty estimates.
4. Keep configuration versioned and seeds documented.

## Deterministic baseline example

```python
from policyflux import IntegrationConfig, LayerConfig, build_engine

config = IntegrationConfig(
	num_actors=80,
	policy_dim=2,
	iterations=150,
	seed=123,
	layer_config=LayerConfig(
		include_ideal_point=True,
		include_public_opinion=True,
		public_support=0.58,
	),
)

engine = build_engine(config)
engine.run()
print(engine.pass_rate)
```

## Seed-sweep uncertainty sketch

```python
from policyflux import build_engine, create_parliamentary_config

rates = []
for seed in range(50, 75):
	config = create_parliamentary_config(
		num_actors=100,
		policy_dim=2,
		iterations=250,
		seed=seed,
	)
	engine = build_engine(config)
	engine.run()
	rates.append(engine.pass_rate)

print(f"mean={sum(rates)/len(rates):.1%}")
print(f"min={min(rates):.1%}, max={max(rates):.1%}")
```

Even when using deterministic backend, seed sweeps provide a first uncertainty proxy.

## Interpreting engine outputs

Focus on:

- central tendency (average pass rate),
- spread (min/max or quantiles),
- stability across small parameter perturbations.

For robust comparisons, keep model scale and aggregation strategy unchanged across runs.
