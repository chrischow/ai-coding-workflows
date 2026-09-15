# My AI Coding Workflow That Actually Works - Chris Titus

[Link to video](https://www.youtube.com/watch?v=wcRR5P0S2Us)

Summary of the "My AI Workflow" video. A workflow designed for **larger, complex software** — not one-prompt-and-loop-and-spit-out-code usage (a prior "1.5 billion tokens" video concluded that's a bad idea). The central theme: **manual gates are essential**. Requires basic Git knowledge first.

## Core philosophy

The magic happens at the manual gates — not in the code generation. The iterative review loop (review → address feedback → test → re-review) is where the value is, and where edge cases get caught that would otherwise surface as GitHub issues later.

## The three pillars

1. **Agents** — machine-wide (global) configuration files
2. **Spec** — repository-specific
3. **Roadmap** — repository-specific

The files are **plain text** and should be edited to match your own rules (delete/disagree with rules freely). Assemble them from leaders in the space (e.g. Karpathy's agents file, modified to taste). Rules include: skip flattery, filler, ceremonial openings, emojis; only touch what the task requires.

## Phase-by-phase workflow

### 1. Build guardrails first
Before any real coding, have the LLM create a test suite and validate it. Get the scaffolding/shell in place before building. Skipping straight to "plan mode → implement plan" is why people get poor results.

### 2. Spec (repo-specific)
Contents: problem, intended users, required behavior, UX, architecture, major components, security/privacy requirements, **versioning**, and project-specific gotchas.

- **Versioning matters**: LLMs pick old versions with security problems — pin current versions (e.g. Node LTS).
- Encode project-specific quirks in the spec (e.g. his QuickShell/QML linting needed types specified, or lint errors would break the build).

### 3. Testing & linting
Validate with linters (e.g. markdownlint CLI) and a test suite. **Acceptance criteria must always include a test or validation** so the AI knows when it succeeds.

### 4. Roadmap
Break work into phases; each phase is "complete when X happens," followed by a **giant review** per phase.

### 5. Real project demo
He walks through a real change: *"add audio percent next to audio icon"* in his DWM desktop environment. The agent used his QuickShell skill, made the change, updated the live install, restarted, and opened a **PR** — that PR is the **manual gate**.

### 6. Review phase
After each phase: unit testing plus **independent AI reviewers** (CodeRabbit, Codex review, Claude review) — must be *outside* the implementing agent. He also runs local CodeRabbit CLI passes *before* opening the PR for bigger changes.

### 7. Manual gate at the merge
He doesn't fully trust AI — he reads the code and AI review comments himself, deciding what to accept/reject, then tells the agent to "address and resolve PR review feedback, commit and push."

### 8. Security
Always enable **Dependabot** and **CodeQL** code scanning on all projects to catch old versions and leaked keys. It's free.

### 9. Continuous integration
Automated tests run per distribution (e.g. Arch, Debian, RHEL smoke tests via Docker); wait for green.

### 10. Final merge gate
Review code + reviews, address and document changes, then merge and delete the branch.

## Key takeaways

- **Slow is smooth, smooth is fast** — wait for tests to be green and reviews to complete (Codex can take 20–30 min on big PRs; chunk work into smaller PRs).
- This is a **compute/cost-heavy workflow** — heavy development plus review needs the $100–$200/month tiers; reviews consume most of the tokens (e.g. 25% of max plan by day one after a reset).
- The real value isn't the code itself (his example was 11 lines) — it's the **iterative review** catching edge cases like "hide the percent when audio is unavailable."
- A good workflow beats the next model — no subscription fixes a bad development workflow. This is good software engineering *with or without* an LLM.
- Terminology is still evolving (Claude: "workflows", Codex: "skills") — don't treat any of it as gospel.