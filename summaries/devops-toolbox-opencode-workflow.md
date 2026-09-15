# Summary: DevOps Toolbox — OpenCode Workflow (2026)

[Link to video](https://www.youtube.com/watch?v=UhRGHr7pgnU)

The video opens with Dario Amodei's prediction that AI would write 90% of developers' code within 3–6 months. The author initially dismissed it, but now admits he has a team of AI engineers building complicated tasks, built on **OpenCode**, which he calls the best coding agent in existence. This video is an update on how he uses OpenCode in 2026 for maintaining open-source projects and building a side gig.

## OpenCode background

- 100k+ stars, the de facto leader among coding agents; runs as a TUI, GUI, web app, and in the terminal.
- History/drama: started as a terminal AI agent by one developer; Dax and Adam joined and got opencode.ai. Charm offered all three positions (acquihire); the first dev agreed, Dax and Adam refused to keep OpenCode open without VC backing. The result: Charm's agent is now known as **Crush** (still containing Dax and Adam's commits).
- OpenCode is now under **Anomaly** (umbrella holding SST, OpenCode, OpenAuth, etc.), backed by Y Combinator, Max Levchin, and others — VC money, but with more dev control.
- Install: integrates with any model, fully configurable via `opencode.json` (project-specific, global, overridable with env vars). Recommends using **Westerm** or **Ghostty** as terminals. Other options: ACP, MCP, the `create` wizard for building agents step by step, headless server, web instance, usage stats.

## Models and Zen

- Picks Claude via **Zen** (OpenCode's router: put in a credit card once, get access to tested/verified models). Zen is not a profit center — they charge card fees at cost; auto-recharges $20 when balance drops below $5.
- **Big Pickle**: OpenCode's own free model (trains on your data) — used as his open-source bot.
- Other options mentioned: **Miniax** (Chinese LLM), Codex, Gemini, and even Kimi (someone claims Opus is slower than Kimi).

## Sessions

- Every session is stored; `/sessions` for history. Recently migrated from JSON to a **SQLite database**, making search/filter/resume much snappier. The DB is accessible via `opencode db` and stored at `~/.local/share/opencode`.
- A session holds full history, context window, active model, etc. He resumes sessions constantly, starting new ones only for brand-new unrelated features.

## Agents

- Two kinds: **primary** and **sub agents**. Primaries: **plan** (read/plan only, no execution) and **build**.
- He criticizes people building hype agents (builder, deep builder, marketing guy, salesperson) — usually empty files. He keeps it simple: plan, build, and one agent for complex/large features requiring structure.
- **Sub agents** run in the background for specific tasks: general (execute/explore) and explore (mostly reading). Invoke/tag them with `@` from the prompt or from primary agent instructions. Example: a code-review sub agent that cannot write but focuses on security and performance.
- Config lives in `~/.config/opencode` (directories: agent, command, skills) or `opencode.json`.
- He pairs the code-reviewer with **gh dash** (his earlier tool) via a keybinding that opens a new tmux window, creates a worktree with **Work Trunk**, and pops OpenCode with a prompt to review a PR.
- Can add more sub agents (e.g., a security engineer) and have the reviewer delegate security tasks to it.
- Build custom agents with `opencode agent create` — a wizard for description, interactive permissions, and choosing primary/sub agent/both. The result is a standalone markdown instruction file with examples, principles, guidelines, edge cases, and output expectations.
- Caveat: agents are just prompt injection — quality depends on you, and permissions don't fully stop an agent breaking script. He's been burned more than once. Use with caution.
- Why bother: (1) specific tasks reduce hallucinations and improve performance, (2) easier to track work across the parent agent thread.

## Security: JIT tokens

- Agents need credentials/API keys. Static keys in env vars/config files are a massive security hole (stolen machines, leaked keys in public repos, social engineering into CI — the key and its access live forever).
- Solution: **Dcope** integrated into OpenCode — **JIT (just-in-time) tokens**: the agent requests access, Dcope verifies identity, issues a short-lived token expiring after minutes. Recommends giving the security engineer sub agent access to Dcope.

## Skills

- Skills are on-demand prompt injection (not loaded into every context window like prompts or agent markdown files). They guide, connect to tools, and can come with scripts and code examples.
- `/skills` starts empty; he adds a skill that lets the agent locate and install skills autonomously.
- Options: **skills.sh** (tens of thousands of open-source skills, lots of junk), **SkillMP** by Manis (claims ~half a million skills — he's skeptical of the AI-generated exponential growth). Prefers following trustworthy sources like **Vercel** via `npx` and a wizard (model/provider specific, global vs project).
- Example: a DevOps engineer skill with a `SKILL.md` referencing files like GitHub Actions or Kubernetes; a Jira skill with endpoints, auth methods, and scripts. Skills save work by packaging others' guidelines and specialized instructions. The DevOps engineer skill found where he was overspending on his private account.

## Key bindings

- As a Neovim user he loves key bindings: a leader key (changed to Ctrl+O to avoid clashes), and **leader+e** opens the prompt in Neovim for proper multi-line editing — saving sends it back to the prompt box.

## Building a team

- He's constantly iterating: fixing agents and having them remember changes via prompts, skills, and settings.
- Naive first implementation of an autonomous team for very large features, built via the agent builder (not hand-editing long JSON):
  - **Team lead**: orchestrates and delegates, gathers requirements.
  - **Product manager**: read/explore/understand the user story only.
  - **Backend dev**, **tester/QA**, plus the **code reviewer**.
- Sees structured requests, deep-dive into sub agents (leader/side arrows), user flows drawn by the PM, backend code changes, real-time interaction (team lead asking PM to clarify requirements, reviewer and tester working alongside).
- Mentions **OpenAgents Control** (built on top of OpenCode; plan-first pipeline, zero-question human gateways) — he wants to see it in action before delegating.
- Verdict after a couple of weeks: ~95% okay, not perfect. The secret is constantly adjusting each sub agent's markdown file to align with his ways.
- Pros: better performance per component from separation. Cons: lots of tokens (big instructions; he lets the main builder agent handle work that isn't a huge new part of the app), and skimming sub agents to understand what went where isn't always pleasant. Keep it simple most of the time.

## Other ways to run OpenCode

- **opencode.nvim** plugin (invoke from within a running Neovim session) — used in the past, no longer comfortable.
- **Web interface**: serves on a local port, exposed with something like ngrok; he codes from his couch on an iPad, browsing recent sessions, code changes, and history.
- **GitHub integration**: `opencode github install` adds an action triggered from the platform; comment `/oc` or `/opencode` on PRs/issues and OpenCode adds an emoji while working, then posts a review with thoughts and issues, plus a link to the session.
- **GUI**: fully native app, still in beta but works well — like the web interface plus sound/OS notifications when an agent is waiting and an integrated terminal like an IDE.
- **Image sharing**: drag an image into the terminal (hence the Westerm/Ghostty recommendation) and OpenCode processes requirements, themes, styles, or any visual — making it a "solve anything, anywhere" agent.

## Conclusion

OpenCode is deeply integrated into everything he does — iPad, GitHub, laptop. He plugs **Work Trunk Next**, the project that makes Work Tree feel like branches, ideal for modern agent workflows (it pops OpenCode open for PRs and issues).
