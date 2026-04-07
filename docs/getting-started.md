# Getting Started

This guide takes you from installation to a validated first simulation in under 10 minutes.

<div class="grid cards" markdown>

-   :material-lightning-bolt: **Fast onboarding**

    ---

    Install, run a baseline simulation, and confirm results with a minimal script.

-   :material-flask-outline: **Research-ready defaults**

    ---

    Start from reproducible parameters (`seed`, fixed dimensions, stable layer toggles).

-   :material-rocket-launch-outline: **Next-step paths**

    ---

    Move directly to API, configuration, and architecture depending on your role.

</div>

## Prerequisites

- Python 3.10+
- pip

!!! tip "Use a virtual environment"
    For clean dependency management, create and activate a dedicated venv before installation.

## Installation

From PyPI:

```bash
pip install policyflux
```

From source:

```bash
git clone https://github.com/MayoDetermined/policyflux.git
cd policyflux
pip install -e .
```

Optional extras:

```bash
# Neural layers
pip install -e ".[torch]"

# Text encoders
pip install -e ".[text-encoders]"

# Notebook/examples support
pip install -e ".[examples]"

# Developer tooling
pip install -e ".[dev]"
```

## Verify installation

```bash
python -c "import policyflux; print(policyflux.__version__)"
```

If the command prints a version number, the package is available in your active Python environment.

## First 15 minutes plan

Use this sequence to reach a trustworthy first baseline quickly:

1. Install package and verify import.
2. Run one deterministic baseline.
3. Repeat with the same seed (consistency check).
4. Change one parameter (for example `public_support`).
5. Compare pass-rate delta.

If steps 2 and 3 differ significantly, pause and validate environment consistency before continuing.

## Minimal simulation

```python
from policyflux import build_engine, IntegrationConfig, LayerConfig

config = IntegrationConfig(
    num_actors=50,
    policy_dim=2,
    iterations=100,
    seed=12345,
    layer_config=LayerConfig(
        include_ideal_point=True,
        include_public_opinion=True,
        include_party_discipline=True,
        public_support=0.60,
        party_discipline_strength=0.5,
    ),
)

engine = build_engine(config)
engine.run()

print(f"Pass rate: {engine.pass_rate:.1%}")
print(f"Accepted: {engine.accepted_bills}, Rejected: {engine.rejected_bills}")
```

## Read the first output correctly

In your first run, focus on directional interpretation, not on a specific percentage target:

- `pass_rate`: overall proportion of accepted bills,
- `accepted_bills` and `rejected_bills`: absolute outcome counts,
- repeatability: rerun with the same `seed` to confirm stable behavior.

If results change with an identical script, verify that you are using the same environment and package version.

## Quick comparative run (preset-based)

```python
from policyflux import (
    build_engine,
    create_parliamentary_config,
    create_presidential_config,
)

presidential = create_presidential_config(num_actors=100, policy_dim=2, iterations=200, seed=42)
parliamentary = create_parliamentary_config(num_actors=100, policy_dim=2, iterations=200, seed=42)

eng_a = build_engine(presidential)
eng_b = build_engine(parliamentary)

eng_a.run()
eng_b.run()

print(f"Presidential pass rate: {eng_a.pass_rate:.1%}")
print(f"Parliamentary pass rate: {eng_b.pass_rate:.1%}")
```

This gives you a fast institutional comparison while keeping actor count, policy dimensions, iteration count, and seed constant.

## First reproducibility sweep

Use a small seed sweep to estimate directional robustness:

```python
from policyflux import build_engine, create_presidential_config

rates = []
for seed in range(10, 20):
    config = create_presidential_config(
        num_actors=100,
        policy_dim=2,
        iterations=200,
        seed=seed,
    )
    engine = build_engine(config)
    engine.run()
    rates.append(engine.pass_rate)

print(f"min={min(rates):.1%}, max={max(rates):.1%}, avg={sum(rates)/len(rates):.1%}")
```


## One-liner runners

Run a full simulation in a single function call:

```python
from policyflux import run_presidential

votes = run_presidential(num_actors=100, policy_dim=2, iterations=200, seed=42)
passed = sum(1 for v in votes if v > 50)
print(f"Pass rate: {passed / len(votes):.1%}")
print(f"Accepted: {passed}, Rejected: {len(votes) - passed}")
```

Available: `run_presidential`, `run_parliamentary`, `run_semi_presidential`.

## TF-style model API

Construct simulations as layer graphs, similar to Keras:

```python
from policyflux.model import Sequential
from policyflux.model import layers as L

model = Sequential(num_actors=100, policy_dim=2)
model.add(L.IdealPoint())
model.add(L.PublicOpinion(support=0.6))
model.add(L.PartyDiscipline(strength=0.5, line_support=0.7))

model.compile(executive="presidential", aggregation="sequential")
results = model.run(iterations=200, seed=42)
model.summary()
```

## Common setup issues

!!! warning "Import error after installation"
    Confirm your terminal uses the same Python interpreter where `policyflux` was installed.

!!! warning "Unstable comparisons"
    Keep `seed` fixed and change only one parameter group per experiment.

!!! warning "Runtime too slow"
    Reduce `num_actors` and `iterations` for exploratory work, then scale up.

## Troubleshooting matrix

| Symptom | Likely cause | Fast check | Fix |
| --- | --- | --- | --- |
| `ModuleNotFoundError: policyflux` | wrong interpreter | `python -c "import sys; print(sys.executable)"` | install in that interpreter or switch env |
| inconsistent first-run results | seed changed or hidden config change | print config + seed before run | freeze seed and vary one variable |
| very low throughput | oversized exploratory settings | check `num_actors`, `iterations` | downscale for exploration, upscale after validation |
| hard-to-interpret deltas | too many simultaneous changes | compare config snapshots | isolate one independent variable |

## What to inspect after run

- `engine.pass_rate`
- `engine.accepted_bills`
- `engine.rejected_bills`

These metrics provide a baseline before adding more complex layers and actor mechanics.

## Suggested next 30 minutes

1. Read [Concepts](user-guide/concepts.md) to map model assumptions.
2. Tune one variable in [Configuration](user-guide/configuration.md).
3. Compare two systems with [Presets](user-guide/presets.md).
4. Review runtime options in [Engines](user-guide/engines.md).

## Development commands

```bash
pytest
ruff check policyflux/
mypy policyflux/
mkdocs serve
```

## Next

Choose your next path:

- API surface and entry points: [API Overview](api-overview.md)
- Configuration depth: [Configuration](user-guide/configuration.md)
- Internals and module map: [Architecture](architecture.md)
