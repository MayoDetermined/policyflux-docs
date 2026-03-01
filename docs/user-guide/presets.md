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

For advanced sweeps, continue with [Scenarios](scenarios.md).
