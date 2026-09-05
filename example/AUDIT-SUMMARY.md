# Five-Repository Legacy Audit Summary

> Status as of 2026-09-05. This is the program-level rollup; repository audit artifacts remain the source of truth for findings.

## Executive summary

This program tests the `legacy-audit` workflow against five materially different legacy-code challenges. OpenMRS, Koha, CiviCRM, Dolibarr, and Odoo are complete. Overall progress is **five of five audits complete**.

The sequence is deliberate. It starts by checking whether a method first exercised on OpenEMR transfers to OpenMRS, moves into genuine Perl archaeology with Koha, then tests adapters and compatibility layers in CiviCRM. Dolibarr tests financial and multi-entity correctness and expands that inquiry across a full ERP codebase. Odoo is the final scale test: a manifest-driven audit across a highly modular platform.

The five completed catalogs contain **1,491 deduplicated root causes: 181 critical, 770 high, 405 medium/systemic, and 135 low**. All five audits are static reviews with runtime-verification limits, so these figures describe cataloged code risks rather than confirmed exploitability in every deployment.

## Program status

<table width="100%" style="width: 100%; table-layout: fixed; border-collapse: collapse">
<colgroup>
<col style="width: 1% !important; max-width: 10px !important" />
<col style="width: 15%" />
<col style="width: 16%" />
<col style="width: 20%" />
<col style="width: 27%" />
<col style="width: 21%" />
</colgroup>
<thead>
<tr>
<th style="width: 1% !important; min-width: 10px; max-width: 10px !important; padding: 0">#</th>
<th>Repository</th>
<th>Primary stack</th>
<th>Repository scale</th>
<th>Audit status and coverage</th>
<th>Findings</th>
</tr>
</thead>
<tbody>
<tr>
<td style="width: 1% !important; min-width: 10px; max-width: 10px !important; padding: 0">1</td>
<td><a href="../../audit-five/openmrs-core">OpenMRS</a></td>
<td>Java, XML, Maven</td>
<td>1,898 tracked files; ≈166K production Java LOC</td>
<td><strong>Complete</strong> — 345 analyses; 1,163 observations</td>
<td>80 (22 C / 36 H / 18 M / 4 L)</td>
</tr>
<tr>
<td style="width: 1% !important; min-width: 10px; max-width: 10px !important; padding: 0">2</td>
<td><a href="../../audit-five/Koha">Koha</a></td>
<td>Perl, Template Toolkit, JS/Vue, SQL</td>
<td>8,269 tracked files; ≈490K Perl lines</td>
<td><strong>Complete</strong> — 873 reports across the six slices</td>
<td>62 (22 C / 30 H / 10 M / 0 L)</td>
</tr>
<tr>
<td style="width: 1% !important; min-width: 10px; max-width: 10px !important; padding: 0">3</td>
<td><a href="../../audit-five/civicrm-core">CiviCRM</a></td>
<td>PHP, Smarty, JavaScript, MySQL</td>
<td>7,923 tracked files; &gt;1.1M executable/source lines</td>
<td><strong>Complete</strong> — 1,504 production-source mappings; 1,529 audit documents</td>
<td>62 (13 C / 38 H / 8 M / 3 L)</td>
</tr>
<tr>
<td style="width: 1% !important; min-width: 10px; max-width: 10px !important; padding: 0">4</td>
<td><a href="../../audit-five/dolibarr">Dolibarr</a></td>
<td>PHP, JavaScript, SQL</td>
<td>15,617 tracked files; ≈1.95M PHP lines</td>
<td><strong>Complete</strong> — full logical-part coverage with source-count reconciliation</td>
<td>655 (101 C / 396 H / 120 M / 38 L)</td>
</tr>
<tr>
<td style="width: 1% !important; min-width: 10px; max-width: 10px !important; padding: 0">5</td>
<td><a href="../../audit-five/odoo">Odoo</a></td>
<td>Python, XML, JavaScript/TypeScript, PostgreSQL</td>
<td>48,195 tracked files; ≈3.34M included source LOC</td>
<td><strong>Complete</strong> — 22,056 direct analyses; 0 first-party files pending</td>
<td>632 (23 C / 270 H / 249 M / 90 L)</td>
</tr>
</tbody>
</table>

