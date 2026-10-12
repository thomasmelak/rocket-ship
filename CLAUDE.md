# CLAUDE.md — rocket-ship

## Current state of the repository

As of this writing the repository contains **a single file**: `.github/workflows/build.yml`
(one commit, `Create build.yml`). There is no application source, `package.json`,
README, or tests checked in yet. This document therefore maps what is actually
present (the CI/CD pipeline) and records what that pipeline implies about the
intended project. Update it once source code is added.

## Functionality map

```
.github/workflows/build.yml   "Build CI" — build and deploy to GitHub Pages
```

### Workflow: Build CI

**Triggers**
- `push` to `main`
- `pull_request` targeting `main`

**Job `build`** (`ubuntu-latest`)
- Permissions: `id-token: write`, `pages: write` (required for Pages deployment)

| # | Step | Action / command | Purpose |
|---|------|------------------|---------|
| 1 | Checkout | `actions/checkout@v4.2.1` | Fetch repo |
| 2 | Setup Node.js | `actions/setup-node@v4.0.4`, Node `22.x` | Toolchain |
| 3 | Install | `npm i` | Install dependencies |
| 4 | Build Tailwind | `npm run build:tailwind` | Compile Tailwind CSS |
| 5 | Build project | `npm run build` | Produce static site in `build/` |
| 6 | Upload artifact | `actions/upload-pages-artifact@v3.0.1`, path `build` | Package output |
| 7 | Deploy | `actions/deploy-pages@v4.0.5` | Publish to GitHub Pages |

## Implied project conventions

- Node.js 22 project using npm and **Tailwind CSS**.
- `package.json` must define two scripts: `build:tailwind` and `build`.
- `npm run build` must output static files to a top-level **`build/`** directory
  (a `build/` output dir is typical of Create React App / Vite configured with
  `outDir: build`; the framework is not confirmed).
- The site is a static site hosted on **GitHub Pages**.

## Caveats / things to review

- The deploy step runs on pull requests too, not only on `main`; the job also lacks
  `contents: read` and an `environment: github-pages` block, which `deploy-pages`
  normally expects. Consider splitting into build and deploy jobs and gating deploy
  to `main`.
- `npm i` is used instead of `npm ci`; with a lockfile, `npm ci` gives reproducible builds.
- Repo must have Pages source set to "GitHub Actions" in settings.

## Working in this repo

- Develop on the designated feature branch; do not push to `main` directly.
- Only open pull requests when explicitly requested.
