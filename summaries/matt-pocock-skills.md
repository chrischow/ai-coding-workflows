# mattpocock/skills: A Complete AI Coding workflow, end-to-end - Matt Pocock

[Link to video](https://www.youtube.com/watch?v=M6mYodf0dJM)

Video tutorial for Matt Pocock's skills repo (162k stars, 7.5M downloads), walked through live on a real brownfield repo (AI Hero CLI). Focuses on the **main flow** only.

## Setup (one-time)

1. **Install the skills**: `npx skills@latest add mattpocock/skills` (requires Node.js). Select all official skills, pick your agent (Claude Code, Cursor, etc.), choose installation scope (project-level for teams, global for solo), and use **symlink**.
2. **Run setup**: `setup mattpocock` — configures:
   - **Issue tracker** (GitHub, Jira, Linear, or local markdown — just tell it which)
   - **Triage labels** (defaults are fine)
   - **Domain docs** (single-context vs multicontext; single is right for 99% of repos)
3. **Optional**: Use the `ask mattpocock` skill to get guidance on how to get started — it acts as a mentor version of Matt.

## The main workflow (idea → shipped code)

### 1. `grill-with-docs` (interview session)
Start with a vague idea — no need for detail. The skill interviews you with follow-up questions while exploring the codebase, and records what it learns in `context.md` and ADRs. It turns "I want to change X" into a crisp, defensible plan. A typical session is ~20 questions (his example was only 6). Run this in **auto mode**, not plan mode.

### 2. Fork in the road
- If the work fits in one context window → **skip to step 5** and run `/implement` directly.
- If it needs multiple sessions → continue:

### 3. `to-spec`
Compresses the entire conversation (e.g. 46K tokens) into a detailed spec document stored in the issue tracker. The spec describes the destination: problem statement, solution, user stories, implementation/testing decisions. Crucially, it lets you compare the final implementation against it later.

### 4. `to-tickets`
Breaks the spec into an implementation plan. Each ticket is sized to fit **one context window / "smart zone"** (~140K tokens — his heuristic for where LLM quality degrades). A real example: one big spec split into 11 short sub-issues, each one session's worth of work.

### 5. `/implement` (per ticket)
Clear the context window, then implement one ticket at a time. He recommends clearing between every ticket rather than batching them.

### 6. Code review (built into implement)
The implement skill automatically runs type checks, build, and verification, then spawns **sub-agent code reviewers** on two axes:
- Compare work against the original spec (catches forgotten ticket items)
- Check against coding standards (the repo's own docs, or classic Martin Fowler ones if none exist)

Sub-agents matter because main agents are bad at criticizing code they just wrote — a fresh context window reviews much better.

### 7. Commit
Done, and verified against spec.

## Key philosophy
- **Context window hygiene is essential**: budget tokens deliberately, treat 140K as the "smart zone" limit, and use specs/tickets to carry state across sessions so every session starts fresh.
- The spec = **where you're going**; tickets = **how you get there**; code review = **verifying you arrived**.

## Bonus notes
- `grill-with-docs` is **stateful** — it records context into `context.md` and ADRs progressively.
- The skills are designed to be **user-invoked with tiny descriptions** (~660 tokens total for 38 skills), so they don't bloat the agent's context.
- Works the same on greenfield (empty dir) and brownfield codebases.