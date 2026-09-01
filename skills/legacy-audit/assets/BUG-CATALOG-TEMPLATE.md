# Bugs & Mitigations — <System Name>

> Synthesized from <N> per-file analyses in `docs/`. Every finding cites source. This catalog is the triage list for any remediation or rewrite.

## Tally

| Severity | Count |
|---|---|
| Critical | |
| High | |
| Medium | |
| Low | |
| **Total** | |

By category: <Security: N · Auth: N · Data integrity: N · …>

## Top findings (plain language)

1. …
2. …

## Catalog

| ID | Sev | Category | Location | Finding | Mitigation | Source analysis |
|---|---|---|---|---|---|---|
| F-001 | Critical | Security | `file.ext` `function` L88–91 | One-line description of what's wrong and why it matters | Design-level resolution for the rewrite/remediation | [LINK.md](./area/FILE.md) |

Conventions:
- IDs are stable (`F-###`); never renumber — later documents will cite them.
- One root cause = one row, even if it appears in many files (list all citations in Location).
- Mitigations describe how the *future* system avoids the problem, not a patch to the old code.
- When a rewrite later resolves a finding, strike the row through with a reference to the change that resolved it.
