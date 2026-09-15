# Cole Medin — The Dead Simple Framework for AI Coding — Workflow Summary

[Link to video](https://www.youtube.com/watch?v=goOZSXmrYQ4)

## The thesis

A lot of AI coding frameworks on GitHub are over-engineered multi-agent systems. What most people need is something **simple, personal, and evolvable over time** — "dead simple" enough that you spend more time coding than building your workflow. The framework shown is what Cole uses every time he starts a **greenfield project** (brownfield/existing codebases are a separate video). All principles are universal — they apply regardless of which coding agent you use (demoed with Claude Code).

## The four golden rules

1. **Context management** — context is your most precious resource; protect the main agent's context at all costs.
2. **Commandify everything** — if you do something more than twice (e.g., create a PRD, write rules, commit), turn it into a reusable slash command/skill.
3. **Commit history = long-term memory** — standardized commit messages let the agent (via the prime command) read the git log to see how the codebase evolved and decide what comes next.
4. **System evolution mindset** — on every bug or misalignment, don't just fix the code; evolve the AI layer so it doesn't happen again.

## The AI layer

The AI layer = every asset in the codebase created to serve as context for the coding agent:

- **PRD** — *what* we're going to build
- **Global rules** (AGENTS.md) — *how* we're going to build
- **Commands** — reusable workflows for the agent (e.g., `/prime`, `/create-prd`, `/pl-feature`, `/execute`, `/commit`)
- **Sub-agents** — delegate research
- **Reference / on-demand context** (e.g., `api.md`, `components.md`, `styles.md`)

The recommended starting point is a small *generic* set of commands you bring into every project, then evolve and specialize them per project — this customizability is why Cole prefers this over heavyweight frameworks like BMAD or GitHub Spec Kit, which are powerful but hard to make your own.

## Step 0: Creating the PRD

1. **Start unstructured.** Have a casual conversation with the agent about the idea, tech stack, architecture. Use **speech-to-text** (Cole uses AquaVoice; free/open-source alternatives: Whisper Flow, Epicenter Whisper) to brain-dump raw ideas at high speed.
2. **Spin off sub-agents for research** (web research on best practices, architecture recommendations, tech stack validation).
3. **Ask the agent to come back with questions.** The #1 goal of planning is **reducing assumptions** — "one line of bad code is one line of bad code; one line of a bad plan is ~100 lines of bad code; one bad line in a PRD is ~1000 lines." Having the agent interrogate you (ideally via multiple-choice question tools) surfaces edge cases you'd never think of. Answer all 20–25 questions; every answer removes an assumption. Where an option doesn't fit, type your own answer and even have a mini-conversation about it.
4. **Run `/create-prd`** — the command bakes in the exact PRD structure so the process is repeatable. Result: MVP scope (all the Q&A baked in, since the conversation is just disposable context — **the PRD is the only thing that survives**), out-of-scope, full directory structure, and **phases of work** (each phase = one granular PIV loop).
5. The PRD is **never sent to the agent to implement wholesale** — it's read phase-by-phase.

## Step 1: Global rules + on-demand context

- **Global rules (AGENTS.md)** — constraints and conventions the agent should *always* see: tech stack, commands to run/test, project structure index, architecture, code patterns/naming conventions, testing & logging strategy. Keep it concise (their example: ~233 lines). Generated with a `/create-rules` command that reads the PRD, does web research on testing/logging best practices, and follows a template. This is where more technical users should invest time aligning patterns with their preferences.
- **On-demand context (reference folder)** — bigger, task-specific docs loaded *only when relevant* ("working on front-end components → read components.md"; "working on API routes → read api.md"), referenced from AGENTS.md. This is **progressive disclosure**: layers of context the agent discovers as needed instead of dumping everything into every conversation. (Claude Code skills are an alternative mechanism.)

## Step 2: The `/prime` command

Run at the **start of every new session** — a guided process that catches the agent up on the codebase so it can recommend the next feature:

- Read documentation (e.g., "read the Drizzle migrations so you understand the database schema")
- Explore the structure, optionally via sub-agents
- Check the **git log** (long-term memory)
- Identify core files / main entry points
- Output an **understanding report** so you can validate its mental model; it then proposes the next phase from the PRD

The command is evolved over time to be project-specific (e.g., add pointers to project docs to the reference folder).

## Step 3: PIV loops (Plan → Implement → Validate)

Take one focused unit of work (usually one PRD phase) through the full loop:

### Plan
Two layers: **top-level project planning** (already done via PRD + rules) and **task-specific planning**:
1. **Vibe planning** — unstructured conversation: architecture for this feature, spin off sub-agents for codebase analysis/documentation research, identify concrete tasks. The conversation is disposable context.
2. **Run `/pl-feature <optional-arg>`** — turns the conversation into a **structured plan** (a mini-PRD): problem statement, context/references, an implementation plan with a task list detailed down to individual files to create/update, and — most importantly — a **validation strategy defined before any code is written** (test-driven mindset). Iterate on the plan in conversation until it matches the PRD/your intent.
3. **Set up the environment in parallel** — create `.env.example` and set real env vars (secrets like DB URL) *before* implementation. Coding agents get tripped up by missing env vars, silently mock-testing and claiming things validated when they're not. With env ready, the agent can run migrations, start servers, and do real E2E without interruption.
4. **Reset the context** (golden rule) — start a brand-new conversation where the structured plan is the *only* context sent in (it contains all references, the full task list, and the validation strategy). No bloat during execution.

### Implement
Run `/execute <plan>` in the fresh context; **delegate all coding to the agent**. It checks its own work: type-checking, linting, unit tests, integration tests, and E2E tests — including full browser automation (Cole uses a **Vercel Agent Browser CLI skill** so the agent spins up frontend + backend, runs DB migrations, and clicks through the app like a user, capturing screenshots).

### Validate (human)
"Trust but verify": the agent's self-testing is sandwiched between planning and validation that you're deeply involved in. Review the code (important if you're technical), then **manually run the app and use it like a user** — create an account, add/edit links, reorder, save, refresh — before committing. Then run `/commit` so every message follows the same standardized format (feeding the git log long-term memory). There's no "vibe coding" here — the plan + validation are what justify delegating implementation.

## Keeping the codebase reliable (regression testing)

As PIV loops stack features, ensure old functionality doesn't break — either:
- **DIY test harness**: have the agent list all E2E tests it ran and package them into a reusable command you run after each future feature; or
- **Platform like QA Tech**: paste your deployed URL, it crawls the site, you supply test-account credentials (stored securely), and it generates test cases that AI agents evolve alongside your codebase. Debug loop: "there's a bug — create a test that should be failing, fix it, then the test should pass."

## System evolution (the highest-leverage step)

Every bug or misalignment (e.g., the front-end style didn't match what he wanted) is an opportunity to upgrade the AI layer: tighten rules/style guides, add new on-demand context (his example: a `styles.md` for Tailwind/shadcn conventions), expand E2E test coverage in commands/workflows, or add a failing-then-passing regression test.

Key practice: for AI-layer changes, have the agent do **meta-reasoning only** ("don't change anything — what should we add/update in our rules or on-demand context?") but **make those small, focused changes yourself** — while for codebase work you delegate to the agent as much as possible. The result: three things evolve in parallel — **codebase, test base, and AI layer** — and it compounds: the agent becomes more reliable, repeatable, and in-tune with *your specific project* over time.

## The recurring loop

After system evolution, loop back to the top: more PIV loops, phase by phase, until the MVP is complete — then the project graduates to brownfield development (covered in a follow-up video). Cole's full resource library of commands and rules is available in the Agentic Coding course in the Dynamis community.