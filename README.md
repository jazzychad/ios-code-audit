# iOS Code Audit Skill

An agent skill that produces a navigable `CODE_AUDIT.md` for an iOS or macOS Swift project — bugs, dead code, Swift concurrency issues, deprecated APIs, security, performance, and SwiftUI quality — with every finding cited to `path/to/file.swift:LINE`.

The skill is **READ-ONLY**: it never modifies your code. It produces one deliverable, `CODE_AUDIT.md`, written to the root of the audited repo.

---

## Who this is for

iOS and macOS Swift developers who want a one-shot, severity-graded audit of a codebase — the kind of review you'd commission before a refactor sprint, a major release, or an acquisition. The output is a single Markdown report you can hand to a teammate, file as issues, or work through over a few PRs.

---

## Install

### Option A — `npx skills add` (recommended)

```bash
npx skills add https://github.com/jazzychad/ios-code-audit --skill ios-code-audit
```

### Option B — manual clone

```bash
git clone https://github.com/jazzychad/ios-code-audit ~/.claude/skills/ios-code-audit
```

### Option C - local project install

In your local project directory:
```bash
mkdir -p .claude/skills && git clone https://github.com/jazzychad/ios-code-audit .claude/skills/ios-code-audit
```

Restart Claude Code (or run `/skills` to refresh) and the skill will appear as `ios-code-audit`.

---

## What you get

`CODE_AUDIT.md` is structured for triage — section numbering survives edits so you can refer to findings as "§5.4" in issues or PRs. Sections include:

- **Executive summary** — top 5–10 highest-impact findings, one line each
- **Quick wins** — ≤30-minute fixes worth knocking out first
- **Concurrency** — Swift 6 / strict-concurrency issues, anchored to actual compiler warnings
- **API modernity** — deprecations and replacements available at your deployment target
- **Bugs / logic errors** — force-unwraps, missing auth handling, retain cycles, race conditions
- **Security** — hardcoded secrets, token storage, debug-vs-prod URL gating
- **Performance** — per-frame allocations, `CIContext` lifecycle, redundant decodes, main-thread hot paths
- **SwiftUI / UI** — `@State`/`@Observable` misuse, view-body work, accessibility gaps
- **Dead code, duplication, refactor candidates** — including oversized files and unresolved `TODO`/`FIXME`
- **Cross-cutting recommendations** — patterns worth applying repo-wide
- **What was NOT audited** — explicit out-of-scope list
- **Verification** — exact lines proving each Critical / High claim

Operating principles enforced by the skill:

- **Read-only.** No code is modified.
- **Every Critical / High finding cites `file.swift:LINE`.** "Throughout the codebase" is rejected.
- **Severity is conservative.** Critical means crash, data loss, memory corruption, or security exposure — and every Critical claim is spot-verified by opening the cited file before it lands in the report.
- **Findings are grouped by root cause.** One missing `@MainActor` annotation that triggers seven warnings is one finding listing the seven sites, not seven findings.

---

## Dependencies

Set these up *before* running the skill.

### 1. Xcode native MCP server (recommended)

The skill uses Xcode's built-in MCP server to extract structured compiler warnings as canonical input for the concurrency and deprecation portions of the audit. Without it, the skill falls back to parsing `xcodebuild` output, which works but is noisier.

Enable it by following Apple's official guide:
👉 **<https://developer.apple.com/documentation/xcode/giving-external-agents-access-to-xcode>**

### 2. `swiftui-expert-skill` (required if your project uses SwiftUI)

Step 4 of the workflow delegates SwiftUI review to a dedicated expert skill. If your project has any SwiftUI views, install:

👉 **<https://github.com/AvdLee/SwiftUI-Agent-Skill>**

```bash
npx skills add https://github.com/AvdLee/SwiftUI-Agent-Skill --skill swiftui-expert-skill
```

If your project is pure UIKit / AppKit, this dependency is optional — the SwiftUI pass is skipped.

---

## Usage

Open Claude Code in the root of your iOS/macOS Swift project and ask naturally — the skill activates on phrases like:

- "Run a code audit"
- "Comprehensive review of this codebase"
- "Find tech debt"
- "What should I clean up?"

Or invoke it explicitly:

```
/ios-code-audit
```

When the skill finishes, the report is written to `<repo-root>/CODE_AUDIT.md`. The skill overwrites any existing `CODE_AUDIT.md` — if you want to keep prior audits, `git mv` them first.

If your project has a `CLAUDE.md` that lists "do not edit" directories (e.g., `Dead/`, `Archive/`), the skill reads and excludes them automatically.

---

## How it works

A 6-step workflow:

1. **Scope** — count Swift files / LOC, identify hot-spot files (largest, central state).
2. **Capture compiler warnings** — via the Xcode MCP server (preferred) or `xcodebuild` (fallback). This becomes canonical input for the concurrency audit, so the skill never has to guess whether a warning exists.
3. **Three parallel Explore agents** —
   - **Agent A:** Concurrency & API modernity (fed the Step 2 warnings)
   - **Agent B:** Dead code, duplication, refactor candidates
   - **Agent C:** Bugs, logic errors, security, performance
4. **SwiftUI expert pass** — invokes `swiftui-expert-skill` against the SwiftUI surfaces (skipped if pure UIKit/AppKit).
5. **Verify every Critical claim** — open the cited lines and confirm. Demote or drop anything that doesn't reproduce.
6. **Synthesize `CODE_AUDIT.md`** — using a stable section-numbered template so findings can be referenced by ID.

The full workflow, including the per-agent briefs and the report skeleton, lives in [`SKILL.md`](SKILL.md) and the [`references/`](references/) directory.

---

## What this skill does NOT cover

- **Algorithmic correctness of domain-specific code.** Obvious issues only.
- **Build settings, scheme configuration, Xcode project structure** beyond what's visible in shared schemes.
- **Third-party dependency internals.** SPM packages are treated as black boxes.
- **Deep test coverage assessment.** Quick scan only.
- **Localization wording.** The audit can flag untranslated strings but won't assess translation quality.
- **Instruments / runtime profiling.** The skill identifies *potential* hot paths but doesn't run traces. For that, use [`swiftui-expert-skill`](https://github.com/AvdLee/SwiftUI-Agent-Skill) and its trace tooling separately.

---

## Contributing

The three files `SKILL.md`, `references/agent-prompts.md`, and `references/report-template.md` form one pipeline — the per-finding template (`Location` / `What` / `Why` / `Action` / `Severity`) must stay identical across all three so synthesis stays mechanical. See [`CLAUDE.md`](CLAUDE.md) for editing rules.

Issues and PRs welcome.

---

## License

[MIT](LICENSE).
