# Phase 8: Route Generation and Scoring

## Why score instead of choose

Stack preference is the most common failure at this stage. The corrective is mechanical: findings tally first, constraints second, routes scored third, choice last. The output must let a skeptical stakeholder ask "why not just port it?" and receive a scoreboard, not a shrug.

## Step 1 — Findings tally

Pull the severity tally from the bug catalog (e.g., "150 findings: 4 critical, 23 high…") and characterize the *shape* of the problem: is it fundamentally sound code with dated dependencies, or is the design itself the bug (no security model, global-state coupling everywhere)? High counts of design-level findings push toward greenfield; mostly-dependency findings push toward remediation or incremental migration.

## Step 2 — Constraints interview

Ask the user (don't assume):

- **Budget & timeline** — hard caps, and what happens if they slip.
- **Team** — headcount, the skills they actually have today (not aspirations), who maintains it after.
- **Fidelity** — must it look identical, behave identically, or just serve the same jobs? Are there users to retrain?
- **Must-keeps** — report formats, file formats, integrations, hardware/OS, data that must migrate.
- **Tooling costs** — automated translators and commercial migrators have license costs; compare them honestly against time saved (for small codebases the license often costs more than the labor it replaces).
- **Deployment** — desktop/web/mobile, one machine or fleet, offline requirements.

## Step 3 — Generate 4–6 genuinely different routes

Cover different *strategies*, then vary stacks within the best-fitting strategy:

1. **Keep & remediate** — fix critical/high findings in place, replace EOL dependencies, no rewrite. Cheapest; carries the architecture forward.
2. **Incremental / strangler** — new system grows around the old, feature by feature, both live during transition. Lowest cutover risk; longest coexistence pain.
3. **Automated translation + cleanup** — machine-translate to a successor language, then modernize. Fast to "compiles"; inherits every cataloged bug, plus tool licensing.
4–6. **Greenfield in candidate stacks** — 2–3 realistic target stacks given the team and deployment (e.g., C#/.NET desktop, Python + Qt, TypeScript web/Tauri, Java/JavaFX). Designs the findings out; highest upfront effort.

For each route record: stack, honest effort range (months, with the assumption behind it), what it does with the cataloged findings (inherits vs. designs out), and its biggest weakness.

## Step 4 — Score and recommend

Build a comparison table: routes × constraints, scored coarsely (✔ / ~ / ✘ or 1–5) with one-line justifications. Then recommend the route **whose weaknesses the organization can live with** — that's the selection criterion, verbatim. State the decisive trade-offs in plain language.

## MIGRATION-OPTIONS.md structure

```markdown
# Migration Options — <System>
## Findings tally & problem shape
## Constraints                      (from the interview, stated as facts)
## Routes
### Route A — <name>                (strategy, stack, effort range, findings handling, weaknesses)
### Route B … 
## Scoreboard                       (routes × constraints table)
## Recommendation                   (the route, and the trade-off reasoning)
```
