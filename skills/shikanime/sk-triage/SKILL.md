---
name: sk-triage
description:
  "Route a shikanime org triage request to sk-triage-issue, sk-triage-pr, or
  sk-triage-discussion."
version: 0.2.0
author: Hermes Agent
license: Apache-2.0
metadata:
  hermes:
    tags: [GitHub, Triage, shikanime-labs, shikanime-studio]
---

# Shikanime Triage — Router

Identify the item kind, then load the matching subskill and follow it.

## Inputs

- `N` : issue, PR, or discussion number.
- `R` : `OWNER/REPO` under `shikanime-labs/*` or `shikanime-studio/*`. Defaults
  to the `origin` remote of the cwd; if not in such a repo, ask.

## Routing

```bash
R=${R:-$(gh repo view --json nameWithOwner -q .nameWithOwner)}
if gh pr view "$N" --repo "$R" --json number >/dev/null 2>&1; then
  KIND=pr
elif gh issue view "$N" --repo "$R" --json id >/dev/null 2>&1; then
  KIND=issue
else
  KIND=discussion   # discussions have no REST view
fi
```

- `KIND=pr` → load `sk-triage-pr`
- `KIND=issue` → load `sk-triage-issue`
- `KIND=discussion` → load `sk-triage-discussion`

If the user already named the kind ("triage PR #5"), skip detection and load
directly. English conventions throughout (see `sk-issue`, `sk-pr`,
`sk-discussion`). Never invent a value the repo does not have.