Purpose and high-level results are detailed in the repository sections below.

LOC values are physical sizing signals taken from each audit's baseline inventory, not normalized semantic SLOC, and their included file types differ. “Complete” retains each audit's declared boundary: Koha and CiviCRM are bounded audits, while Dolibarr uses complete logical-part reconciliation.

## Audit 1 — OpenMRS

**Purpose:** verify that the reverse-first audit process developed through the OpenEMR work transfers to a different healthcare repository rather than overfitting to one codebase.

**Status:** complete.

**Evidence:** source commit `aeb580f1b`, audited 2026-09-01. The catalog consolidates 1,163 file-level observations from 345 analysis documents into **80 root causes: 22 critical, 36 high, 18 medium, and 4 low**. Source catalog: `audit-five/openmrs-core/docs/BUGS-MITIGATIONS.md`.

**High-level findings:**

- Authorization is opt-in and proxy-sensitive. Unannotated service methods fail open, a privilege is temporarily granted while it is checked, and same-object calls bypass protected service interception (`F-001`–`F-003`).
- Patient merge can run finalization twice, revive voided identities, displace survivor data, and produce contradictory provenance (`F-012`, `F-013`, `F-047`).
- Core clinical invariants rely on race-prone application checks rather than database constraints, affecting identities, allergies, visits, orders, terminology, diagnoses, and medication dispenses (`F-009`, `F-010`, `F-014`–`F-018`, `F-029`, `F-033`, `F-038`).
- The initialization path shares mutable wizard state, can reach destructive setup out of sequence, and builds privileged DDL from request-derived values (`F-004`, `F-005`).
- Module, storage, serialization, and datatype extension surfaces permit path escape, unsafe reconstruction, or trusted code execution without a sufficiently narrow provenance and capability boundary (`F-006`–`F-008`, `F-050`–`F-052`, `F-064`, `F-065`).
- HL7, scheduler, and outbox work is not uniformly claimed atomically, allowing duplicate clinical or external side effects (`F-009`, `F-020`, `F-053`).

**Result:** the OpenEMR-derived audit method transferred successfully to a different healthcare platform and produced a traceable, deduplicated system-level catalog. The audit also exposed a verification constraint: Maven tests could not run because the workspace lacked Java 21, so the findings require Java 21 regression, database, and multi-node validation before remediation or release decisions.

## Audit 2 — Koha

**Purpose:** test whether the workflow can reconstruct behavior in a genuinely old, convention-heavy Perl system without reducing the audit to dependency scanning or generic modernization advice.

**Status:** complete.

**Evidence:** source commit `481e170da1484a70bd281d6c4299adf8f2134751`, static review closed 2026-09-02. The audit produced 873 structured reports—444 UI/contract, 231 logic, and 198 data—across six agreed slices: circulation, authentication/authorization, OPAC/public search, cataloguing/search indexing, acquisitions/serials, and REST API. It found **62 root causes: 22 critical, 30 high, and 10 medium/systemic**. Source catalog: `audit-five/Koha/audit/BUG-CATALOG.md`.

The scope was intentionally bounded: the six slices are complete, but the audit does not claim cover-to-cover coverage of all 8,269 repository files.

**High-level findings:**

- Patron-management boundaries permit plausible staff-account takeover, superlibrarian privilege import, and staff password reset by insufficiently privileged operators (`members/memberentry.pl:187-253,604-654`; `Koha/Patrons/Import.pm:301-394,432-476,642-657`; `Koha/REST/V1/Patrons/Password.pm:43-65`).
- Authentication reuses anonymous session state, has inconsistent 2FA paths, stores cleartext passwords during public-registration verification, and does not revoke existing sessions after password changes (`C4/Auth.pm:940-964,1194-1377,1651-1815`; `opac/opac-memberentry.pl:197-209`; `Koha/Patron.pm:1075-1175`).
- Reachable SQL injection and stored/client-side script injection occur in staff catalog and OPAC paths (`C4/HoldsQueue.pm:1365-1376`; `catalogue/itemsearch.pl:178-207`; `C4/Items.pm:1421-1445,1593-1643`; catalog High findings 12–14).
- Circulation, holds, lost-item reversal, acquisitions, serials, and item-move workflows commit related state separately, making partial success, incorrect financial state, and catalog-graph corruption normal failure modes (Critical findings 11–18; High findings 6–11 and 16, 24–30).
- Several unauthenticated or under-authorized routes mutate serial routing, payment-plugin, suggestion, bundle, and agency state (Critical findings 15, 19, 20, 22; High finding 29).
- Zebra acknowledges indexing work before success, while Elasticsearch workers can report success after partial loss, so durable catalog/search changes can disappear silently (Critical finding 21; High finding 22).

