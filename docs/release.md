# Release Guide

This guide defines the release playbook for publishing PolicyFlux to PyPI safely and repeatably.

!!! info "Release objective"
	Produce a tagged, traceable, and installable artifact with validated metadata,
	synchronized versioning, and reproducible CI publication.

## Release stages (at a glance)

1. local validation and packaging checks,
2. version and changelog update,
3. GitHub tag + release publication,
4. trusted-publishing CI to PyPI,
5. post-release verification.

## 1) Pre-release checklist

Run all quality gates in a clean environment:

```bash
pip install -e ".[dev]"
pytest tests/
ruff check policyflux/
mypy policyflux/
python -m build
twine check dist/*
```

**Expected result:** all commands pass and `twine check` reports valid distributions.

## 2) Versioning and changelog

1. Update `policyflux/__init__.py` (`__version__`).
2. Update `pyproject.toml` version.
3. Move `Unreleased` changelog entries into a dated release section.
4. Commit and push to `main`.

!!! warning "Version consistency is mandatory"
	Do not publish if version values differ across source files and package metadata.

## 3) Create GitHub release

1. Create and push a tag (for example `v0.1.1`).
2. Create a GitHub Release from that tag.
3. Publish the release.

The `.github/workflows/publish.yml` workflow builds artifacts, validates metadata, and publishes to PyPI.

## 4) Trusted publishing setup

In PyPI project settings, configure a trusted publisher for:

- owner/repo: `MayoDetermined/policyflux`,
- workflow: `.github/workflows/publish.yml`,
- environment: `pypi` (if used).

## 5) Post-release verification

Validate resolution and installed version:

```bash
pip install policyflux
python -c "import policyflux; print(policyflux.__version__)"
```

Confirm the expected version resolves from PyPI.

## 6) Rollback and incident notes

- If CI publish fails: inspect workflow logs and artifact metadata first.
- If wrong version was tagged: create a corrective release instead of rewriting published history.
- If install resolution is stale: wait for index propagation, then verify in a fresh environment.

## Release checklist for maintainers

- [ ] tests, lint, type-check, build and `twine check` pass,
- [ ] version + changelog updated consistently,
- [ ] tag and release published from intended commit,
- [ ] PyPI publication successful,
- [ ] install verification done in clean environment.
