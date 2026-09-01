# <FILE-NAME> — Analysis

> Source: `<path/to/file.ext>` · ~<N> lines · Layer: <UI / logic / data> · Audited: <date>

## Purpose

One or two paragraphs of prose: why this file exists, what user-visible behavior it produces, how it fits the architecture (what calls it, what it calls).

## Lifecycle

Initialization → main events/calls → teardown, in prose. Note anything unusual (non-standard entry, hidden side effects on load, cleanup that never runs).

## Surface

For each control / route / exported function: name, what it does, what it triggers. Keep one line each; expand only where behavior is surprising.

## Permissions & validation

- Checks present: `<function>` (lines N–M) — what it enforces.
- **Checks missing:** where an operation should be gated or validated and isn't. Absence is a finding.

## Data touches

| Operation | Target (table/file/endpoint) | Where (`function`, lines) | Read/Write |
|---|---|---|---|

## Shared state

Globals / singletons / module state this file reads or writes, by name, with where.

## Findings

For each — specific, cited, actionable:

- **[Bug|Smell|Dead code|Security] — <one-line title>.** What happens, why it's a problem, `function` (lines N–M). *Severity guess:* critical/high/medium/low.

## Cross-references

Links to related analysis files this one depends on or explains.