**Result:** bounded vertical slices made genuine Perl archaeology tractable without pretending the entire repository was audited. The process successfully connected CGI entry points, Perl modules, templates, OpenAPI contracts, database definitions, and tests, but it also confirmed that scope declarations are essential for a codebase of this size. Runtime verification was unavailable because the workspace lacked Koha's Perl dependencies, database/configuration, and supporting services; all severities remain provisional until reproduced in a representative deployment.

## Audit 3 — CiviCRM

**Purpose:** test the audit method against adapter-heavy architecture, external integrations, extension points, and compatibility layers accumulated across versions.

**Status:** complete.

**Evidence:** source commit `93cefae00a4635b6de5b5f5f69bda647e8e1cad3` (`6.19.alpha1`), five-pass static audit completed 2026-09-02. The declared boundaries contain 1,504 production-source mappings across identity/external trust, constituent/contact core, money/payment, installer/bootstrap, and event registration. The catalog contains **62 root causes: 13 critical, 38 high, 8 medium, and 3 low**. Source catalog: `audit-five/civicrm-core/docs/BUGS-MITIGATIONS.md`.

The scope was intentionally bounded: 1,529 audit documents cover the five passes, but the audit does not claim cover-to-cover coverage of all 7,923 tracked files. Major excluded areas include mailings, memberships, cases, reports/search builders, campaigns, unrelated extensions, and most historical/generated/third-party material.

**High-level findings:**

- A network-reachable fresh Standalone deployment can be installed and claimed by an unauthenticated client, making the first-administrator setup route a critical ownership boundary (`INST-001`; `Civi/Standalone/WebEntrypoint.php:17-40,98-123`).
- Contact tasks, token rendering, nested child records, relationships, groups, and imports repeatedly authorize the feature but trust caller-selected Contact or child IDs instead of enforcing target-record ACLs (`CON-001`–`CON-004`, `CON-006`).
- Authorize.Net and PayPal Pro/Express recurring callbacks accept unauthenticated provider notifications, while payment edit, additional-payment, refund, and negative-payment paths lack consistent operation permission and record binding (`PAY-001`–`PAY-004`, `PAY-007`, `PAY-008`).
- Event flows repeat the same boundary failure: participant tasks accept arbitrary targets, fee selection reaches linked financial state, Event Cart exposes unauthenticated Participant deletion, and public dedupe can overwrite an existing Contact (`EVT-001`–`EVT-004`).
- Authentication and capability paths have weak or fragmented lifecycle controls, including fail-open MFA orchestration, replay-prone TOTP/remembered-device state, URL credentials, unbounded token lifetimes, and malleable default encrypted tokens (`ID-003`–`ID-007`, `CON-005`).
- Payment, event-registration, installation, and schema-generation workflows lack atomic recovery boundaries; gateway success, local state, fulfillment, files, and database changes can diverge after partial failure (`PAY-009`, `PAY-011`, `INST-004`, `INST-007`, `EVT-006`–`EVT-008`).
- Installer-created world-writable paths can feed generated executable PHP, default CMS grants expose constituent/upload data, and legacy setup tools expose credentials or default to destructive reset behavior (`INST-003`, `INST-005`, `INST-008`).

**Result:** the audit confirmed the hypothesis behind the CiviCRM test: adapters and compatibility layers are active trust boundaries, not passive maintenance baggage. Standalone/CMS hosting, API3/API4, legacy AJAX/forms, payment processors, bundled extensions, and shared BAOs each carry partial security assumptions, so checks applied in one path do not reliably protect parallel paths. The five bounded passes successfully traced those assumptions through UI, integration, domain, and persistence layers without claiming whole-repository coverage.

