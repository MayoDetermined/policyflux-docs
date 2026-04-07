# Presets

System preset factories:

- `create_presidential_config(...)`
- `create_parliamentary_config(...)`
- `create_semi_presidential_config(...)`

Use presets for fast comparative experiments across institutional systems.

## Why presets are useful

Presets provide:

- faster onboarding,
- consistent institutional assumptions,
- fewer accidental inconsistencies during comparative studies.

They are ideal for baseline scenario design before moving to custom configuration.

## Standard comparative pattern

```python
from policyflux import (
	build_engine,
	create_presidential_config,
	create_parliamentary_config,
	create_semi_presidential_config,
)

common = dict(num_actors=120, policy_dim=2, iterations=250, seed=42)

configs = {
	"presidential": create_presidential_config(**common),
	"parliamentary": create_parliamentary_config(**common),
	"semi_presidential": create_semi_presidential_config(**common),
}

for name, config in configs.items():
	engine = build_engine(config)
	engine.run()
	print(f"{name}: {engine.pass_rate:.1%}")
```

## Preset + targeted adjustment

After creating a preset config, apply small focused changes for hypothesis testing.

```python
from policyflux import create_presidential_config

config = create_presidential_config(num_actors=100, policy_dim=2, iterations=200, seed=10)
config = config.with_flat(public_support=0.64)
```

Keep changes narrow to preserve interpretability.

## Preset experiment checklist

1. Keep `num_actors`, `policy_dim`, and `iterations` equal across systems.
2. Use identical seed for direct baseline comparison.
3. Change one additional variable at a time.
4. Record pass-rate deltas and variance across seed sweeps.

## Practical preset recipes

### Recipe 1: Three-system baseline table

Goal: produce one compact table comparing presidential, parliamentary,
and semi-presidential systems on identical assumptions.

```python
from policyflux import (
	build_engine,
	create_parliamentary_config,
	create_presidential_config,
	create_semi_presidential_config,
)

common = dict(num_actors=120, policy_dim=2, iterations=250, seed=42)

configs = [
	("presidential", create_presidential_config(**common)),
	("parliamentary", create_parliamentary_config(**common)),
	("semi_presidential", create_semi_presidential_config(**common)),
]

print("system,pass_rate,accepted,rejected")
for name, cfg in configs:
	eng = build_engine(cfg)
	eng.run()
	print(f"{name},{eng.pass_rate:.4f},{eng.accepted_bills},{eng.rejected_bills}")
```

### Recipe 2: Policy-dimension stress test across presets

Goal: verify whether institutional ranking is stable when `policy_dim` grows.

```python
from policyflux import (
	build_engine,
	create_parliamentary_config,
	create_presidential_config,
)

for dim in [1, 2, 3, 4]:
	a = create_presidential_config(num_actors=100, policy_dim=dim, iterations=220, seed=99)
	b = create_parliamentary_config(num_actors=100, policy_dim=dim, iterations=220, seed=99)

	ea = build_engine(a)
	eb = build_engine(b)
	ea.run()
	eb.run()

	print(
		f"policy_dim={dim} | "
		f"pres={ea.pass_rate:.1%}, parl={eb.pass_rate:.1%}, delta={ea.pass_rate-eb.pass_rate:+.1%}"
	)
```

### Recipe 3: One-factor intervention on a preset

Goal: estimate effect size of one intervention while preserving institutional context.

```python
from policyflux import build_engine, create_semi_presidential_config

baseline_cfg = create_semi_presidential_config(
	num_actors=110,
	policy_dim=2,
	iterations=240,
	seed=123,
)

intervention_cfg = baseline_cfg.with_flat(public_support=0.68)

base = build_engine(baseline_cfg)
mod = build_engine(intervention_cfg)
base.run()
mod.run()

print(f"baseline={base.pass_rate:.1%}, intervention={mod.pass_rate:.1%}, delta={mod.pass_rate-base.pass_rate:+.2%}")
```

For defensible conclusions, repeat each preset recipe across a seed range.


## Country-specific parliament presets

Ten ready-made bicameral (or unicameral) parliament models are available for structural comparison.
Each preset returns a fully configured `MultiChamberParliamentModel`:

```python
from policyflux.integration.presets.parliament_presets import (
    create_uk_parliament,
    create_us_congress,
    list_presets,
)
from policyflux.toolbox import SequentialBill

# List all available preset names
print(list_presets())

uk = create_uk_parliament()    # Commons (650) + Lords (800), suspensive veto
us = create_us_congress()      # House (435) + Senate (100), full veto

bill = SequentialBill()
bill.make_random_position(dim=2)

result = uk.cast_votes(bill)
print(f"Passed: {result.passed}, Rounds: {result.rounds}")
```

Available country presets:

| Key | Parliament | Upper-house power |
| --- | --- | --- |
| `uk` | UK Parliament (Commons + Lords) | Suspensive veto |
| `us` | US Congress (House + Senate) | Full veto |
| `germany` | Bundestag / Bundesrat | Full veto (consent laws) |
| `france` | Parlement français | Suspensive veto |
| `italy` | Parlamento italiano | Full veto (perfect bicameralism) |
| `poland` | Sejm + Senat | Override by lower chamber |
| `sweden` | Riksdag | Unicameral |
| `spain` | Cortes Generales | Suspensive veto |
| `australia` | Parliament of Australia | Full veto |
| `canada` | Parliament of Canada | Suspensive veto |

These presets use realistic membership sizes and constitutional veto structures.
Use `chamber_size` override when you need faster exploratory runs:

```python
from policyflux.scenarios import country_comparison

results = country_comparison.run(chamber_size=50, n_bills=20, seed=42)
for r in results:
    print(f"{r.parliament_name}: {r.passage_rate:.1%}")
```

## Reporting template for preset studies

Use a minimal metadata block with every chart/table you produce:

```text
study_id: presets_baseline_v1
systems: [presidential, parliamentary, semi_presidential]
num_actors: 120
policy_dim: 2
iterations: 250
seed_policy: fixed(42)
aggregation_strategy: average
independent_variable: system_type
```

This makes cross-run comparisons audit-friendly and easier to reproduce later.

For advanced sweeps, continue with [Scenarios](scenarios.md).
