# CODE_AUDIT.md Skeleton

Copy this structure into `<repo-root>/CODE_AUDIT.md` and fill in. Section numbering helps the user reference findings (e.g., "let's fix §5.1 first").

```markdown
# <App Name> Code Audit

Generated <YYYY-MM-DD>. Scope: ~<N> Swift files + <M> Metal/other kernels across <list of targets>. `<excluded dirs>` are excluded by request (intentional archive per `CLAUDE.md`).

Findings cite `path/to/file.swift:LINE` so you can jump straight to them in Xcode. Each item has a recommended action; no code changes were made.

---

## 1. Executive summary

Top items to address, in priority order:

1. **[Critical] <one-line title>** — `path:line`. <one-sentence consequence>.
2. **[Critical] <…>** — …
3. **[Critical/Security] <…>** — …
4. **[High] <…>** — …
5. **[High] <…>** — …
6. **[High] <…>** — …
7. **[High] <…>** — …
8. **[High] <…>** — …
9. **[High] <…>** — …
10. **[High] <…>** — …

---

## 2. Quick wins (≤30 min each)

These deliver outsized value relative to effort and have no architectural ripples.

- **<action>** — `path:line`. <one-line rationale>.
- …

---

## 3. Concurrency

### 3.1 <Title — usually the root cause, e.g. "ClassFoo should be @MainActor">
- **Location:** `path:line` (and list of affected call sites if grouped)
- **What:** <observed>
- **Why:** <impact>
- **Action:** <recommendation; no code>
- **Severity:** Critical | High | Medium | Low

### 3.2 <…>

---

## 4. API modernity

(Deprecations, iOS-target+ replacements available.)

### 4.1 <…>

---

## 5. Bugs / logic errors

### 5.1 <…>

---

## 6. Security

### 6.1 <…>

---

## 7. Performance

### 7.1 <…>

---

## 8. SwiftUI / UI

(If the project uses SwiftUI; otherwise omit or replace with "UIKit / AppKit".)

### 8.1 <…>

---

## 9. Dead code / duplication / refactor

### 9.1 Files to delete outright
- `path/to/file.swift` — <reason>.
- …
- **Severity:** High (clean up; ~<N> LOC removed)

### 9.2 <duplicated helper name>
- **Locations:** `path1:line`, `path2:line`
- **Action:** Extract to <shared location>.
- **Severity:** High | Medium

### 9.3 <…>

### 9.N Oversized files (>500 LOC)
- **`path:LOC`** — <split proposal>. Severity: High | Medium.
- …

### 9.N+1 Unresolved TODOs / FIXMEs
- `path:line` — <quoted comment>.
- …
- **Severity:** Medium

### 9.N+2 Magic constants
- <constant> at `path:line`, `path:line` — name it once.
- …
- **Severity:** Low

---

## 10. Cross-cutting recommendations

Patterns worth applying repo-wide rather than one finding at a time:

1. **<pattern>**. <one-paragraph rationale tying together multiple findings>.
2. **<pattern>**. …
3. …

---

## 11. What was NOT audited

- `<excluded dirs>` (intentional archive).
- Algorithmic correctness of <domain-specific code, e.g. Metal kernels, ML models>.
- Build settings / Xcode project structure beyond shared schemes.
- Third-party SPM/CocoaPods dependency internals (<list>).
- Tests under `<test target paths>` — quick scan only; no deep coverage review.
- The `<extension target>/` targets got light coverage. Their entitlements files were not opened; verify they match the App Group identifier used elsewhere.
- StoreKit 2 product configuration in `<.storekit file>` — file structure only, not whether each product matches App Store Connect.
- Localization and string catalogs — not assessed.
- <Anything else specifically out of scope>.

---

## 12. Verification

Spot-check pattern: open Xcode, command-click the `path:line` reference in this report — it should land on the cited line. Each Critical / High finding has an exact line range, not "scattered throughout."

For the Critical items, here are the lines that prove the claim:

- **§<N.M>** — open `path`, lines `start-end`. <one-sentence verification — what to look at>.
- **§<N.M>** — open `path`, line `N`. <…>.
- **§<N.M>** — …

If any finding doesn't reproduce when you visit the line, ping me with the specific reference and I'll re-investigate.
```

## Notes on filling the template

- **Numbered subsections (3.1, 3.2, …) survive edits.** When you delete a finding, leave the number — note "REMOVED: <reason>" — so the user's notes/issues that reference "§5.4" don't shift.
- **Executive summary is curated, not algorithmic.** Pick the 10 highest-impact items across all categories; some Critical items may not appear here if they're already obvious from another finding.
- **Cross-cutting recommendations** is where you connect dots. If three separate findings all point at "CaptureState should be @MainActor," call that out as one repo-wide change.
- **The "What was NOT audited" section is mandatory.** It sets the user's expectations and prevents "you missed X" follow-ups.
- **Verification section is the trust contract.** For each Critical, name the literal lines the reader can open to confirm the claim. If you can't, the finding probably isn't Critical.