The application could not run because the workspace lacked PHP, Composer, MySQL, installed dependencies, configuration, and a CMS/Standalone host. Dynamic routing, optional extensions, provider behavior, browser execution, concurrency, rollback, and filesystem ownership therefore remain integration-verification gates.

## Audit 4 — Dolibarr

**Purpose:** test financial correctness, transaction boundaries, rounding, currency handling, auditability, and multi-entity authorization in a legacy ERP.

**Status:** complete.

**Evidence:** baseline `8805e0ef12faebf28554d19515586107051759ab` on `develop`, static audit completed 2026-09-04. The coverage manifest reconciles the complete codebase in logical parts across 15,617 tracked files, including first-party executable paths, APIs, integrations, domain workflows, migrations, administration, public surfaces, tests, tooling, and deployment assets. Exact upstream third-party copies and non-source assets were inventoried through provenance rather than redescribed line by line. The catalog contains **655 findings: 101 critical, 396 high, 120 medium, and 38 low**. Source catalog: `audit-five/dolibarr/docs/BUGS-MITIGATIONS.md`.

Configuration dependence is material: 82 critical, 291 high, 78 medium, and 14 low findings require an unsafe option, module, deployment condition, or missing production safeguard. The catalog marks these with an asterisk; they remain findings about reachable source paths, not claims that every deployment exposes them.

**High-level findings:**

- Multi-entity and object-level authorization is repeatedly enforced against a route-level permission or a caller-selected decoy object rather than the record actually read or changed. The pattern spans payments, stock, customers, users, administration, products, sales, finance, HR, projects, public routes, and REST/SOAP/MCP surfaces (`F-009`, `F-014`–`F-016`, `F-021`–`F-024`, `F-107`–`F-121`, `F-140`–`F-185`, `F-210`–`F-455`, `F-635`–`F-655`).
- Financial workflows do not consistently bind child rows and allocations to an authorized parent or enforce atomic lifecycle transitions. Credits can cross customers, payments and invoice lines can target unrelated records, direct-debit work can be claimed twice, and bookkeeping permits unbalanced or non-idempotent operations (`F-014`, `F-345`–`F-404`, `F-417`–`F-464`).
- Authentication and privileged maintenance have critical lifecycle gaps: sessions are not rotated at login, password-reset credentials do not expire or become consumed, unlocked install/upgrade/repair routes can create administrators or execute destructive actions, and tenant administrators can reach instance-wide backup, schema, session, secret, and module-deployment controls (`F-002`, `F-005`, `F-006`, `F-107`, `F-117`, `F-120`–`F-126`, `F-186`–`F-190`, `F-635`).
- External integration boundaries routinely expose credentials or accept insufficiently authenticated operations. This includes legacy SOAP, AI/MCP tools, Stripe and PayPal, webhooks, WebDAV, email collectors, PBX, OAuth/OIDC, LDAP, public payment callbacks, and cross-tenant provider state (`F-015`–`F-079`, `F-341`, `F-515`, `F-554`–`F-570`, `F-619`–`F-625`).
- Public capabilities are often permanent, predictable, replayable, or weakly scoped. Online signing, surveys, public calendars, portals, TakePOS, donations, membership, event suggestions, payment returns, virtual cards, and public utilities can cross object or tenant boundaries (`F-102`, `F-492`–`F-565`).
- Migration, installation, packaging, demo, and developer tooling can silently lose data, continue after partial failure, expose credentials, or install/execute unverified content (`F-090`, `F-091`, `F-186`–`F-209`, `F-572`–`F-614`).

**Result:** the financial-workflow test exposed a broader architectural result: Dolibarr's financial integrity depends on a consistent tenant/object boundary that is missing across the surrounding ERP graph. Invoice, payment, bank, stock, customer, supplier, accounting, and administrative state cannot be secured or made atomic by patching one controller family at a time. The audit expanded beyond the originally bounded workflow into a complete logical-part codebase audit, and its coverage manifest makes that scope change explicit.

PHP was unavailable and the Docker daemon could not run, so no runtime or integration tests were possible. Configuration-dependent exposure, transaction outcomes, concurrent behavior, and proposed mitigations require testing in representative multi-entity deployments.

## Audit 5 — Odoo

**Purpose:** test whether the audit process can establish and defend subsystem boundaries inside a very large modular application.

**Status:** complete.

