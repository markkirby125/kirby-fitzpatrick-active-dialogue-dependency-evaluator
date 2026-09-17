# Active Dialogue Dependency Evaluator — Technical Operational Dispatcher

**Framework Author**: William Fitzpatrick (*Writer Science*)  
**Source Lecture**: [I learned a system for writing effortlessly](https://www.youtube.com/watch?v=_ribgj7VIGc)  
**Parent Collection**: [Master Collection](../../kirby-fitzpatrick-writers-collection/SKILL.md) | [Global Help](../../../kirby-help/SKILL.md)  

---

## 1. Cognitive Foundation: The Dependency Is an Interlocutor, Not a Utility

Fitzpatrick's system for effortless writing is not a filing method. It is a three-tier pipeline whose middle tier is the one everybody skips:

1. **Fleeting notes** — frictionless capture. Raw, undated-in-argument, unverified.
2. **Dialogic notes** — the tier that does the work. You do not re-read the capture; you **interrogate it**. You put a question to your own material, let it answer, and — this is the load-bearing clause — **let the answer change the plan.** The vault becomes a windmill instead of a filing cabinet only when the material is made to talk back.
3. **Permanent notes** — the durable claim that outlives the conversation that produced it.

The engineering translation is exact, because dependency selection is structurally the same problem with the tiers relabeled. A third-party package is not a utility function you call. It is a **standing, uninvited co-author of your build**: its maintainers may rewrite the API, drop a version, add a `postinstall` hook, change the license, abandon the project, or hand the namespace to someone with different intentions — none of which requires your permission. Every `npm install`, `cargo add`, and `pip install` opens a dialogue with a party that can change the terms unilaterally, after the fact, at a moment of their choosing.

Almost every team evaluates at **tier 1 and calls it tier 2**. Download counts, GitHub stars, "everyone uses it", "the model suggested it", "it's the ecosystem standard" — these are *fleeting impressions*. They are legitimate triggers to open a dialogue. They are not evidence inside one. A tally is a collection pass, and a collection pass produces a verdict without ever asking the package what it costs.

**The active dialogue is a two-sided interrogation, asked in a fixed order, with both answers grounded in names and numbers:**

- **Q1 — What concrete bottleneck does this solve?** Concrete means: a *named* call site, a *named* pain (defect, hours burned, lines hand-rolled, p99 latency, build minutes), *measured*, and **reachable today**.
- **Q2 — What security and maintenance surface does this add?** Surface means an *enumerated* graph: transitive weight, install-time and build-time code execution, native toolchain coupling, `unsafe`/FFI, advisory history, license of the whole tree, maintainer bus factor, feature-flag bloat, error semantics, telemetry, and — the one nobody computes — **exit cost**.

Two properties make it a *dialogue* rather than a review theater:

- **Bidirectionality.** You ask the package what it will cost you, and you ask yourself whether you can afford the answer. If Q2 is incapable of killing the dependency, no dialogue occurred; you were justifying a decision that was already made, in the shape of a table.
- **Order.** Q1 first, always. Surface evaluated in isolation from a bottleneck is how a team talks itself into "it's widely used, so it's fine" — a sentence with a Q2 adjective and no Q1 noun.

Fitzpatrick's boundary governs the automation: **the agent works on the evaluation, not instead of it.** The agent runs the tree, diffs the lockfile, greps for `build.rs` and `postinstall`, counts `unsafe`, pulls advisories, and diffs the graph. It does not decide. It enumerates, grounds, and states — explicitly — which surface classes it could not enumerate. The human picks between admit, vend, reject, and defer.

```text
      Fitzpatrick's note pipeline                  Dependency admission pipeline
  ┌───────────────────────────────────┐       ┌────────────────────────────────────────┐
  │ 1 · FLEETING   capture, no friction│ ────▶ │ 1 · IMPRESSION  stars, downloads,      │
  │   raw, unverified, provisional     │       │   "the LLM picked it", "we always use" │
  └───────────────────────────────────┘       └────────────────────────────────────────┘
                    │                                           │
        ...the pass that is                         ...the pass that is
        almost always skipped ...                   almost always skipped ...
                    ▼                                           ▼
  ┌───────────────────────────────────┐       ┌────────────────────────────────────────┐
  │ 2 · DIALOGIC   interrogate: ask    │ ────▶ │ 2 · ACTIVE DIALOGUE  two questions,    │
  │   the question, let the material   │       │   in order, both answered with names   │
  │   answer, let the answer change    │       │   and numbers, answer recorded         │
  │   the plan                         │       │                                        │
  └───────────────────────────────────┘       └────────────────────────────────────────┘
                    │                                           │
                    ▼                                           ▼
  ┌───────────────────────────────────┐       ┌────────────────────────────────────────┐
  │ 3 · PERMANENT  durable claim that  │ ────▶ │ 3 · LEDGER ENTRY  DEP-YYYY-NNN with    │
  │   outlives the conversation        │       │   the delta, an owner, an exit cost,   │
  │                                    │       │   and the re-open triggers             │
  └───────────────────────────────────┘       └────────────────────────────────────────┘
```

**The failure this skill exists to prevent — a monologue wearing a review's clothes:**

```text
BEFORE — THE MONOLOGUE (one column filled, and it is the easy one)

  "Should we add `chrono`?"   ◀── the only question actually asked
              │
              ▼
  ┌───────────────────────────────────────────────────────────────┐
  │ BURDEN REMOVED   "date handling is annoying to write by hand" │  ← a feeling, not a
  │                                                               │    bottleneck: no call
  ├───────────────────────────────────────────────────────────────┤    site, no defect, no
  │ SURFACE ADDED    (blank)                                      │    measurement
  ├───────────────────────────────────────────────────────────────┤
  │ VERDICT          "it's fine, everyone uses it"                │  ← tier 1 evidence
  └───────────────────────────────────────────────────────────────┘    promoted to a tier 3
                                                                       decision
  ─────────────────────────────────────────────────────────────────────────────────────
  The package was never asked what it costs. Nothing in the exchange could have changed
  the outcome, so the outcome was never evaluated — it was announced.
```

```text
AFTER — THE ACTIVE DIALOGUE (both columns populated, in the same units)

  Q1  WHAT CONCRETE BOTTLENECK DOES THIS SOLVE?
      ├─ call sites     4  (billing/period.rs:88, :140, reports/rollup.rs:31, :77)
      ├─ bottleneck     hand-rolled DST / leap-year arithmetic, written twice, divergent
      ├─ measured       61 lines hand-rolled → 0; 2 prior defects
      │                 (INC-4201: off-by-one invoice day, 3 h to diagnose)
      └─ reachable      yes — both call sites ship in this release

  Q2  WHAT SECURITY / MAINTENANCE SURFACE DOES THIS ADD?
      ├─ graph              cargo tree --edges normal → +0 direct, +6 transitive
      ├─ install/build exec no build.rs, no proc-macro in tree            ✓
      ├─ native toolchain   none (time-only features, no localtime/TZ db) ✓
      ├─ unsafe in tree     0 at this version                             ✓
      ├─ advisories         RUSTSEC: 0 open, 1 historical (2020, fixed)   ✓
      ├─ license            MIT / Apache-2.0, allowlist clean             ✓
      ├─ bus factor         4 maintainers, release cadence ~6 weeks,
      │                     MSRV advertised and tested in CI
      ├─ feature flags      default-features = false; `clock` only
      │                     (drops `serde`, `std`, bundled TZ database)
      └─ exit cost          4 call sites, no type in the public API,
                            nothing persisted → ~2 h                         ✓

  DELTA   (removes a recurring defect class + 61 lines)  vs  (6 crates, 2 h exit)
  VERDICT ADMIT   →  RECORD DEP-2026-041 · owner @billing-lead
                     re-open on: MSRV bump · advisory · graph drift in Cargo.lock
```

**Five measures make the dialogue auditable instead of aspirational:**

- **Bottleneck Grounding (BG)** = Q1 claims carrying both a call site and a measurement ÷ total Q1 claims. **Target 1.0.** A Q1 claim without a call site is a hypothesis; without a measurement it is an opinion.
- **Surface Enumeration Ratio (SER)** = surface classes cited with a command or artifact ÷ surface classes that apply to the candidate. **Target 1.0.** The denominator is named before enumeration starts, so "we checked everything" is a countable claim rather than a mood.
- **Transitive Fan-Out (TFO)** = nodes added to the dependency graph by this change, measured (`cargo tree`, `npm ls --all`, `pip install --dry-run --report`). **Record the integer.** The adjective *small* is not a measurement, and the transitive tree is where the surface actually lives.
- **Exit Cost (EC)** = engineer-hours to remove the dependency, including call-site rewrites, migrations for any persisted or wire format, and public-API leakage. **Target:** EC is written at admission and is less than the bottleneck's measured cost per quarter. A dependency whose exit cost is unmeasured has not been evaluated.
- **Delta Sign** = (burden removed − burden added) **in comparable units**. If one side is "61 lines and two defects" and the other is "6 crates and 2 hours", convert both to recurring cost per quarter or the entry is marked **not yet evaluable** — never "looks fine".

---

## 2. Core Transformation Protocols

### 2.1 The ordered protocol (numbered rules, non-negotiable)

1. **Ask Q1 before Q2.** State the concrete bottleneck first, grounded in a call site and a number. Evaluating surface before value guarantees the surface scan becomes a rationalization of the value you assumed.
2. **Pre-commit to a killer.** Before enumerating, write down at least one surface class that would make you reject or vendor the dependency. If no such class exists, you are not evaluating — say so in the record and route the decision to whoever wants the dependency.
3. **Both columns in the same units.** An unmeasured burden on either side yields the verdict `NOT YET EVALUABLE`. That is a legitimate output; "seems fine" is not.
4. **Concreteness test for Q1.** Named call site + named pain + measurement + reachable today. Four of four, or it is a fledgling impression.
5. **Concreteness test for Q2.** Every claimed surface item cites the command or artifact that produced it: tree output, lockfile diff, advisory database query, license scan, `build.rs`/`postinstall` inspection, unsafety count.
6. **Interrogate before the lockfile changes.** The dialogue is pre-merge. Reading the source after `cargo add` is archaeology, not evaluation.
7. **Prefer the narrower surface.** One dependency per job; non-default features unless justified; no default features you cannot name a use for.
8. **Every admission carries an exit.** EC measured at admission, plus the trigger list that re-opens the dialogue: new major version, maintainer or ownership change, published advisory, MSRV / `requires-python` bump, license change, transitive drift in the lockfile.
9. **Quarantine the impressions.** Stars, download counts, "standard in the ecosystem", and model suggestions are admissible as *triggers* and are labelled as such; they are never evidence inside the dialogue.
10. **The agent enumerates; the human decides.** The agent must additionally declare which surface classes it could not enumerate and why. An enumeration with no stated blind spot is an enumeration that has not been inspected.
11. **Write the permanent note.** Every admitted dependency gets a ledger entry recording the delta, the owner, the exit cost, and the triggers. Approval given verbally does not exist for the next reader — Fitzpatrick's rule about unwritten decisions applies at full strength.

### 2.2 Ecosystem enumeration commands (the dialogue's raw material)

| Surface class | npm | cargo | pip |
|---|---|---|---|
| Transitive graph | `npm ls --all --json` | `cargo tree --edges normal`; `cargo tree -d` for duplicates | `pip install --dry-run --report - > g.json`; `pipdeptree --warn silence` |
| Install/build-time code execution | `npm view <pkg> scripts`; watch `postinstall` / `preinstall` / `prepare` in CI | any `build.rs`, `proc-macro`, or crate-level macro expansion in tree | sdist vs wheel: inspect the PEP 517 backend; `setup.py` executes at build time |
| Native / toolchain coupling | `node-gyp` in the tree, platform-specific binaries | `*-sys` crates, `cc` build-deps, musl/glibc | `--only-binary=:all:` feasibility; manylinux tags |
| Advisory history | `npm audit --json` | `cargo deny check advisories` (RUSTSEC) | `pip-audit` |
| License of the whole tree | `license-checker --production` | `cargo deny check licenses` | `pip-licenses` |
| Unsafe / memory-safety surface | native addons, WASM boundary | `cargo geiger`, count `unsafe` | C extensions, `ctypes` |
| Provenance & bus factor | publisher identity, 2FA, provenance attestation | crates.io ownership, signing | PyPI trusted publishing |
| Feature bloat | `peerDependencies` ranges, duplicate majors | `--no-default-features`, `cargo build --timings` | optional extras |
| Exit cost | call-site count, public-type leakage | same + type in the public API | same + type in persisted schema |
| Hermetic build feasibility | registry reachable at install time | `Cargo.lock` + vendoring / private mirror | hash-pinned wheels, `--require-hashes` |

### 2.3 Admission classes and gate effects

| Class | Condition | Gate effect | Required record |
|---|---|---|---|
| **Clear** | Bottleneck measured, surface fully enumerated, delta positive in comparable units | Merge allowed | Ledger entry: delta, owner, EC, triggers |
| **Conditional** | Delta positive, but one named surface item requires mitigation (feature flags off, `--ignore-scripts`, CI audit gate, version pin) | Merge allowed **only with** the mitigation in the same PR | Mitigation, its owner, and the check that enforces it |
| **Vend** | Bottleneck real, surface unacceptable (install-time execution, unmaintained security-critical path, no namespace control) | Merge allowed with extracted code, not with the dependency | Attribution + license, extraction diff, upgrade path |
| **Reject** | Bottleneck unmeasured or solved by the standard library / 30 local lines; or the surface holds an unremediable class (copyleft contamination in a proprietary binary, no declared license, abandoned crypto) | Merge blocked | Which question failed and what would change it |
| **Defer** | Bottleneck real but not reachable today (no call site yet) | Not admitted; parked | Trigger that promotes it to Clear, plus owner |
| **Not yet evaluable** | Graph, license, or bottleneck could not be enumerated | Merge blocked on the missing enumeration only | The missing artifact and who can produce it |

### 2.4 Transformation table: anti-patterns and clean replacements

| Anti-Pattern | What it hides | Clean Replacement |
|---|---|---|
| "It's the ecosystem standard" | Q1 never grounded: no call site, no bottleneck | Named call sites + measured pain; the standard-library or hand-written alternative priced in the same units |
| "12M weekly downloads" | Tier-1 impression promoted to tier-3 evidence | Downloads recorded as the *trigger* that opened the dialogue; the delta decides |
| "The model suggested this crate" | No interrogation occurred at all | The same two-column evaluation, run by the human who owns the call site |
| `npm i left-pad` for one function | TFO of 1 package for 12 lines of logic | Local helper, or the platform's own facility |
| `cargo add` and read it later | Execution-time and build-time surface never inspected | `cargo tree` + `build.rs` grep + advisory query *before* the lockfile change |
| "It's a small library, so it's safe" | Direct size ≠ transitive graph; `build.rs` code size is zero | TFO measured; install/build execution checked explicitly |
| "We can always swap it out later" | Exit cost never measured, so removal becomes a quarter | EC written at admission; unmeasured EC means defer |
| "The maintainer is active" | Bus factor, funding, and advisory response time unexamined | Named maintainers, cadence, and last-advisory time-to-fix, recorded with sources |
| "It's MIT" | Transitive tree carries a copyleft dependency | License scan over the whole resolved graph, against an allowlist |
| "It passed `npm audit`" | No published advisory ≠ no surface; unaudited code has no advisories | Input class named (parser? TLS? auth? crypto?) + install-time execution enumerated |
| "Everyone on the team already knows it" | In-group familiarity substituting for verification | API surface actually used vs total exported surface; CI/compile delta measured |
| Review comment: "why do we need this package?" | Neither column is populated; the author cannot act | The two-column dialogue with numbers, or `NOT YET EVALUABLE` plus the missing artifact |
| Shim wrapper added "for vendor neutrality" | Decoration masquerading as an exit plan | Abstract only when a second implementation exists; otherwise record EC and skip the shim |
| Vendoring a whole crate for one helper | Replacement cost never compared to extraction cost | Extract the single function with attribution, or write it |
| Approving in chat, merging the lockfile diff | The permanent-note tier was never written | `DEP-YYYY-NNN` entry in the PR body; verbal approval is not a record |

### 2.5 Failure diagnostics

| Symptom observed | Dialogue diagnosis | Fix |
|---|---|---|
| One-line feature PR touches 400 lines of lockfile | Graph drift admitted without interrogation (TFO never computed) | Pin, enforce `--frozen`/`--locked`/`--require-hashes`, and route dependency bumps through their own reviewable PR with the ledger entry |
| Cold CI build time doubles after "a tiny utility" | Surface not enumerated: `build.rs`, native toolchain, or default features pulling TLS | Measure `cargo build --timings` / `npm ci` delta pre-merge; disable default features |
| A CVE drops and nobody knows who owns the response | Ledger entry has no owner or patch SLA | Owner + patch SLA written at admission; advisory alerts wired to that owner |
| The same helper is hand-rolled separately in three services | Dialogue ran in one direction, inside one team | Cross-team dialogue, or promote shared internal code and record it |
| A 400-export package is used for one function | Cost/benefit never computed in equal units | Narrower crate, feature-gated subset, or local implementation |
| Extraction PR takes three sprints | EC was never measured at admission | EC probe written at admission time; treat unmeasured EC as Defer |
| `postinstall` script appears in CI logs for a "pure JS" package | Install-time execution surface unenumerated | `--ignore-scripts` where feasible, or Vend / Reject |
| Build fails in airgapped / offline CI | Registry fetched at build time; hermetically unsound | Vendored mirror or hash-pinned offline artifacts; record the constraint in the ledger |
| Two majors of the same library resolved side by side | Duplicate graph not enumerated | Deduplicate or reject the second provider; `cargo tree -d` / `npm ls` duplication check in CI |
| Removal ticket balloons to twelve files | The dependency's types leaked into the public API and persisted format | Exit plan written at admission; keep third-party types behind a boundary type |

**Related dispatchers.** Keep the human's ground truth untouched while the agent enumerates, with [Read-Only Vault Isolation](../../kirby-fitzpatrick-read-only-vault-isolation/SKILL.md); draw the graph before judging any node with [3D Architectural Grounding](../../kirby-fitzpatrick-3d-architectural-grounding/SKILL.md) and the [Codebase Navigation Router](../../kirby-fitzpatrick-codebase-navigation-router/SKILL.md); refuse speculative abstractions that the dialogue does not justify with the **Anti-Coaster Lens Filter**; capture the raw failure evidence that opens a dialogue under **Fleeting Bug Ingestion**; audit the branches the dependency's own API implies with the [Semantic Gap Hunter](../../kirby-fitzpatrick-semantic-gap-hunter/SKILL.md); bind every claim to an artifact a reviewer can open with the [Cold Reader PR Auditor](../../kirby-fitzpatrick-cold-reader-pr-auditor/SKILL.md).

---

## 3. Engineering Application Scenarios

### 3.1 Code Reviews — Interrogating a Diff That Adds a Dependency

The reviewer's advantage is the un-briefed reader's advantage: they do not carry the author's mental model, so the empty Q2 column is visible to them. A dependency review is therefore not "does this look reasonable" — it is **a check that the dialogue actually happened, and that both columns are populated from artifacts.**

**Review procedure:**

1. **Locate the admission.** Find the manifest and lockfile hunks. If the lockfile moved without a ledger entry, the dialogue is undocumented — that is the finding, before any judgement about the package.
2. **Read Q1 and test it.** Does it name call sites in *this* diff or in already-merged code? Is the bottleneck measured, or asserted? Is the pain reachable today, or hypothetical?
3. **Re-derive Q2 independently.** Run the tree yourself, diff the graph, inspect `build.rs` / `postinstall` / PEP 517 backend, query advisories, scan licenses. The author's surface list is a claim to be reproduced, not accepted.
4. **Check the feature configuration.** Are default features enabled by convenience? Is `serde`, bundled TLS, or a TZ database now in the build because nobody passed a flag?
5. **Check the exit.** Are third-party types in the public API or in a persisted/wire format? If so, EC just went up and the entry must say so.
6. **Classify and gate.** Clear / Conditional / Vend / Reject / Defer / Not yet evaluable — with the mitigation named for Conditional and the missing artifact named for the last two.

**Before — the comment that names nothing:**

> Do we really need this package? It feels like a lot of dependencies for one small function.

Diagnostics: *"a lot"* is an unmeasured TFO; *"one small function"* is an unverified claim about API usage; the comment asserts a defect it never demonstrates, converting a mechanical enumeration into a taste argument that the deadline will win.

**After — the grounded two-column comment:**

```markdown
**Dependency dialogue — `chrono` (Q1 grounded, Q2 has one open item)**

Q1 · Bottleneck solved (verified)
- Call sites: `billing/period.rs:88`, `:140`, `reports/rollup.rs:31`, `:77` — all in this diff.
- Bottleneck: DST/leap-year arithmetic hand-rolled twice and already divergent; INC-4201
  (off-by-one invoice day, 3 h triage) traces to `rollup.rs:44`.
- Measured: 61 hand-rolled lines removed; one recurring defect class removed.
- Reachable: yes, both call sites ship in this release.

Q2 · Surface added (independently reproduced)
- `cargo tree --edges normal` → +0 direct, +6 transitive (I re-ran it; matches the PR body).
- No `build.rs` and no proc-macro in the resolved tree. No native deps. 0 `unsafe` at 1.14.2.
- RUSTSEC: 0 open advisories; 1 historical (2020, fixed in 2020-04).
- License scan clean against the allowlist (MIT / Apache-2.0 across the tree).
- **Open item → Conditional:** `default-features` is on, which pulls `serde` + `std` + bundled
  TZ data. Requested change: `default-features = false, features = ["clock"]`. This is the one
  item that would flip this to Vendor — we do not need `serde` here and do not want the TZ db
  shipped.
- Exit cost: 4 call sites, no third-party type in the public API, nothing persisted → ~2 h.

**Gate:** Conditional on the feature-flag change and the `DEP-2026-041` ledger entry landing in
this PR. Owner: author. Re-open triggers recorded in the entry.
```

**Acceptance rule.** A dependency review comment is valid only if it names the call sites, the measured bottleneck, the reproduced surface items (with the command that produced each), and either a verdict or the single missing artifact that blocks one. A comment that cannot do this is a hypothesis with a request attached — never a gate.

### 3.2 PR Descriptions — The Permanent-Note Tier (`DEP-YYYY-NNN`)

Fitzpatrick's third tier exists because a decision that is not written down does not exist for the next reader. A dependency added in a PR whose body says *"adds date handling"* produces exactly that failure: the next engineer inherits a package, an owner-free graph, and no memory of why. The **permanent note** is a ledger block in the PR body, and it is the artifact the audit trail is built from.

```markdown
## Dependency ledger —

### DEP-2026-041 · `chrono` 1.14.2 (direct)
**Q1 · Bottleneck solved.** DST/leap-year invoice arithmetic; 61 hand-rolled lines removed across
`billing/period.rs:88,140` and `reports/rollup.rs:31,77`. Prior defects attributable: INC-4201,
INC-4330. Measured: 2 defects / 6 weeks before, 0 after (30-day observation).

**Q2 · Surface added.**

| Class | Finding | Source of the finding |
|---|---|---|
| Transitive fan-out | +6 crates (was 0 direct, 118 total → 124) | `cargo tree --edges normal | wc -l`, re-runnable |
| Build/install execution | none — no `build.rs`, no proc-macro | `cargo tree` + grep of resolved sources |
| Native toolchain | none | `cargo deny check bans` |
| Unsafe in tree | 0 at 1.14.2 | `cargo geiger` |
| Advisories | 0 open; 1 historical (2020, fixed) | `cargo deny check advisories`, 2026-04-02 |
| License | MIT / Apache-2.0, allowlist clean | `cargo deny check licenses` |
| Bus factor | 4 maintainers, cadence ~6 weeks, MSRV advertised | upstream repo, 2026-04-02 |
| Runtime footprint | +11 kB binary, no cold-path allocations | `cargo bloat --release` |
| Exit cost | ~2 h (4 call sites, no public-API leakage) | counted, not estimated |

**Delta.** Removes one recurring defect class and 61 lines of divergent arithmetic; adds 6 crates
and a 2-hour exit. Signed positive in recurring-cost terms.

**Configuration commitment.** `default-features = false, features = ["clock"]` — no `serde`, no
bundled TZ database. A CI check fails the build if the resolved feature set grows.

**Impressions (triggers only, not evidence).** "Recommended in the internal channel"; trending on
`r/rust`. Recorded here so they are not mistaken later for justification.

**Re-open triggers.** New major version · ownership or maintainer change · any published advisory ·
MSRV bump past the workspace floor · any change to the resolved feature set or transitive count.

**Verdict: ADMIT.** Owner: @billing-lead. Recorded 2026-04-02.
```

**Binding rules that make the entry auditable:**

- **Both columns with sources.** Every surface row cites the command that produced it. An entry whose surface column is a memory is not a record; it is a recollection, which is what the ledger exists to replace.
- **Impressions are quarantined, not deleted.** Recording *"a colleague recommended it"* in an explicitly labelled tier-1 section preserves useful signal while preventing it from migrating into the justification column on the next read.
- **The delta, not just the verdict.** A verdict with no delta cannot be re-audited, because nobody can tell what changed when the triggers fire.
- **Owner and date on every entry.** An unowned admitted dependency is an unpatched dependency the first time an advisory lands.
- **Feature configuration is a commitment.** The pinned feature set is part of the record, and a CI check enforces it, because transitive feature bloat is the most common way an admitted surface silently grows.
- **EC is written as a number.** Estimates in prose ("should be quick to remove") are how extraction becomes a quarter.

### 3.3 Architecture RFCs / ADRs — Adopt vs Vendor vs Write, in Equal Units

An ADR is a model of a system that will outlive the conversation that produced it, and its purpose here is narrower and sharper than a status update: it forces the three candidates — **adopt the dependency, vendor the minimal code, or write it locally** — into a single comparison table in comparable units. The dialogue is completed by pricing the alternative you rejected, which is the step that a one-column review structurally cannot perform.

```markdown
# ADR-027 — Invoice period arithmetic: adopt `chrono`, vendor, or write

## Context
Invoice period arithmetic (DST boundaries, leap years, month-end clamping) is currently
hand-rolled twice and divergently (`billing/period.rs`, `reports/rollup.rs`). Two incidents in six
weeks (INC-4201, INC-4330). The workspace floor
is Rust 1.78; MSRV policy is "support rustc current − 2". Build must remain hermetic (offline CI
mirror; no registry fetch at build time).

## Options, priced in the same units (recurring cost per quarter)

| Dimension | Adopt `chrono` | Vendor the minimal subset | Write it locally |
|---|---|---|---|
| Bottleneck solved | Yes — removes both hand-rolled copies | Yes | Yes |
| Transitive fan-out | +6 crates | +0 | +0 |
| Surface added | Advisory surface, feature-set drift, MSRV coupling, 1 upstream owner group | Attribution + license file + a vendored patch queue we must maintain | Design, review, and ongoing correctness ownership for DST/leap rules |
| Hermetic CI | Registry mirror required at lock time | None | None |
| Recurring cost / quarter | ~0.5 h (upgrade + advisory triage) | ~3 h (patch rebases, correctness review) | ~6 h (initial) + ~2 h (correctness hardening) |
| Exit cost | ~2 h | ~2 h | n/a |
| Public-API leakage | None if wrapped behind `Period` | None | None |

## Decision
Adopt `chrono` 1.14.2 with `default-features = false, features = ["clock"]`, wrapped behind the
existing `Period` newtype so no third-party type enters the public API or the persisted invoice
format. Rationale: the vendored and local options both price above the adoption cost per quarter,
and the correct-handling burden (DST rule change, historical offset data) is precisely the work a
maintained dependency amortizes.

## Reversal / exit plan
- `Period` is the only type crossing the module boundary; a local implementation reuses the same
  signature. Exit cost measured at admission: ~2 h, 4 call sites.
- Reverses if: transitively-resolved feature set grows without a corresponding need; an unpatched
  advisory exceeds the 14-day policy; upstream goes 12 months without a release.

## Re-open triggers
New major version · ownership change · published advisory · MSRV floor violation · graph drift ·
license change · the resolved feature set changing.

## Consequences
- Cost of inaction: 2 defects / 6 weeks, one recurring defect class, two divergent implementations.
- Accepted residual risk: +6 transitive crates on the invoice path; mitigated by CI graph-diff gate
  and `cargo deny` license + advisory checks on every lockfile change.
- Unverified: the observability of `Period` failures under `clock`-only features — owner: author,
  probe: injected-malformed-timestamp fixture before GA.
```

**Rules that make the three-way comparison meaningful:**

- **Price the rejected alternatives.** A comparison with an empty "vendor" and "write" column is a monologue that has learned the format of a table. The dialogue is only active if at least one alternative was costed and could have won.
- **Same units, or no table.** Hours per quarter, crates added, call sites affected. Mixed units let the preferred option win by choosing a friendlier axis.
- **Boundary discipline is part of the decision.** Third-party types in the public API or the persisted format convert a 2-hour exit into a migration; require a boundary type as part of the adopt decision, not as follow-up work.
- **Reversal conditions are written as observable triggers.** "If it becomes a problem" is not a trigger; a 14-day advisory SLA, a 12-month release gap, and a graph-diff gate are.
- **State what could not be enumerated.** The agent's blind spots are recorded alongside its findings; an evaluation with no declared blind spot has not been inspected.
- **An ADR that cannot show the delta is a status update.** "The team agreed to use `chrono`" describes a meeting; the table above describes a decision that a stranger can re-audit in a year.

---

## 4. Verification Checklist

- [ ] **Both columns populated and grounded.** Q1 names call sites, a measured bottleneck, and reachability today; Q2 cites a command or artifact for every surface class it claims, and both sides are expressed in the same units so the delta is computable rather than felt. No row rests on a recollection.
- [ ] **The dialogue could have changed the outcome.** At least one surface class was named in advance as a killer, and the enumerated surface was actually allowed to move the verdict to Admit, Conditional, Vend, Reject, Defer, or Not yet evaluable — no verdict was announced before Q2 existed.
- [ ] **The graph and its executables were measured, not assumed.** Transitive fan-out counted, duplicate majors checked, install/build-time execution inspected (`postinstall`/`prepare`, `build.rs`, PEP 517 backend), native toolchain and `unsafe` counted, advisories and the whole-tree license resolved, and the feature set pinned as a commitment with a CI check.
- [ ] **The permanent note exists.** A `DEP-YYYY-NNN` entry carries the delta, the named owner, the numeric exit cost, the quarantined tier-1 impressions, and the re-open triggers; approval given only in chat does not satisfy this, and nothing was admitted on a verbal verdict.
- [ ] **The human decided, and the boundary held.** The agent enumerated the surface and explicitly declared what it could not enumerate; no agent-authored verdict was merged as ground truth; impressions stayed labelled as triggers; and the exit plan is written and priced before the dependency lands rather than reconstructed after it becomes a problem.