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

For advanced sweeps, continue with [Scenarios](scenarios.md).
