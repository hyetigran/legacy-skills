# Migration Spec — <System> → <Target Stack>

> Route chosen in [MIGRATION-OPTIONS.md](./MIGRATION-OPTIONS.md). Specification derived from the audit in `docs/` and the catalog in [BUGS-MITIGATIONS.md](./BUGS-MITIGATIONS.md).

## Target architecture

Layers, boundaries, and the design rules — each rule annotated with *why* (usually a catalog category it exists to kill).

## Stack

| Concern | Original | Replacement | Why |
|---|---|---|---|
| Language/runtime | | | |
| UI | | | |
| Data store | | | |
| Data access | | | |
| Reporting | | | |
| Auth/crypto | | | |

## Standards

- Coding conventions:
- Test strategy & merge gate:
- Commit conventions: Conventional Commits, squash-merge to protected main.

## Findings map

| Finding ID(s) | Severity | Designed out by | How |
|---|---|---|---|
| F-001 | Critical | Arc 03 | |

(All critical/high findings individually; medium/low may map by category.)

## Roadmap

| Arc | Focus | Depends on | Effort (range) | Demonstrable outcome |
|---|---|---|---|---|
| 01 | Foundation — skeleton, DI, test infra | — | | |
| 02 | Data layer | 01 | | |
| … | | | | |
| NN | Release — installer, cutover | all | | |

Total estimate: <range>, assuming <team/availability assumptions>.

## Workflow

Slice loop per `references/slice-loop.md`: branch `slice/<arc>-<story>-<slice>-<name>`, full gate before presenting, human merges, `DEV-LOG.md` append per slice, catalog strikethrough on resolution, `arc-NN-complete` tags.
