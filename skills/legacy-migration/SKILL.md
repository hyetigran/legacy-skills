---
name: legacy-migration
description: Turn a completed legacy-code audit into a defensible migration decision and an executable rewrite plan — score candidate routes against real constraints, write the migration spec, and run a disciplined slice-loop implementation. Use this whenever the user asks how to modernize, rewrite, port, migrate, or replace a legacy system, asks "should we rewrite or refactor", wants to choose a target stack for a rewrite, wants a phased rewrite roadmap, or is starting/continuing implementation of a planned rewrite. Works for any source and target language. Requires (or will first help produce) an audit with a bug catalog — pair with the legacy-audit skill.
---

# Legacy Migration (Forward Pass)

The forward half of a reverse-first modernization: given an audit of a legacy system, decide the route, specify the target, and implement it in reviewable slices. The guiding move: **don't pick a stack first.** The instinct is "rewrite it in <favorite language>." The process does the opposite — tally the audit's findings, score candidate routes against the constraints that actually matter, and pick the route whose *weaknesses the organization can live with*. That turns an engineering opinion into an engineering decision, and a scored decision defends itself when stakeholders push back: you don't say "trust me," you show the scoreboard.

## Prerequisite: the audit

This skill consumes audit artifacts — at minimum a severity-tagged bug catalog (`docs/BUGS-MITIGATIONS.md` or equivalent) and ideally per-file analyses. **The audit is the specification**: when the reading and cataloging were done properly, the rewrite plan almost falls out of them, and the rewrite team's job becomes designing the cataloged problems *out*, not porting them across.

If no audit exists, stop and say so. Designing a migration for a system nobody understands reproduces every landmine in a new language. Offer to run the audit first (use the `legacy-audit` skill if available). Don't accept "we've eyeballed it, let's just rewrite" — even a throwaway verdict requires understanding what's being thrown away.

## Phase 8: Choose the route

Read `references/route-scoring.md`, then:

1. **Gather constraints from the user** — budget, timeline, team size and actual skills, must-keep integrations (reporting engines, file formats, hardware), deployment targets, and required fidelity to the original (pixel-faithful vs. workflow-faithful vs. spirit-faithful).
2. **Generate 4–6 candidate routes** spanning genuinely different strategies, not just different languages: keep-and-remediate, incremental/strangler migration, automated translation + cleanup, greenfield rewrites in 2–3 plausible stacks.
3. **Score every route against every constraint**, with honest effort ranges and honest weaknesses. No free options exist; pretending otherwise destroys the decision's credibility.
4. **Recommend one**, with the trade-off reasoning stated plainly (e.g., "the codebase is small enough that the translation tool's license costs more than the time it saves, and 150 cataloged bugs are easier to design out than to fix in place").

Output: `docs/MIGRATION-OPTIONS.md` — the evaluated routes, the scoreboard, the recommendation.

## Phase 8.5: Write the spec

Read `references/migration-spec.md`, then produce the implementation playbook for the chosen route using `assets/MIGRATION-SPEC-TEMPLATE.md`: target architecture, stack, coding/test standards, and a roadmap of **arcs** (epics) broken into stories and commit-sized slices. Every critical/high finding in the bug catalog must map to the arc that designs it out — that mapping is what makes the catalog the spec rather than a wishlist.

Output: `docs/MIGRATION-<TARGET>.md` plus per-arc planning files in `docs/arcs/` (written just-in-time, before each arc begins).

## Phase 9 onward: Implement via the slice loop

Read `references/slice-loop.md` before writing any implementation code. The loop in one breath: one branch per slice, code + tests + doc updates, full test gate, present the diff, the **human** reviews and merges — the implementer never commits to main. Each slice appends one entry to `docs/DEV-LOG.md`, and when a slice resolves a catalog finding, that finding is struck through with a slice reference. Traceability from bug → mitigation → arc → slice → commit is the whole point.

Side quests — enhancements and new features — come **dead last**, after the audited behavior is rebuilt. New scope during the rebuild is how rewrites die.

## Throughout: the operating cycle

Understand → plan → execute → reflect. A bad plan means something wasn't understood; a bad implementation means the plan was wrong. When something breaks, walk back up that chain rather than patching forward. And keep the human the driver: present decisions with reasoning and wait for direction at every route choice, every arc boundary, every merge.
