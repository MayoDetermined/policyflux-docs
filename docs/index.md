# PolicyFlux Documentation

PolicyFlux is a Python framework for modeling legislative behavior, voting dynamics,
and institutional political systems.

<div class="hero" markdown>

## Advanced legislative simulation for research and policy analytics

PolicyFlux combines configurable actors, influence layers, and institutional presets
to run deterministic and Monte Carlo experiments in a single, composable workflow.

[Get started now](getting-started.md){ .md-button .md-button--primary }
[Explore API](api-overview.md){ .md-button }

</div>

## Why PolicyFlux

<div class="grid cards" markdown>

-   :material-tune-variant: **Composable configuration**

	---

	Tune behavior with `IntegrationConfig`, `LayerConfig`, presets,
	and flat overrides for fast scenario iteration.

-   :material-chart-line: **Research-ready engines**

	---

	Compare deterministic runs with sequential and parallel Monte Carlo
	to capture baseline and distributional outcomes.

-   :material-layers-triple: **Layered political dynamics**

	---

	Model public opinion, lobbying, media pressure, party discipline,
	and agenda control in one pipeline.

-   :material-book-open-variant: **Complete developer docs**

	---

	Move from quick start to deep module reference with consistent,
	production-quality documentation pages.

</div>

## Start here

- New users: [Getting Started](getting-started.md)
- Core public interfaces: [API Overview](api-overview.md)
- Internal module map: [Architecture](architecture.md)
- Advanced guides: [User Guide](user-guide/concepts.md)
- Maintainer workflow: [Release Guide](release.md)

## Documentation scope

- user guide for configuration, layers, engines, presets, and scenarios,
- architecture and integration flow,
- API reference generated from source code via MkDocStrings,
- development quality checks and release operations.

## Typical workflow

1. Create an `IntegrationConfig` directly or from presets.
2. Build an engine with `build_engine(config)`.
3. Execute the simulation with `engine.run()`.
4. Analyze metrics such as pass rate and accepted/rejected bills.