**Evidence:** baseline `f7449ec73ab5679d3a95b411a43081d3646f950e`, Odoo 19.0, audit completed 2026-09-05. The final coverage gate closes all **665 manifest-defined modules**, **22,471 included source-bearing files**, and **3,339,631 included LOC**, with **22,056 direct per-file analyses**, 415 vendored files handled through provenance inventory, and zero pending first-party files. The catalog contains **632 root causes: 23 critical, 270 high, 249 medium, and 90 low**. Sources: `audit-five/odoo/docs/AUDIT-COVERAGE.md` and `audit-five/odoo/docs/BUGS-MITIGATIONS.md`.

The full-codebase scope requires the workflow to:

- identify a subsystem's manifest, entry points, user-visible behaviors, and data ownership;
- distinguish direct dependencies from extension or plugin surfaces;
- declare read-cover-to-cover, skim, and excluded areas without implying whole-repository coverage; and
- synthesize findings without losing citations across subsystem boundaries.

**High-level findings:**

- Weakly scoped public and administrative boundaries can expose records or permit state changes without an owning-record authorization check; the catalog counts 46 security/authorization findings.
- Localization, tax-report, payment, POS, and financial configuration paths contain invariant and carryover risks that can misstate or mispost accounting results.
- Installation, module loading, server actions, asset handling, external integrations, and credential-bearing payment paths create privileged execution and trust-boundary risks.
- Test and fixture isolation gaps can hide regressions or make assertions pass against unrelated state; verification findings are the largest category at 218.
- Data-integrity findings total 152 and reliability/maintainability findings total 216, including deletion, transaction, cache, indexing, and partial-success failure modes.

The application could not start because the environment lacks the declared Babel dependency and has no database or service configuration. This is an audit-environment limitation, not an Odoo defect.

## Findings rollup

| Repository    | Critical |    High |  Medium |    Low |   Total | Evidence status                                                                   |
| ------------- | -------: | ------: | ------: | -----: | ------: | --------------------------------------------------------------------------------- |
| OpenMRS       |       22 |      36 |      18 |      4 |      80 | Complete static catalog; Java 21 runtime verification pending                     |
| Koha          |       22 |      30 |      10 |      0 |      62 | Complete six-slice static catalog; representative deployment verification pending |
| CiviCRM       |       13 |      38 |       8 |      3 |      62 | Complete five-pass static catalog; integrated runtime verification pending        |
| Dolibarr      |      101 |     396 |     120 |     38 |     655 | Complete logical-part static catalog; runtime verification pending                |
| Odoo          |       23 |     270 |     249 |     90 |     632 | Complete 665-module static catalog; runtime verification pending                  |
| **Portfolio** |  **181** | **770** | **405** | **135** | **1,491** | Aggregates all five completed catalogs                                           |

Koha calls its third tier “medium/systemic”; it is included in the Medium column for rollup purposes. Dolibarr's configuration-dependent findings remain included at their assigned severity. “No result yet” is distinct from a zero count.

## Cross-repository conclusions

The five completed catalogs support five shared conclusions:

