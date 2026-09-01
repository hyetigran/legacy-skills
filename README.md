# Legacy Skills

[![skills.sh](https://skills.sh/b/hyetigran/legacy-skills)](https://skills.sh/hyetigran/legacy-skills)

Two complementary agent skills for understanding and modernizing legacy codebases without carrying their hidden problems into a rewrite.

## Skills

### `legacy-audit`

Runs a reverse-first codebase audit: reconnaissance, structure mapping, cover-to-cover file analysis, and a cited, severity-tagged bug catalog. Use it before changing, rewriting, or migrating an unfamiliar system.

### `legacy-migration`

Turns a completed audit into a defensible modernization decision and an executable migration plan. It compares migration routes against real constraints, maps audit findings into a target specification, and guides implementation in reviewable slices.

The intended sequence is:

```text
legacy-audit -> bug catalog -> legacy-migration -> migration plan -> implementation slices
```

## Install

Choose skills interactively:

```bash
npx skills@latest add hyetigran/legacy-skills
```

Install only the audit workflow:

```bash
npx skills@latest add hyetigran/legacy-skills --skill legacy-audit
```

Install only the migration workflow:

```bash
npx skills@latest add hyetigran/legacy-skills --skill legacy-migration
```

Install both explicitly:

```bash
npx skills@latest add hyetigran/legacy-skills \
  --skill legacy-audit \
  --skill legacy-migration
```

## Contents

- [`legacy-audit`](skills/legacy-audit/SKILL.md)
- [`legacy-migration`](skills/legacy-migration/SKILL.md)

Each skill includes its own references and reusable artifact templates.
