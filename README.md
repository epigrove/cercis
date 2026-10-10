# Cercis

**English** | [中文](README.zh-CN.md)

> May Cerces safeguard your thoughts.

**A folder *is* a database.** Cercis is a local-first recorder for research data:
your records live as plain CSV files inside your own folder, and the tool's only job is to make
that folder **reliable, traceable and readable by AI**.

No cloud, no proprietary format, no lock-in: without this software you can still open the files
in Excel, analyse them in R, or read them in any text editor.

## What it is trying to solve

| Everyday problem | What Cercis does |
|------------------|------------------|
| Data scattered across dozens of spreadsheets; nobody can tell who changed what | **Data is append-only**: a correction adds a correction record, the original value stays forever; every change carries a log entry (who / which device / when / why) |
| Paper notes → transcription → data entry; one mistake makes the whole table suspect | **Dry-run before import**; row-by-row checks for column names, required fields, primary keys and referential integrity; anything wrong means nothing is written |
| Endless "cleaning" before analysis; nobody can reproduce it | Plain text as the single source of truth + stable IDs + export notes; results can be traced back to a specific version of the data |
| Wanting AI to help with queries or scripts, but not daring to let it touch files | AI goes through **kernel commands + `--json`**: read-only by default, writes are logged, validation cannot be bypassed |
| If the tool dies, the data dies with it | Folder-as-database: CSV + images + Markdown, readable by anything |

## Repositories

