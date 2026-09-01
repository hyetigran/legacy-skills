# Phases 2–6: The Per-File Audit

This is the grind, and it's where the real value gets made. Every in-scope source file gets a cover-to-cover reading captured as one markdown analysis in long-form prose. Prose, not bullets-only notes, because writing buys three things: cheap cross-linking between files, findings you can back with a citation, and a reference a future rewrite team (or future you) can actually use.

## Order of attack

Audit top-down through the layers, because each layer explains the one below it:

- **Phase 2 — UI / presentation layer.** Forms, views, pages, components, CLI entry points. The UI shows you what the system does for its users.
- **Phase 3 — Business logic.** Services, modules, controllers, helpers. Now the behaviors you saw in the UI get their mechanisms.
- **Phase 4 — Data.** Schema, migrations, queries, stored procedures, reports. What the state actually is and how it's touched.

Within each phase, start from the entry point / center of gravity identified in recon and follow the dependency direction.

## The three mechanical steps (repeat per file until it's muscle memory)

**Step 1 — Read.** Open the file and read every function/handler/helper in sequence. No skimming. The bar: you should be able to narrate the file from memory without reopening it. If you can't, you haven't read it yet.

**Step 2 — Identify smells.** Bugs, dead code, half-finished features, missing permission checks, hardcoded credentials, mutable global state, injection-prone string building, swallowed errors, copy-paste divergence. Every single one gets pinned with a citation — function name in backticks, line numbers. Never "there's a bug somewhere in here."

**Step 3 — Record.** Write the analysis file using `assets/FILE-ANALYSIS-TEMPLATE.md`. Findings recorded here get lifted into the central catalog in Phase 7 — the reading is the thinking; the catalog is its structured output.

## What to capture for every file

1. **Purpose & lifecycle** — why the file exists; initialization → events/calls → teardown.
2. **Surface** — the controls/routes/exports/public functions and what each does.
3. **Permission & validation checks** — both the ones present and, critically, the ones *missing*. Absence is a finding.
4. **Data touches** — every read and every write (DB, files, network), and via what mechanism.
5. **Shared state** — globals/singletons/module state it reads or writes, by name.
6. **Bugs & smells** — each cited, each with a one-line severity guess (finalized in Phase 7).

The standard for findings: **specific, cited, actionable.** "The `lblCopyright_Click` handler autofills admin credentials (lines 88–91); combined with seeded defaults this is one-click admin access" — not "login form has security issues."

## Phase 5: Repo hygiene

Keep original source frozen and separate from your analyses. Analyses live in `docs/`, cite into the source, and never modify it. If the working copy risks accidental edits, suggest the user snapshot the original (e.g., an `ORIGINAL-CODE/` directory or a tagged commit) as the immutable reference.

## Phase 6: Deep dives

After the systematic pass, return to hotspots the pass flagged — the crypto module, the auth path, the file with the most findings, anything the analyses cross-reference heavily — and do a second, adversarial read. Ask of each hotspot: what inputs would break this, who can reach it without authorization, what happens on failure. Update the relevant analysis files rather than creating new ones.

## Pacing and honesty

- Work in batches (e.g., 3–5 files), then summarize progress and headline findings to the user before continuing. This surfaces course corrections early.
- If a file is too large to genuinely read cover-to-cover in one pass, split the analysis by region and say which regions got full reads vs. skims. Never present a skim as a reading.
- Cross-link: when file A's bug depends on file B's behavior, link both analyses to each other. These links are what make the catalog trustworthy.
