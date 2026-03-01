# Docs Deployment (GitHub Pages)

This project publishes documentation with MkDocs via GitHub Actions.

## Stack

- static site generator: MkDocs,
- source files: `docs/*.md`,
- config: `mkdocs.yml`,
- deploy target: GitHub Pages.

## One-time repository setup

In repository settings:

1. Open **Settings -> Pages**.
2. Set **Source** to **GitHub Actions**.
3. Save.

## Automatic deployment

Deployment workflow is defined in `.github/workflows/docs.yml`.

It runs on:

- push to `main` when docs-related files change,
- manual `workflow_dispatch` trigger.

Pipeline stages:

1. install Python,
2. install MkDocs,
3. build static site (`mkdocs build --strict`),
4. upload Pages artifact,
5. deploy to GitHub Pages.

## Local preview

```bash
pip install mkdocs
mkdocs serve
```

Open <http://127.0.0.1:8000>.

## Common issues

- Broken links in docs: run local preview and check navigation links.
- Build fails in CI with strict mode: fix markdown warnings and invalid links.
- Pages not updating: confirm workflow succeeded and Pages source is GitHub Actions.
