# Phase 8.5: The Migration Spec

The spec (`docs/MIGRATION-<TARGET>.md`) is the implementation playbook for the chosen route. It isn't paperwork written after the real work — it *is* the real work, distilled from the audit. Use `assets/MIGRATION-SPEC-TEMPLATE.md` as the skeleton.

## What it must contain

1. **Target architecture** — layers, boundaries, and the design rules that exist specifically to kill classes of cataloged findings (e.g., "no module-level mutable state" because global-state coupling was a top category; "parameterized queries only, enforced at the repository layer" because of injection findings). Every architectural rule should be traceable to a reason, and most reasons live in the catalog.
2. **Stack** — exact frameworks/libraries with versions, chosen in Phase 8. Include replacements for each dead dependency the audit flagged (what replaces the EOL report engine, the ancient data layer, the hand-rolled crypto).
3. **Standards** — coding conventions, test strategy (unit/integration/smoke and what gates a merge), commit conventions.
4. **Findings map** — every critical and high finding ID from the catalog mapped to the arc that designs it out. This table is what makes the catalog an executable specification. Medium/low findings can map at category level.
5. **Roadmap: arcs → stories → slices.** Arcs are epics (typically 8–15 for a full rewrite), ordered so each stands on the previous: foundation/skeleton first, then data layer, then auth, then domain core, then the presentation surfaces, then cross-cutting features, then data-migration tooling, then hardening, then release. Side quests (new features) come dead last — after the audited behavior is rebuilt. Each arc gets a one-line scope in the spec and a full planning file in `docs/arcs/ARC-NN-NAME.md` written just before that arc begins, not all upfront.
6. **Workflow rules** — the slice-loop protocol (see `references/slice-loop.md`), branch naming, merge policy, who reviews.

## Ordering heuristics for arcs

- Data layer before anything that touches data; auth before anything that must be gated.
- Rebuild the center of gravity (the screen/flow everything hangs off, identified in recon) early among the UI arcs — it de-risks the most.
- A data-migration arc (old store → new store importer) belongs near the end but before release; it needs the new schema stable.
- Every arc should end in something demonstrable; arcs that end in "internal progress" hide slippage.

## Estimating honestly

Give per-arc effort ranges and a total, with the assumptions attached (team size, availability). The audit's file-count and findings-count are your calibration inputs. Never present a single-point estimate for a rewrite.
