# 1000 Hours of Claude Code — Coding Sloth

[Link to video](https://www.youtube.com/watch?v=YAsxyoTWFDA)

Coding Sloth shares everything learned from ~1000 hours with Claude Code: the tier-ranked features, favourite skills and MCPs, an alternative-tool roundup, and token-saving strategies for surviving the $20 plan (he hits usage limits after 1–2 prompts and refuses to pay $100/month).

## The pain point
- **Usage limits suck.** On $20/month you can hit the limit after a prompt or two and wait 3 hours — a big reason people get bad results (they can't use it enough).
- Goal of the video: make your tokens last via **context management**, since usage is billed by tokens (messages, file reads, responses all cost), not by prompts.

## Feature tier rankings

| Feature | Tier | Notes |
|---|---|---|
| `/init` + CLAUDE.md | C–D (file moved up after months of use) | Analyzes codebase → writes `CLAUDE.md` as permanent project memory. Beef: every other agent uses `AGENTS.md`; Claude doesn't support it (though you can import one). New experimental version **interviews you** and recommends skills/hooks. His file: project description, current status, coding style (incl. a rule against AI's ugly all-caps in user-facing UI), working philosophy, PR language. Not make-or-break — skills give similar results. |
| **Skills** (SKILL.md files) | **A (S if used smartly)** | Markdown guides for repeatable workflows; auto-triggered or explicitly invoked. Defaults (code review, security review) = B/A. Finding them: **skills.sh**, GitHub repos. Favourites: **Matt Pocock's** skills (grill-with-docs to refine requirements; improved-codebase-architecture), **Cursors' Thermonuclear Code Quality Review** (simplifies AI code, kills the slop — pairs great with improved-codebase-architecture), **Shaden's improve skill** (audits codebase, writes implementation plans for other agents). Best practice: **don't spam skills** — stick to one coherent set matching your style. |
| **Plan mode** (Shift+Tab) | **S** | Reads code thoroughly and writes a full plan before touching anything; you approve or deny. Easier to catch a mistake in a plan than in 3,000 lines across 12 files. Use for big tasks only — wasteful for typos, renames, small design changes. |
| **Verification** | **S / triple S** | Give Claude a way to check its own work; it has no idea if its output is correct. Methods: **tests written first, then implement** (writing tests after = tests that pass its own code = cheating); test only the important things (smarter models over-test and bloat the codebase); type checkers + linters; **screenshot & browser testing** for front-end — Claude is blind, so it opens the app, clicks around and sees via screenshots (now built-in, used to require MCPs). Not just an AI thing — good software engineering fundamentals. |
| **MCPs** | **A** | Give Claude other tools: GitHub, database, Slack, analytics, deployments, browser. Use for: fetching design inspiration, database seeding (fake data), researching docs, browser testing. Only needed when Claude must interact with something **outside the codebase** — skills handle patterns/best practices. Same rule: don't install a bunch. |
| `/voice` | feature | Type `/voice` and talk — speech becomes the prompt. Easier to be descriptive, think out loud; faster than typing. |
| `/btw` (ask mid-task) | C | Ask a question without interrupting Claude or touching conversation history; Claude can't read files here, uses history only. |
| Teleport / Remote Control | A (if you go outside) / C–D (for him) | Move sessions between phone, web, terminal — start ideas in the mobile app, continue in the terminal. |
| Shell mode (`!` prefix) | C (underrated) | Back to a normal terminal, but Claude also sees the output. Convenient shortcut. |
| `/radio` | F for productivity, S for vibes | Opens Claude FM — lofi music while you code. |
| `/context` | B | Shows context usage. Run before big tasks. |
| `/compact` | C auto / B manual | Summarizes conversation history. If Claude compacts itself mid-task → **start a new session** (quality drops, "dementia"). If you run it yourself, tell it exactly what to keep. |
| `/loop` | (not ranked — he's just a fan) | AI cron job: give a time interval + task, it repeats. His automations: **GitHub issue implementation** (daily picks an open issue, works on it, PR waiting when he returns), **security & bug sweep** (scans codebase, files findings as GitHub issues), **feature brainstorming** (reads codebase/PRs/issues, proposes features). |
| `/task` (goals) | B on low plans, A on high/unlimited | Give Claude a goal ("make every test pass, no type errors") and it works until done or needs help. |
| Sub-agents | A (S on higher plans) | Mini-Claudes spawned for one job (researcher, reviewer, debugger), each with its **own context window**, reporting back a summary to the main "daddy" agent. Easiest creation: just ask. Caveat: each is a full parallel conversation — a small multi-agent setup can eat your entire $20-plan limit before the task finishes. |
| Work trees | A | Check out multiple branches of one repo, each copied to its own folder — perfect for parallel AI development. His workflow: **every chat is a work tree**; agents stay isolated. Native support in Claude Code. |

## Context management (token-saving playbook)
- Claude has a **"dumb zone"**: more context = forgetting, self-contradiction, dumb mistakes. Defaults to a 1M-token window, but that only *extends* the dumb zone — performance dips above ~100–200K tokens and usage limits get cooked.
- **1. New session per task.** Every medium/big task burns ≥50K tokens, easily 100K+ with research/skills/MCPs; stacked tasks get worse output and drain limits fast.
- **2. Kill research tangents.** Every file/website Claude reads is tokens. If it starts exploring things it doesn't need, tell it to stop immediately — and name the specific files/sources to use.
- **3. Be stupidly specific in prompts.** Vague prompts make Claude read everything to guess; this matters most for technical decisions.

## Wider point: learn to code
Mocks the "for loop" prompting trend — engineers "reinventing" the same thing, proof you still need to understand code to use AI well. Don't be the programmer who hits a usage limit and can't work on their own project. Use AI to make you smarter: have Claude **explain every change** instead of blindly accepting. (Sponsor: Brilliant's new AI tutor "Cooji".)

## Alternatives to Claude Code
- **Codex** (OpenAI) — his personal favourite; generous usage limits.
- **Open Code** — open-source Claude Code clone; use any model.
- **Pi** — minimalist; build your own workflow from scratch.
- **Cursor** — IDE + agent combo; any model (likes Grok & Composer: fast, cheap).
- **VS Code + GitHub Copilot** — the OG.
- **T3 Code** — open-source control plane for coding agents; use every subscription (Claude, Codex, Open Code, Cursor) in one app; its UI beats Claude Code's if you have a subscription.

## Key takeaways
- Only do what's necessary — the big pattern across skills, MCPs and context.
- Plan with a smart/expensive model, implement with a cheaper-flex model (e.g., Opus plans → Sonnet implements).
- Price-check everything: the $20 plan forces discipline — context hygiene, no tangents, fresh sessions, deliberate verification.