| Repository | What it is | License |
|------------|------------|---------|
| **[cercis-core](https://github.com/epigrove/cercis-core)** | Kernel: data storage + AI-friendliness (Rust library + `cercis` CLI) | Apache-2.0 |
| **[cercis-kits-basic](https://github.com/epigrove/cercis-kits-basic)** | Basic kit: project wizard / data entry / import & export / health check | Apache-2.0 |
| **[cercis-kits-research](https://github.com/epigrove/cercis-kits-research)** | Research kit: field-trial template, terminology packs, SPSS/R export, print & labels, scribe | Apache-2.0 |
| **[cercis-ui](https://github.com/epigrove/cercis-ui)** | Plugin UI specification: one interface "blueprint", native on every platform | Apache-2.0 |
| **[cercis-apps](https://github.com/epigrove/cercis-apps)** | Apps and plugin host (desktop local-web UI first) | Apache-2.0 |
| **[cercis-examples](https://github.com/epigrove/cercis-examples)** | Synthetic example dataset + golden-reference acceptance (generator, end-to-end script, R re-computation) | Apache-2.0 |
| **[cercis-docs](https://github.com/epigrove/cercis-docs)** | **Reader documentation**: data format, templates, tutorials, example tour | CC BY 4.0 |

> The requirement document, decision log and other **internal development documents are not
> published** (version-controlled locally only). Reader-facing specifications, tutorials and the
> synthetic-example tour live in **cercis-docs**, and contain no real research data.
>
> Source comments sometimes carry markers like "§12.5.1" or "D47" — those are section and
> decision numbers of the internal requirement document. The public reader docs explain the
> concepts and the usage; you never need those numbers to use the software.

## What you install

| Install | You get | How |
|---------|---------|-----|
| **Cercis app** (kernel + basic kit) | Projects, data entry, import/export, health check, AI-friendliness (AGENTS.md / contract / agent skill) | **One installer, done** |
| Research kit | Field-trial template, terminology packs, SPSS/R export, print & labels, scribe | **Not installed by default**: the app ships a research marketplace — click install when you want it |
| Domain / third-party kits | Anything | Add a marketplace (a manifest in a git repository), or drop a plugin into the project's `plugins/` |

**Every plugin follows the same rules** — including the bundled ones: any plugin can be disabled,
blocked, individually authorised, or switched off in safe mode.

## Architecture: kernel + plugins

```
        ┌─────────────── app (per-platform UI + plugin host) ───────────────┐
        │                                                                   │
    basic kit (for everyone)             research kit (for research)
        │                                                                   │
        └──────────────────── kernel (storage · AI-friendliness) ──────────┘
```

- **The kernel knows no discipline**: it understands tables, records, fields, files and history.
  Meaning ("study / plot / observation") comes from kit templates — swap the template and it
  becomes something else entirely (inventory, equipment log, …).
- **There is exactly one write path**: plugins and AI both write through kernel commands —
  atomic writes, per-change snapshots, automatic change log.
- **The app is the plugin host**: it discovers plugins, grants the permissions they declare and
  calls them as subprocesses; a crashing plugin cannot take down the UI or the data.
- **Plugin UIs are written once**: a plugin hands over a UI "blueprint" and each platform renders
  it with its own native controls — same structure, platform-native look (see `cercis-ui`).
- **AI is a user, not a special channel**: the kernel writes `AGENTS.md`, a machine manifest and a
  contract for it, and can install an **agent skill** in one command (Codex / Claude Code
  understand it right away).

## Where the project stands

The kernel → UI → research loop is complete; the current phase is release preparation
(installers, documentation, name clearance).

| Step | Content | Status |
|------|---------|--------|
| S1–S3 | Kernel: storage, templates & queries, value types, AI-friendliness, plugin protocol | **Done** (193 kernel tests) |
| S4–S5 | Desktop local-web UI + the four basic-kit plugins (wizard / entry / transfer / health) | **Done** (34 tests, incl. cross-process end-to-end) |
| S6 | Golden-reference acceptance: multi-period, multi-factor synthetic data through the whole chain, re-computed in R | **Done** ([157 checks, 0 failures](https://github.com/epigrove/cercis-examples/blob/main/verify/REPORT.md)) |
| S7–S9 | Research kit: field-trial template & terminology packs, xlsx/SPSS/R import & export, field scribe (voice + handwriting, per-cell confidence) | **Done** (27 tests) |
| S9.5 | Dynamic composability: plugin registry with reverse rollback, declarative prerequisites, state fingerprint, key namespaces | **Done** |
| S10 | Plugin marketplace (kernel + app panel), install/uninstall/block, permission downgrades (table scope / read-only / domain allow-list) | **Done** |
| S11–S12 | Encrypted sharing with per-member access; pack / merge / share guard ("publishing carries no data") | **Done** |
| S13 | Desktop app polish: to-dos, history review & diff, large-table paging, transfer page, NAS & working copies, accounts and roles | **Done** (30 app tests + 24 automated UI checks) |
| S14 | Multi-device: LAN mode (tablets/phones use it in a browser), Linux & macOS installers, arm64/x86 cross-checks | **Partly done**: desktop usable; **native mobile clients still to come** |
| S15 | Release preparation: name clearance, pre-release scan (no real data), release notes, installers | **In progress** |
| — | Plugin UI specification (`cercis-ui`): one blueprint, native on every platform | **v1** (spec + blueprint format + validator + two official examples; renderers still to come) |

## Run it in three minutes (synthetic data only — no real research data involved)

Building from source expects **three repositories side by side** in the layout below
(the app's build looks for the kernel at `../kernel`, and the packaging scripts take plugins from
`kits/basic`; use these directory names):

```bash
mkdir -p cercis-workspace/kits && cd cercis-workspace
git clone https://github.com/epigrove/cercis-core.git        kernel
git clone https://github.com/epigrove/cercis-kits-basic.git  kits/basic
git clone https://github.com/epigrove/cercis-apps.git        apps

cd kernel && cargo build && cd ..                   # kernel + command line
bash apps/desktop/packaging/install-basic-kits.sh   # build & install the four basic plugins
cd apps/desktop && cargo run                        # opens http://127.0.0.1:8765
```

Use the **Project wizard** in the UI to create a project, and you can start recording, importing
CSV, exporting and running health checks.

Command line only:

```bash
cd ../..                         # back to cercis-workspace
./kernel/target/debug/cercis init ~/my-project --name "My project" --json
./kernel/target/debug/cercis skill ~/my-project --install codex   # teach Codex this project
```

Prefer not to compile? **macOS and Linux have packaging scripts** (under
`apps/desktop/packaging/`; one command produces an installer with the kernel and the basic kit
inside). The installers are **not code-signed**, so the first launch needs a manual approval step
described in the release notes.

## Hard rules (non-negotiable)

1. **Publishing carries no data**: no release artifact (repository, installer, share package)
   contains real research data.
2. **Never touch existing folders**: an existing folder can only be "mounted (read-only index)"
   or "moved (after explicit confirmation)".
3. **Data is append-only**: a correction is a new correction record; the original record stays
   forever.
4. **The kernel stays domain-neutral**: no discipline-specific kernel — research capability lives
   in kits.

## About the names

Three names, three jobs:

| Name | Used for | What it is |
|------|----------|------------|
| **Cercis** | Product name | The botanical genus of the redbud — a real plant name, unremarkable in professional contexts, and searchable (unlike game content with the same name) |
| **Cerces** | Tagline | "May Cerces safeguard your thoughts" — a nod to Cerces in *Honkai: Star Rail*; **unofficial, non-commercial** |
| **epigrove** | Organisation | Short for Epiphany Grove — easy to type, keeps the "grove" imagery |

In one sentence: **a neutral product name, the homage in the tagline, the affiliation in the
organisation name.** That way the reference is recognisable without turning a game's proper noun
into a brand with trademark risk (which is also why the project is not simply called `Epiphany` —
that name is taken on npm, PyPI and crates.io too; and why the organisation was shortened from
`epiphanygrove` to `epigrove`).

## License

Code is Apache-2.0; documentation, templates and the synthetic example dataset are CC BY 4.0.

---

<details>
<summary>Development workspace (this directory)</summary>

This directory is the development workspace; every subdirectory is an independent repository
matching the table above:

```
Cercis/
├── kernel/          → cercis-core
├── kits/basic/      → cercis-kits-basic
├── kits/research/   → cercis-kits-research
├── apps/            → cercis-apps
└── docs/            internal requirement documents (not published)
```

Publishing rule: no release artifact contains real research data; share packages exclude
`plugins/` and `access/` by default.
</details>
