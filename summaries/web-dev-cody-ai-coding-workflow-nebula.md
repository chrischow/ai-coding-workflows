# Building Nebula: An Agentic Development Workflow in a Language I Don't Know

[Link to video](https://www.youtube.com/watch?v=Tbp6M_hXNfw)

Summary of an AI coding workflow walkthrough from someone who built **Nebula** — an 85,000+ line Rust TUI application for orchestrating AI coding agents — despite having **zero Rust experience** (13 years of web dev background in TypeScript/Node.js). The project is an experiment in how far an AI-assisted workflow can push a codebase in an unfamiliar language. He never read the Rust code; nearly every feature was implemented by prompting an AI agent.

## Product engineering strategy

- **Optimize real workflows, don't add features blindly.** He used existing agent tools (Cursor, Claude Code, Conductor, etc.) and found they didn't fit his actual workflow: jumping between many projects, kicking off **isolated work trees** (clone-like branches) per task, and having agents open PRs for team review. Nebula exists to close that gap.
- **Strict anti-bloat discipline.** Anything that feels "extra" gets left out. Every feature must improve his actual day-job workflow, not satisfy a random request from someone online (he'd rather they fork the repo than accumulate toggles for everyone).
- **Always ask: what can I remove?** Features get simplified or deleted — e.g. debating whether the enter-key "harness picker" should be removed in favor of the quick-prompt modal (press `P`, `Tab` to switch harness), and the idea of agent presets in the quick prompt, kept in the codebase for weeks before deciding.

## Feedback loop (weekly cadence)

1. **Use the app for a week** — at work and on side projects.
2. **Accumulate a list of changes** — feature requests (e.g. archive/unarchive sessions, list archived sessions), bug fixes, performance tweaks.
3. **Kick off the changes concurrently against `main`** — he'll run ~8–10 agent sessions at once. Workable because the tasks are isolated (nobody touches the same panels/files), assuming the codebase is architected well.
4. **Review every session by hand** — restart the app, verify the feature works and is actually valuable, archive completed ones, delete leftover work trees/terminals.
5. **Ship a release**, then repeat.
6. **Experimental flags** for on-the-fence features — let them sit for a week or two before deciding to fully adopt or delete. Example: the "hide root work trees" setting, since he rarely works off `main` on professional projects.

## Tactics for prompting

- **No plan mode at all** — just high-level prompts, always via the default agent (Fable 5 Extra High; ~80–90% of features built on Fable 5.0). The model is capable enough to one-shot most changes.
- **Prompt like you're describing the screen to someone sitting next to you** — be visual and cover the six Ws:
  - **Who** — e.g. "as a teacher user" when the system has role-based auth.
  - **What** — the desired behavior.
  - **When** — conditions/triggers (e.g. "when I press enter to create a work tree, it takes ~5 seconds to appear").
  - **Where** — the exact location, and importantly **use the same keywords that are in the codebase** ("work tree list" vs "work tree panel") so the agent learns the mapping.
  - **Why** — the rationale, so the agent understands the user's context and intent.
  - **How** — technical guidance when you have it (e.g. his performance fix prompt explicitly asked for **optimistic updates** — the UI should list the work tree instantly, then kick off creation in the background, handle failure with a rollback/deletion).
- **Bad output usually means a bad prompt.** When an agent implements something wrong, go back to the original prompt and find what wasn't described correctly. Accurate user stories are the key to good output.
- **Vague prompts → vague LLM output.** GitHub issues / user stories should carry enough description and acceptance criteria that the agent knows when it's done.
- **No skills or CLAUDE.md files in this repo** — he just prompts the model directly. Intelligent models need less hand-holding (the Claude Code author reportedly deleted 80% of his CLAUDE.md).
- **Skills are still useful for process things** — he does keep skills for **writing PR descriptions** (table of contents; he mostly reads the Risks section — new attack surfaces for the local/VM deploy — plus mermaid diagrams, before/after screenshots, and a high-level description; he no longer reads the deep code-dive section since the model writes Rust fine) and for **reviewing incoming PRs**.

## What the app supports (features that emerged from the loop)

- Search files, fuzzy jump, jump between projects, work trees per task, concurrent agent sessions with status updates (running / finished / awaiting feedback), PR review with direct `git diff` without leaving the app, GitHub issue integration (to stop tabbing to Chrome), terminals, session archive, hotkey hints in the UI (left arrow/L/H for focus, J/K to move, `/` fuzzy file jump, Shift+F find in files, G for git diff) — added because he plans to stream/record and viewers need to learn the hotkeys.

## Key takeaways

- **Concurrency works when architecture is clean** — many agents can edit different parts simultaneously if services/modules are small and isolated; a small codebase (~80K lines) lets the model hold the whole service in context and be accurate.
- **Human verification is the gate** — every batch is manually reviewed before release; ~5–10% of features get reverted after he decides he doesn't like them.
- **Build for the workflow you actually live** — features like the focused-panel tint and the quit confirmation came from watching real users (e.g. transparent-background setting broke, so it went back behind a setting defaulting to on) and from UX accidents (accidentally pressing `Q` closing the app when the wrong panel was focused).
- **Bloat is the enemy** — add nothing you don't need today; keep asking what can be removed to keep the product simple.