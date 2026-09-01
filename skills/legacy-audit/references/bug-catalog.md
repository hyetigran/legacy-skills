# Phase 7: The Bug Catalog

The catalog condenses every finding from every per-file analysis into one triage list: `docs/BUGS-MITIGATIONS.md`. This is the document a rewrite team resolves, the document that turns fear into a spreadsheet, and — done well — the seed of the eventual specification. It is the *input to the rewrite*, not the analysis itself.

## Building it

1. Sweep every analysis file (and RECON.md's free findings) and lift each finding into one catalog row.
2. Deduplicate: the same root cause appearing in five files is one finding with five citations, not five findings.
3. Assign each finding a **severity**, a **category**, and a **concrete mitigation**.
4. Tally by severity at the top (e.g., "150 findings: 4 critical, 23 high, …"). The tally is what stakeholders read first and what migration decisions get scored against.

## Severity scale

- **Critical** — exploitable security flaw, data loss/corruption path, or anything an attacker or a bad day turns into disaster (e.g., reversible password "hashing", one-click admin login, SQL injection on a reachable path).
- **High** — incorrect behavior users hit, latent data bugs, auth gaps requiring some circumstance to exploit.
- **Medium** — reliability and maintainability landmines: swallowed errors, mutable global state coupling, dead-but-reachable code.
- **Low** — smells, dead code that's truly unreachable, cosmetic and consistency issues.

Severity is about consequence, not effort to fix. A trivial one-line bug that leaks credentials is critical.

## Categories

Use categories that fit what the audit actually found — typically ~8–12 such as: Security, Authentication/Authorization, Data integrity, Error handling, Concurrency, Dead code, Hardcoding (credentials, locales, paths), Global state, UI/UX defects, Dependencies (EOL/deprecated), Build/packaging. Categories exist so the rewrite can resolve findings in coherent groups.

## Mitigation, not fix

Each row's mitigation is a *design-level* statement of how a rewrite (or remediation) resolves the finding — "replace XOR encoding with Argon2id password hashing; never store reversible credentials" — not a patch to the old code. The audit remains a read pass; the mitigations column is where the future system gets designed, one row at a time.

## Format

Use `assets/BUG-CATALOG-TEMPLATE.md`. Every row must trace back: ID, severity, category, location (file + function + lines), description, mitigation, and a link to the analysis file that discovered it. An uncited row is an opinion, and opinions don't survive stakeholder pushback — the whole point of the catalog is that it defends itself.

## Presenting it

When the catalog is done, give the user: the severity tally, the top 5–10 findings in plain language, and the pattern-level story ("most high-severity findings cluster in auth and data integrity — the original had no security model"). Then note the fork in the road: keep / remediate in place / rewrite is now a decision they can make with evidence — and if a migration skill is available, it starts from exactly this document.
