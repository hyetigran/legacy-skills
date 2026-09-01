---
name: legacy-audit
description: Run a reverse-first audit of an unfamiliar or legacy codebase — recon, manifest mapping, per-file cover-to-cover analysis, and a severity-tagged bug catalog — before anyone changes or rewrites anything. Use this whenever the user inherits a codebase with no docs/tests/maintainer, asks to "understand", "audit", "reverse engineer", "document", or "assess" an existing system, asks "what does this code do" about a nontrivial repo, wants a bug/risk/security catalog of existing code, or is considering rewriting/modernizing/migrating legacy code (the audit must come first). Works for any language or ecosystem.
---

# Legacy Audit (Reverse-First)

A repeatable method for walking into an unfamiliar, undocumented codebase and coming out with a defensible, cited understanding of it. The core principle: **understand before changing.** A surgeon diagnoses — scans, blood work, history — before operating. Same discipline here: read the source cover to cover, catalog every bug and smell with a severity and mitigation, and only then decide what to do about it.

Why this matters: the default instinct with legacy code is "just rewrite it." But if you port without understanding, you port all the bugs — you faithfully reproduce every landmine in a shiny new language. The audit exists so a future rewrite can *design the problems out* instead of carrying them across.

## The pipeline

Each phase produces artifacts the next phase consumes. **Don't skip; don't loop.** The universal temptation is to jump to the fun redesign part on day one — resist it. The sequence protects you from your own enthusiasm.

| Phase | What | Output |
|---|---|---|
| 0 | Manual recon — README, run it if possible, eyeball the tree | `docs/RECON.md` |
| 1 | Manifest mapping — read the file that lists all other files | Structure map in `docs/RECON.md` |
| 2 | UI-layer audit — per-file prose analysis | `docs/<area>/<FILE>.md` each |
| 3 | Business-logic audit — same template | `docs/<area>/<FILE>.md` each |
| 4 | Data audit — schema, queries, reports | `docs/data/*.md` |
| 5 | Repo hygiene — freeze original source, separate docs | Directory layout |
| 6 | Deep-dive passes on flagged hotspots | Updated analyses |
| 7 | Bug catalog — synthesize all findings | `docs/BUGS-MITIGATIONS.md` |

The order within 2–4 is deliberate: UI first (it shows you what the system *does* for users), then the logic behind it, then the data underneath. Layers, top down.

## How to run it

1. **Read `references/recon.md` and do Phase 0–1 first.** This is non-negotiable, and it's cheap: recon gives you *behavior* to reason about, not just syntax. Without it you'll summarize what the code says instead of what the system does. Never start reading arbitrary source files before you've read the manifest — read the map before you walk the territory.

2. **Then read `references/file-audit.md` and grind through Phases 2–6.** One markdown analysis per source file, using `assets/FILE-ANALYSIS-TEMPLATE.md`. This is where the value gets made. It's methodical, not glamorous.

3. **Finally read `references/bug-catalog.md` and produce Phase 7's catalog** using `assets/BUG-CATALOG-TEMPLATE.md`. The reading is the thinking; the catalog is the structured output of that thinking — it becomes the input to any future rewrite.

If the user asks for only part of this (e.g., "just catalog the bugs"), check whether the upstream artifacts exist. A bug catalog built without per-file readings is guesswork; say so, and offer to run the missing phases first (or do a scoped version and label its confidence honestly).

## Working with the user

- At the start, ask (or infer) two things: **can the application be run** (recon quality depends on it), and **roughly how large is the codebase**. For very large repos, triage: agree with the user on which subsystems to audit cover-to-cover, which to skim, and which to deliberately skip. Choosing what to ignore is a skill — you cannot read everything, and pretending otherwise produces shallow analysis of everything instead of real analysis of what matters.
- Work in bounded passes and report progress: "Forms 1–4 of 16 analyzed, top findings so far: …". Long silent grinds help nobody.
- Every finding must be **cited** — function name, line numbers, file path. A finding you didn't write down (or can't point to) doesn't exist.
- This is a *read* pass. Do not fix anything, refactor anything, or modify original source during the audit. Fixes belong to a later rewrite/remediation phase, informed by the catalog. If you spot something critical (exposed secrets, live vulnerabilities), flag it to the user immediately — but flag, don't patch.

## Output layout

Create all artifacts under a `docs/` directory (or `audit/` if `docs/` is taken), never inside the original source tree:

```
docs/
├── RECON.md                  # Phase 0–1
├── <area>/                   # e.g. forms/, ui/, api/, services/ — mirror the codebase's own areas
│   └── <FILE-NAME>.md        # one per source file audited
├── data/                     # schema, migrations, reports analyses
└── BUGS-MITIGATIONS.md       # Phase 7 catalog
```

If the repo layout allows, keep the original source clearly separated (e.g., under `ORIGINAL-CODE/` or untouched at root) and treat it as frozen — an immutable reference everything else cites into.

## After the audit

The audit's endpoint is a catalog, not a plan. If the user wants to decide *what to do next* — keep, remediate in place, or rewrite — that's a migration decision. If a `legacy-migration` skill is available, hand off to it; the catalog and analyses are exactly the inputs it needs. Either way, the message to leave the user with: the audit *is* the specification. Done properly, the rewrite plan almost falls out of it.
