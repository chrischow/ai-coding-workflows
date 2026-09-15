# Kun Chen — Agentic Engineering Workflow

[Link to video](https://www.youtube.com/watch?v=iQyg-KypKAA)

> Kun Chen is a former principal engineer (Meta, Microsoft, Bing, Windows) who now builds frontier coding agents at Atlassian and ships 40–50 production PRs per day with AI. This summary captures his full workflow, framed as captaining a ship with a crew of agents.

## The philosophy

- **Shift from sailor to captain**: Don't review every diff or test everything yourself. Set up culture, processes, and tools so agents do the heavy lifting; you apply judgment at the start (planning) and end (quality bar).
- **Everything is agent-agnostic**: The agent harness landscape changes fast, so the workflow (memory files, skills, tools) works regardless of which harness/model you use.
- **Todo**: When doing bug fixes, reproduce the bug end-to-end as close to real user experience as possible — unit tests aren't a sufficient product guard.

## 1. Working in the terminal

- **Why terminal**: (a) hands never leave the keyboard → stays in flow, no context switching; (b) the exact same workflow works everywhere, even on a phone.
- **WezTerm**: cross-platform, highly customizable via Lua config (dynamic logic, hot reload).
- **tmux**: terminal multiplexer — split panes (agent in one, editor in another, shell for yourself), tabs for parallel agent sessions, persistent sessions you can detach from and reattach (even from another device like a phone).
- **Neovim**: modern vim — modal editing, relative line numbers, muscle-memory navigation, plugins (e.g. fuzzy file find, ripgrep search). Admitted learning curve but "flying like a bird" once comfortable.

## 2. Agent harnesses (crewmates)

- **Claude Code**: only practical choice for Anthropic subscription; most sensible out-of-the-box defaults; occasionally buggy, less customizable.
- **Codex CLI**: written in Rust, smoother feel; open source so it can debug its own source; fewer bells and whistles.
- **Pi coding agent**: minimal and highly extensible — good for tinkering and making it your own.
- **OpenCode**: smooth TUI, model-agnostic, most complete out-of-the-box feature set.

He demos with Claude Code but keeps everything harness-agnostic.

## 3. Onboarding crewmates: memory files

- **Global memory file** (symlinked so all harnesses share one file): kept **minimal (~27 lines)** because it loads into the system prompt of *every* session — bloat silently burns tokens. Contains personal preferences:
  - Never use em dashes (AI defaults to them; feels robotic).
  - When making technical decisions, don't give too much weight to development cost. (Frontier models estimate dev time in days/weeks based on human data, but can actually build a playable 3D FPS in minutes. Their cost bias makes them pick cheap, low-quality, unscalable solutions.)
  - Bug fixes: always start by reproducing end-to-end as a user would experience it.
- **Project-level memory file** (e.g. `.claude/agents.md`, also symlinked): project context, repo layout, terminology, how key components work, e2e testing instructions, conventions. Built **organically**: every time the agent does something wrong, correct it and ask it to store the learning in this file — the crew gets smarter over time. No fancy memory system needed; a markdown file suffices.

## 4. Skills (progressive disclosure)

- Conditionally-needed info (like e2e testing instructions, only useful when making changes) should move from memory files into **skills** to save tokens.
- **Progressive disclosure**: only a tiny description loads into the system prompt; the full content is read only when the agent decides to use the skill.
- Use `npx skills` (by Vercel) to install/manage skills across agents; install the **Skill Creator** skill (by Anthropic) so agents can create new skills themselves.
- **Warning against random Internet skills**: they can run arbitrary things on your machine (exfiltrating API keys/credentials) and popularity ≠ quality. Example: a 177k-star skills repo evaluated on Program Bench made the agent *worse* (5% more tokens, worse results). Don't install skills that claim magic improvements without rigorous published evidence.

## 5. Voice input (how you talk to crewmates)

- **Talk, don't type**: Stanford research shows talking ≈3x faster than typing.
- **Open Super Whisper**: free, open source, local Whisper transcription — great quality; customisable system prompt lets you seed project vocabulary (e.g. project names) for accurate transcription.
- Fall back to typing only for URLs/paths (don't read a URL out loud).

## 6. Tool designs matter: agent ergonomics (Axi)

- External tools (like GitHub) hugely affect agent performance. Benchmark: for identical tasks, the **GitHub MCP server cost ~3x tokens and >2x latency vs the GitHub CLI** — you're wasting time and money.
- **Axi**: design standards he authored — 10 principles for making tools treat agents as first-class citizens (e.g. token-efficient output formats save ~40% tokens vs JSON). He built Axi for GitHub and Chrome DevTools, benchmarked against alternatives (fewer turns, fewer tokens for the same task). Catalog at axi.sh.

## 7. Planning with interactive artifacts (Lavish)

- Default planning output is a wall of text you must read; hard to react to specific parts. **Lavish** (rich editor as an HTML artifact) fixes this:
  - Agent builds the plan as an **HTML artifact using the project's own design system**, so options are visual and consistent with the real app.
  - You can **annotate/comment on specific parts** and **click to make decisions** — feedback flows back to the agent without returning to the terminal.
  - Enabled via an installed skill so it kicks in automatically for planning tasks; say "don't use lavish" to see the old flow.

## 8. Validating changes: "No Mistakes" pipeline

Instead of reviewing diffs (a bottleneck and no fun), think of yourself as an **engineering manager** — create good culture/processes, let the team carry them out.

**No Mistakes** (free, open source) orchestrates the agent through a pipeline taking first-pass code to a clean PR:
1. Create branch + commit; run in an **isolated worktree** (nothing affects your current repo).
2. Understand the real intent from the agent session.
3. Rebase onto latest main, resolve conflicts up front.
4. **Adversarial review in a fresh context window** — most problems caught here; obvious ones self-corrected, ambiguous ones escalated to you.
5. **E2E test against original intent**, recording **evidence** (screenshot, video demo, logs) you can inspect to confirm it works.
6. Documentation pass + linting check, then push branch and raise the PR.
7. Continues **babysitting the PR until merged** (new merge conflicts, CI failures).

Trigger by command or just the "no mistakes" skill. He never stares at the screen — he spins up other tasks and returns when all checks pass. A **risk assessment** tells him how much time to spend reviewing: low-risk changes skip the diff entirely; only risky ones get deep review.

## 9. Long-running tasks: "Goodnight, Have Fun"

- Free, open source tool for long unassisted runs: give an **objective + stop condition**, it loops until done.
- Demo: "Pretend you're a 7-year-old using the app; find a usability problem that would confuse a kid, fix it, repeat."
- Monitor token usage / iteration count / commits; wake up and cherry-pick the commits you want.
- Better than Codex/Claude Code's `/goal`: you can set **token caps, iteration caps, or precise stop conditions** (avoid burning your weekly quota overnight).
- Best for verifiable objectives (reduce page load time, improve e2e coverage, hit a metric via hypothesis experimentation) or objectives where you trust agent judgment.

## 10. Parallel worktrees: Treehouse

- Multiple agents in one directory conflict; `git worktree` solves it but creates mental overhead (remembering which worktree was for what, manual cleanup).
- **Treehouse** automates it: run it to drop into a fresh worktree, keep spawning more, `treehouse status` lists them, closing the tab frees it, and it **reuses idle worktrees** instead of creating new ones.
- Demo: three parallel Claude Code sessions (voice-button UX fix, screenshot attachment action, bot activity status bar fix), each in its own worktree; switch between tmux tabs with keyboard shortcuts, monitoring status via the tab bar.

## 11. First Mate (the commander)

- Free, open source project — clone it and run an agent in that repo; it becomes your **first mate** managing all the crewmates.
- Talk to it in natural language; it spins up tmux tabs, calls Treehouse for worktrees, launches agents, runs No Mistakes, and prepares PRs — the juggling is its job, not yours.
- Demo: "Add an update CLI command to three repos" → recognised as three parallel tasks, dispatched to parallel agents, each validated and PR-ready. Also pulls recent open issues, discusses which are actionable, then fixes the chosen one. Watching it context-switch is "oddly satisfying" because that's what you'd otherwise do.
- The bottleneck shifts to **you running out of ideas** — a good sign.

## 12. The captain's mindset

- With a first mate managing execution, spend your energy on **what matters**: talking to users, understanding the competitive landscape, and crafting a good direction/treasure map for the crew.
- That transition — from sailor to captain — is the end goal.

## Key takeaways

- Keep memory files lean and load conditionally-needed knowledge into skills.
- Design/choose tools with agent ergonomics in mind (CLI over MCP; token-efficient formats).
- Plan visually in Lavish, let No Mistakes validate and babysit PRs, use Goodnight-Have-Fun for overnight loops, Treehouse for parallel worktrees, and a First Mate to orchestrate it all.
- Free up the middle of every task so you can run more work in parallel; your time sits at the beginning (planning) and end (quality bar).