1. **Authorization is optional or caller-owned instead of intrinsic to the domain boundary.** OpenMRS relies on annotations and proxy interception that can be omitted or bypassed (`F-001`–`F-003`, `F-023`); Koha depends on individual CGI/OpenAPI callers to enforce actor, target, branch, fund, or object scope (Critical findings 1–4 and 22; Medium/systemic finding 2); CiviCRM feature-level checks frequently trust caller-selected Contact, Participant, payment, contribution, or child-record IDs (`CON-001`–`CON-004`, `PAY-003`, `PAY-004`, `EVT-001`, `EVT-002`); Dolibarr repeats the pattern across tenant, customer, parent/child, financial, warehouse, user, and administrative targets (`F-009`, `F-107`–`F-185`, `F-210`–`F-455`, `F-635`–`F-655`).
2. **Multi-object state changes lack reliable transaction and idempotency boundaries.** OpenMRS exposes races and duplicate effects in clinical merge, HL7, scheduler, and outbox paths (`F-009`, `F-012`–`F-015`, `F-020`); Koha separately commits circulation, financial, catalog, acquisition, serial, plugin, notice, and index state (Critical findings 11–18 and 21; High findings 7, 9, 11, 16, 25); CiviCRM can split gateway, contribution, participant, cart, fulfillment, installation, and schema state across unrecoverable partial commits (`PAY-009`, `PAY-011`, `INST-004`, `INST-007`, `EVT-006`–`EVT-008`); Dolibarr's invoice, direct-debit, bank, bookkeeping, stock, mailing, and public-payment workflows show the same failure mode (`F-345`–`F-464`, `F-558`, `F-559`).
3. **Extension and integration surfaces inherit more trust than their provenance warrants.** OpenMRS modules, datatype handlers, templates, and serializers can reach trusted execution or stored content (`F-006`, `F-050`–`F-052`, `F-065`); Koha invokes request-selected payment plugins before authentication and lets plugins participate inside fragile multi-step workflows (Critical finding 19; Medium/systemic finding 3); CiviCRM accepts forgeable processor callbacks and exposes critical behavior through optional Event Cart, CMS adapters, legacy endpoints, and extension-defined settings (`PAY-001`, `PAY-002`, `EVT-003`, `INST-010`); Dolibarr extends the pattern across SOAP, MCP/AI, OAuth/OIDC, payment, WebDAV, email, PBX, LDAP, module, and package-import boundaries (`F-015`–`F-091`, `F-554`–`F-580`, `F-619`–`F-650`).
4. **Systems can report or record success before durable success is known.** OpenMRS work claims and lease recovery can duplicate side effects (`F-009`, `F-020`, `F-027`); Koha's Zebra and Elasticsearch paths can discard failed indexing work while reporting completion (Critical finding 21; High finding 22); CiviCRM can record recurring cancellation after provider failure or accept gateway success without completing local registration (`PAY-009`, `PAY-011`, `EVT-007`); Dolibarr setup, migration, payment, mailing, and scheduled work contains early acknowledgement, partial commits, and non-atomic claims (`F-186`–`F-209`, `F-290`, `F-358`–`F-372`, `F-438`, `F-558`, `F-559`).
5. **Parallel compatibility paths multiply policy drift.** OpenMRS authorization varies with annotations, proxy entry, legacy encoders, and module surfaces; Koha carries CGI, OPAC, REST, offline, plugin, Zebra, and Elasticsearch paths with different assumptions; CiviCRM spans CMS/Standalone hosts, API3/API4, forms/AJAX, payment adapters, and bundled extensions; Dolibarr spans browser controllers, REST, SOAP, MCP, public handlers, optional modules, multi-entity sharing rules, and older operational tooling; Odoo spans the server core, 665 manifests, localization modules, web/POS clients, payment providers, and install-time data. In all five systems, fixing one route or framework layer is insufficient unless the underlying domain boundary becomes mandatory.

The workflow result is equally important: OpenMRS demonstrated large-scale observation deduplication, Koha demonstrated defensible subsystem scoping, CiviCRM demonstrated cross-adapter trust tracing through five overlapping vertical passes, Dolibarr demonstrated full-codebase logical-part reconciliation, and Odoo demonstrated deterministic coverage gating across a manifest-driven platform at 22k+ source files. Dolibarr and Odoo also show the cost of scope expansion: their 655- and 632-finding catalogs are substantially harder to triage than the bounded catalogs. All catalogs preserve code locations, but Koha currently numbers findings within severity sections rather than assigning globally stable IDs; future catalogs should use stable IDs so cross-document references survive reordering.

## Immediate updates needed

1. Re-run OpenMRS's prioritized regression suite under Java 21 with database and multi-node coverage.
2. Reproduce Koha's highest-severity paths in a representative deployment before disclosure or remediation prioritization.
3. Reproduce CiviCRM's critical setup, payment-callback, Contact/Participant authorization, and Event Cart paths in disposable CMS and Standalone environments.
4. Triage Dolibarr's P0 set, then retest with global administrators, tenant administrators, restricted users, and external users while asserting both denial and absence of financial, stock, configuration, or cross-tenant side effects.
5. Reproduce Odoo's highest-severity authorization, accounting, credential, and destructive-operation findings with a working dependency/database environment before remediation or disclosure decisions.

The five completed audits can individually enter migration comparison once their runtime-verification limits and business constraints are understood. The five-repository static audit program is complete; the next phase is deployment-backed reproduction and remediation prioritization.
