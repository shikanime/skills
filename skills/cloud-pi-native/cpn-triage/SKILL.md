---
name: cpn-triage
description: "Route a cloud-pi-native console triage request to cpn-triage-issue, cpn-triage-pr, or cpn-triage-discussion."
version: 0.2.0
author: Hermes Agent
license: Apache-2.0
metadata:
  hermes:
    tags: [GitHub, Triage, cloud-pi-native, French]
---

# CPN Triage — Routeur

Identifier le type d'élément, puis charger la sous-compétence correspondante
et la suivre.

## Entrées

- `N` : numéro d'issue, de PR ou de discussion.
- `R=cloud-pi-native/console` (défaut ; le fork a Issues/PRs désactivés).

## Routage

```bash
R=cloud-pi-native/console
if gh pr view "$N" --repo "$R" --json number >/dev/null 2>&1; then
  KIND=pr
elif gh issue view "$N" --repo "$R" --json id >/dev/null 2>&1; then
  KIND=issue
else
  KIND=discussion   # les discussions n'ont pas de vue REST
fi
```

- `KIND=pr`          → charger `cpn-triage-pr`
- `KIND=issue`       → charger `cpn-triage-issue`
- `KIND=discussion`  → charger `cpn-triage-discussion`

Si l'utilisateur a déjà nommé le type (« triage PR #5 »), sauter la détection
et charger directement. Conventions françaises (voir `cpn-issue`, `cpn-pr`,
`cpn-discussion`). Ne jamais inventer une valeur que le dépôt ne possède pas.
