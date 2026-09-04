# Five-Repository Audit Program

This folder tracks the five repositories being used to exercise and validate the `legacy-audit` workflow:

1. OpenMRS
2. Koha
3. CiviCRM
4. Dolibarr
5. Odoo

The live status, purpose, and available results are in [AUDIT-SUMMARY.md](./AUDIT-SUMMARY.md). OpenMRS, Koha, CiviCRM, and Dolibarr are complete. Odoo is in progress.

## Evidence model

Each repository keeps its full audit evidence close to its source:

```text
repo-name/
└── docs/
    ├── RECON.md
    ├── <area>/
    │   └── <FILE>.md
    ├── data/
    │   └── <FILE>.md
    └── BUGS-MITIGATIONS.md
```

The portfolio summary sits above those repositories. It explains what each audit tests, reports progress, rolls up findings once catalogs are available, and identifies lessons supported across multiple audits.

It does not become a second bug catalog. Every reported finding must point to a stable finding ID in the relevant repository's `BUGS-MITIGATIONS.md`, where the source path, symbol, line range, analysis, and mitigation live.

## Updating the summary

Update [AUDIT-SUMMARY.md](./AUDIT-SUMMARY.md) at three points:

- when an audit starts, record its scope, exclusions, and runtime-recon status;
- while it is in progress, report coverage and provisional themes without publishing uncited findings as conclusions; and
- when it completes, import the severity tally, top findings, confidence limits, and workflow lessons from the final catalog.

If an audit is partial, state exactly which areas were read cover to cover, skimmed, or excluded. Do not combine partial and complete audits without showing that distinction.
