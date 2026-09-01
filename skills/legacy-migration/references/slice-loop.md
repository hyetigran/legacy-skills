# Phase 9+: The Slice Loop

The implementation protocol. Its purpose is traceability and human control: every change is small enough to review, every merge is a human decision, and every resolved finding points at the commit that resolved it.

## Units of work

- **Arc** — an epic from the roadmap (e.g., "Data Layer", "Login & Dashboard UI"). Gets a planning file `docs/arcs/ARC-NN-NAME.md` before it starts and a retro entry in the dev log when it completes.
- **Story** — a coherent capability within an arc.
- **Slice** — the atomic unit: 1–3 days of work, ≤ ~500 lines of meaningful diff, always mergeable. If a slice grows past that, split it — oversized slices are where review quality dies.

## The loop, per slice

1. **Branch**: `slice/<arc>-<story>-<slice>-<short-name>` off main.
2. **Build**: implementation + tests + any doc updates, together in the slice. Tests are part of the slice, not a later slice.
3. **Gate**: run the full test gate defined in the spec (unit + integration + smoke). A slice that doesn't pass the gate isn't presented.
4. **Present**: show the human the diff, what it does, which findings/stories it advances, and the gate results.
5. **Human reviews and merges.** The implementer (agent or developer) **never commits to main directly**. Main stays protected: linear history, squash-merge, all gates green. Use Conventional Commits for the squash message.
6. **Record**: append one entry to `docs/DEV-LOG.md` (append-only — never edit past entries; the log is the project's memory). Mark the slice complete in the active arc file. If the slice resolved a finding from the bug catalog, strike that finding through with the slice reference — this is the moment the audit's promise gets cashed.

## Dev log entry shape

Use `assets/DEV-LOG-TEMPLATE.md`. Each entry: date, slice ID, what changed, findings resolved (IDs), gate results, and anything learned that affects later slices.

## Discipline rules

- One slice at a time; no parallel half-done slices on one implementer.
- Scope creep goes to the backlog, not into the current slice. New feature ideas are side quests — they wait until the audited behavior is rebuilt.
- When a slice reveals the plan was wrong, stop and reflect rather than forcing it: a wrong plan usually means something was misunderstood — go back to the relevant analysis file, correct the understanding, amend the arc plan, then resume. Understand → plan → execute → reflect.
- Each completed arc gets a tag (`arc-NN-complete`) and a short retro in the dev log: what went as planned, what didn't, what changes for the next arc.
