# Phase 0–1: Reconnaissance and Manifest Mapping

Recon exists because of one failure mode: without knowing what the app actually *does*, any analysis (yours or an LLM's) summarizes syntax instead of behavior — "this function loops over rooms" instead of "this grid is the app's center of gravity and everything hangs off it." Fifteen to thirty minutes of just looking pays for itself across the entire audit. Cheap looking beats expensive guessing.

## Phase 0: Manual recon (no source reading yet)

Do these, in roughly this order, and write findings into `docs/RECON.md` as you go:

1. **Read the README** and any docs, changelogs, license files, installer scripts. Note authorship, dates of first/last activity, license, and stated purpose. History is signal: "built 2014–2021, unmaintained since" tells you the maintenance story before you read a line.
2. **Run the application if at all possible.** Click around. If the user has it running, ask them to describe or screenshot the main screens. Capture: what the main screen is, what the login/entry flow looks like, what the obvious workflows are. If it can't be run, say so in RECON.md and lean harder on screenshots, docs, and the data layer.
3. **Eyeball the file tree** (`ls -R` / `find`, or ask the user for a listing). Note: languages present, rough line counts per area, build artifacts, database files, config files, anything shipped pre-built.
4. **Identify the center of gravity** — the screen, module, or concept everything else hangs off. Naming it now tells you where to aim the deep audit later.
5. **Note the free findings.** Recon routinely surfaces real findings before any source is read: credentials printed on a login screen, EOL dependencies, hand-rolled crypto, hardcoded currencies/locales. Record them in RECON.md; they'll be promoted into the bug catalog later.

## Phase 1: Read the map before you walk the territory

Every ecosystem has a manifest — the file that describes all other files. **Find it and read it before any source file.** It hands you, at near-zero cost: the full file inventory, the dependency list (and their versions — deprecated/EOL ones jump out immediately), the entry point (and whether it's unconventional), and version/branding info.

| Ecosystem | Manifest to read first |
|---|---|
| Node / JS / TS | `package.json` (+ lockfile for real versions) |
| Python | `pyproject.toml`, `setup.py`, `requirements.txt`, `Pipfile` |
| Java | `pom.xml`, `build.gradle` |
| .NET | `.sln`, `.csproj` |
| VB6 | `.vbp` |
| Ruby | `Gemfile`, `.gemspec` |
| Go | `go.mod` |
| Rust | `Cargo.toml` |
| PHP | `composer.json` |
| C/C++ | `CMakeLists.txt`, `Makefile`, `configure.ac` |
| iOS/macOS | `.xcodeproj`/`project.pbxproj`, `Package.swift` |
| Android | `build.gradle`, `AndroidManifest.xml` |
| Infra/monorepo | `docker-compose.yml`, `Dockerfile`, workspace files |

If there's no manifest (loose scripts, ancient code), construct one: list every source file, its size, and your best guess at its role. That constructed inventory plays the same role.

From the manifest, produce a **structure map** in `docs/RECON.md`: entry point, every source file grouped by layer/area, every external dependency with version and status (current / dated / EOL). This map scopes the whole audit — it's how you decide what to read first, last, and never, and it means later analysis passes never have to re-derive the file list.

## Scoping prompts: map first, opine later

Whether you're briefing a sub-agent, another model, or structuring your own passes, the recon stage teaches a prompt discipline worth keeping:

- **Too vague** — "Tell me about this codebase." Produces a generic essay. No scope, no target output.
- **Too eager** — "Find all the bugs and rewrite it in modern X." Feels productive, is dangerous: it guesses, hallucinates, and skips to fixes for problems not yet found.
- **Just right** — "Start with `<manifest>`. Identify the entry point, every module, every dependency. Produce a tree, not a critique."

One bounded job at a time, with the output format named. An audit is a read pass; judgment comes after the map exists.

## RECON.md structure

```markdown
# Recon — <System Name>
## What it is            (one paragraph: purpose, users, scale)
## History & provenance  (authors, dates, license, maintenance status)
## Observed behavior     (from running/screenshots: screens, workflows, center of gravity)
## Structure map         (entry point; files by area; from the manifest)
## Dependencies          (name, version, status — flag EOL/deprecated)
## Free findings         (anything already visibly wrong — carried into the catalog later)
## Audit plan            (read cover-to-cover / skim / skip — with reasons)
```
