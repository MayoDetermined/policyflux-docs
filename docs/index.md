# PolicyFlux Documentation

<div class="hero" markdown>

## Computational framework for legislative process modeling

PolicyFlux provides a structured Python environment for simulating voting dynamics,
institutional behavior, and policy outcomes. It supports deterministic and stochastic
execution modes with configurable influence layers and actor models.

[Documentation](getting-started.md){ .md-button .md-button--primary }
[API Reference](api-overview.md){ .md-button }

</div>

<div class="code-preview" markdown>
<div class="code-preview__header">Example — baseline simulation</div>

```python
from policyflux import build_engine, IntegrationConfig, LayerConfig

config = IntegrationConfig(
    num_actors=50, policy_dim=2, iterations=100, seed=12345,
    layer_config=LayerConfig(
        include_ideal_point=True,
        include_public_opinion=True,
        public_support=0.60,
    ),
)

engine = build_engine(config)
engine.run()
print(f"Pass rate: {engine.pass_rate:.1%}")
```

</div>

## Overview

<div class="grid cards" markdown>

-   :material-tune-variant: **Composable configuration**

    ---

    Construct experiments using `IntegrationConfig`, institutional presets,
    and flat parameter overrides. Modify scenario parameters without
    altering simulation internals.

-   :material-chart-line: **Deterministic and stochastic engines**

    ---

    Execute single deterministic runs or parallel Monte Carlo batches.
    Compare outcomes across institutional configurations using
    built-in summary metrics.

-   :material-layers-triple: **Layered influence model**

    ---

    Represent public opinion, lobbying, media pressure, party discipline,
    and agenda control as independent, composable influence layers
    with configurable aggregation.

-   :material-book-open-variant: **Reference documentation**

    ---

    Complete coverage from introductory guides through auto-generated
    API reference. All public interfaces are documented with
    parameter descriptions and usage context.

</div>

<hr class="section-divider">

## Navigation

<div class="nav-cards" markdown>

[:material-lightning-bolt: **Getting Started** <span>Installation and first simulation</span>](getting-started.md)

[:material-api: **API Overview** <span>Public entry points and surface</span>](api-overview.md)

[:material-sitemap-outline: **Architecture** <span>Module layout and design</span>](architecture.md)

[:material-book-open-page-variant: **User Guide** <span>Concepts, layers, engines, presets</span>](user-guide/concepts.md)

[:material-cog-outline: **Configuration** <span>Parameter reference</span>](user-guide/configuration.md)

[:material-source-branch: **Contributing** <span>Development setup</span>](development/contributing.md)

</div>

<hr class="section-divider">

## Documentation scope

<ul class="scope-list" markdown>
<li>User guide: configuration, layers, engines, presets, scenarios</li>
<li>Architecture: module map, runtime flow, design principles</li>
<li>API reference: auto-generated from source via MkDocStrings</li>
<li>Development: testing, quality assurance, release operations</li>
<li>Institutional presets: presidential and parliamentary systems</li>
<li>Execution engines: Monte Carlo and deterministic modes</li>
</ul>

<hr class="section-divider">

## Standard workflow

```text
1. Configure    IntegrationConfig  — from presets, flat dicts, or direct construction
2. Build        build_engine(config)
3. Execute      engine.run()
4. Analyze      engine.pass_rate, accepted_bills, rejected_bills
```

## Choose your starting path

| If you want to... | Start here | Then continue with... |
| --- | --- | --- |
| run your first simulation quickly | [Getting Started](getting-started.md) | [User Guide: Concepts](user-guide/concepts.md) |
| tune simulation parameters precisely | [Configuration](user-guide/configuration.md) | [Layers](user-guide/layers.md), [Engines](user-guide/engines.md) |
| compare institutional systems | [Presets](user-guide/presets.md) | [Scenarios](user-guide/scenarios.md) |
| understand internal design | [Architecture](architecture.md) | [API Reference](reference/index.md) |

## What makes results reproducible

For comparative experiments, keep these inputs constant unless they are your intended independent variables:

- random seed (`seed`),
- number of actors (`num_actors`),
- dimensionality (`policy_dim`),
- layer aggregation strategy (`aggregation_strategy`),
- layer order (`layer_names`) when explicitly configured.

When changing assumptions, modify one group of parameters at a time (for example only `public_support`), then compare output deltas.

## Common first-week workflows

### 1) Baseline + one intervention

1. Run a baseline configuration.
2. Enable one layer (for example public opinion).
3. Re-run with identical seed.
4. Compare `pass_rate` and accepted/rejected counts.

### 2) Institutional comparison

1. Build two configs from system presets.
2. Keep model scale and seed fixed.
3. Run both engines.
4. Compare aggregate outcomes.

### 3) Robustness check

1. Select one configuration.
2. Repeat runs across a seed range.
3. Inspect variance of `pass_rate`.
4. Use Monte Carlo backends for larger batches.

## Where to find details

- Symbol-level API docs: [API Reference](reference/index.md)
- Layer and runtime mechanics: [Layers and Engines](layers-and-engines.md)
- Contributor process and QA: [Development](development/contributing.md)
- Release and deployment workflow: [Release](release.md), [Docs Deployment](deployment.md)
