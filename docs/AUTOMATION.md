# Automation Rollout Guide

This document records every automated agent active in this repository and provides a repeatable checklist for rolling the same setup out to other KaaS Holdings repositories.

---

## Active Automations in `kaas-holdings`

| Workflow file | Trigger(s) | Purpose |
|---|---|---|
| `.github/workflows/jekyll-docker.yml` | push/PR → `main`, manual | Builds the Jekyll site; uploads `_site/` as an artifact |
| `.github/workflows/codeql.yml` | push/PR → `main`, weekly (Mon 06:00 UTC) | CodeQL static security analysis |
| `.github/workflows/stale.yml` | daily (01:00 UTC), manual | Marks and closes stale issues/PRs |
| `.github/workflows/labeler.yml` | PR opened/synced/reopened | Auto-labels PRs by changed file paths |
| `.github/dependabot.yml` | weekly (Mon 06:00 UTC) | Dependabot version bumps for GitHub Actions |

### Labels used by automation

Create these labels in the repository **Settings → Labels** if they do not already exist:

| Label | Color suggestion | Created by |
|---|---|---|
| `stale` | `#ededed` | stale workflow |
| `dependencies` | `#0075ca` | Dependabot / labeler |
| `github-actions` | `#e4e669` | Dependabot |
| `documentation` | `#0075ca` | labeler |
| `site` | `#bfd4f2` | labeler |
| `ci/cd` | `#d4c5f9` | labeler |
| `security` | `#ee0701` | labeler |
| `pinned` | `#0052cc` | manual — exempts issues/PRs from stale |
| `in-progress` | `#fbca04` | manual — exempts issues/PRs from stale |

### Recommended branch-protection rules (PR quality gates)

In **Settings → Branches → Branch protection rules** for `main`, enable:

- ✅ Require status checks to pass before merging
  - Required checks: `build` (Jekyll CI), `analyze` (CodeQL)
- ✅ Require branches to be up to date before merging
- ✅ Do not allow bypassing the above settings

---

## Repeatable Rollout Checklist for Additional Repositories

Use this checklist for every new KaaS Holdings repository:

### Step 1 — Copy workflow files
Copy the following files from this repository into the target repo's `.github/` directory:

```
.github/workflows/codeql.yml
.github/workflows/stale.yml
.github/workflows/labeler.yml
.github/dependabot.yml
.github/labeler.yml
```

> Adjust `codeql.yml` → `language` matrix to match the repo's stack (e.g. `python`, `go`, `java`).

### Step 2 — Adapt or add CI workflow
- If the repo uses a framework (Node, Python, Go, etc.), add a matching build/test workflow.
- Pin all `uses:` references to a specific SHA or tag (e.g. `actions/checkout@v4`).
- Add `concurrency:` and `permissions:` blocks to every workflow.

### Step 3 — Create labels
Run the label list above in the target repository's **Settings → Labels**.

### Step 4 — Set branch protection rules
Apply the required status checks listed above for the repo's `main` (or `master`) branch.

### Step 5 — Enable Dependabot alerts
In **Settings → Security → Dependabot**, enable:
- Dependabot alerts
- Dependabot security updates
- Dependabot version updates (handled by `dependabot.yml`)

### Step 6 — Verify
Open a test PR that touches each path pattern in `labeler.yml` and confirm:
1. Labels are applied automatically.
2. Jekyll/CI build status check appears.
3. CodeQL scan starts.
4. Merge is blocked until both checks pass.

---

## Pending (Remaining Repositories)

As additional repositories are identified, add them here and work through the checklist above for each one.

| Repository | Status | Notes |
|---|---|---|
| *(next repo)* | ⬜ pending | |

---

*Last updated: 2026-07-09 by Copilot automation agent.*
