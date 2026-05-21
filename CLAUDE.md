# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A single Claude Code **skill** (`ios-code-audit`) that produces a `CODE_AUDIT.md` report for an iOS/macOS Swift project. There is no application code, build system, or test suite here — the "deliverables" are the prompts that drive an audit run against some *other* Swift codebase.

When the user invokes this skill, Claude is operating in the user's iOS project directory, *not* this one. Edits in this repo only change future audit behavior.

## File layout

- `SKILL.md` — the skill definition and the 6-step workflow Claude follows when invoked. This is the source of truth; everything else is referenced from it.
- `references/agent-prompts.md` — copy-paste briefs for the three parallel Explore agents (concurrency/API modernity, dead code/duplication, bugs/security/performance). Contains a placeholder table at the bottom showing how each `{PLACEHOLDER}` is discovered.
- `references/report-template.md` — the `CODE_AUDIT.md` skeleton with section numbering. Section numbers (3.1, 3.2, …) are stable across edits by design so user notes referencing "§5.4" don't shift.

The three files form a single pipeline: `SKILL.md` drives the workflow, the agent prompts produce raw findings, and the report template structures them.

## Editing rules specific to this skill

- **Severity discipline is the load-bearing invariant.** `SKILL.md` enforces a four-level guide (Critical/High/Medium/Low) and *requires* spot-verification of every Critical claim before it lands in the report. Don't loosen this — it's a direct response to past runs where agents overstated severity.
- **The three agents must run in parallel.** `agent-prompts.md` repeats this: "send all three in a single message with multiple `Agent` tool calls." If you edit the workflow, preserve the parallel-launch requirement.
- **Per-finding template is identical across all three agent briefs.** If you change the format (Location/What/Why/Action/Severity), update it in all three briefs *and* the report template in lockstep — synthesis depends on it being mechanical.
- **`Dead/` (or any explicitly-archived directory) is excluded by default.** The skill expects to read the audited project's own `CLAUDE.md` to discover excluded paths.

## What the skill explicitly does not cover

Listed in `SKILL.md` under "What this skill does NOT cover" — Metal kernel correctness, Xcode project structure, third-party dependencies, deep test coverage, localization wording, Instruments profiling. Keep that list in sync if scope changes.
