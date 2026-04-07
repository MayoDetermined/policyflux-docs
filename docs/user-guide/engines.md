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

## Practical engine recipes

### Recipe 1: Deterministic sanity gate

Goal: confirm config correctness before expensive sweeps.

1. Run one deterministic baseline.
2. Re-run the same config with the same seed.
3. Confirm that summary metrics are unchanged.

```python
from policyflux import build_engine, create_presidential_config

cfg = create_presidential_config(num_actors=100, policy_dim=2, iterations=200, seed=42)

eng1 = build_engine(cfg)
eng2 = build_engine(cfg)
eng1.run()
eng2.run()

print(f"run1={eng1.pass_rate:.1%}, run2={eng2.pass_rate:.1%}")
```

### Recipe 2: Sequential uncertainty profile

Goal: produce a quick uncertainty envelope for one configuration.

```python
from statistics import mean

from policyflux import build_engine, create_parliamentary_config

rates = []
for seed in range(1, 31):
	cfg = create_parliamentary_config(num_actors=120, policy_dim=2, iterations=240, seed=seed)
	eng = build_engine(cfg)
	eng.run()
	rates.append(eng.pass_rate)

print(f"mean={mean(rates):.1%}")
print(f"pessimistic={min(rates):.1%}, optimistic={max(rates):.1%}")
```

### Recipe 3: Compare two configs under identical seed policy

Goal: estimate robust delta between baseline and intervention.

```python
from statistics import mean

from policyflux import build_engine, create_presidential_config

baseline_rates = []
intervention_rates = []

for seed in range(10, 40):
	base_cfg = create_presidential_config(
		num_actors=100,
		policy_dim=2,
		iterations=220,
		seed=seed,
	)
	int_cfg = base_cfg.with_flat(public_support=0.70)

	e_base = build_engine(base_cfg)
	e_int = build_engine(int_cfg)
	e_base.run()
	e_int.run()

	baseline_rates.append(e_base.pass_rate)
	intervention_rates.append(e_int.pass_rate)

delta = mean(intervention_rates) - mean(baseline_rates)
print(f"avg_delta={delta:+.2%}")
```

## Engine quality gates

Before scaling experiments, pass these checks:

1. deterministic rerun consistency with fixed seed,
2. expected metric ranges (no impossible values),
3. stable directional effect for at least one known intervention,
4. logged metadata for every batch.

If one gate fails, debug configuration before increasing run volume.

## Performance tuning notes

- Use smaller `iterations` and `num_actors` for hypothesis prototyping.
- Use sequential repeated runs when you need easy traceability.
- Move to higher-throughput execution only after logic is validated.
- Cache intermediate aggregates when building dashboards or notebooks.

A fast wrong run is still wrong; optimize only after correctness checks.

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

## Reporting template for engine studies

```text
study_id: engine_uncertainty_v1
engine_mode: deterministic+seed_sweep
seed_range: 1..30
num_actors: 120
policy_dim: 2
iterations: 240
aggregation_strategy: average
independent_variable: <your_variable>
```

Store this block together with output tables/charts for reproducibility.
