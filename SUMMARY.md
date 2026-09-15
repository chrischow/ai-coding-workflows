# AI Coding Workflows — Meta Summary

A synthesis of 16 summaries of AI coding workflow talks (YouTube). Each source is cited by a short name in brackets: **[Holtz]** Charlie Holtz/Conductor, **[Titus]** Chris Titus, **[Sloth]** Coding Sloth, **[Medin]** Cole Medin, **[Dex]** David Ondrej/Dexter, **[OpenCode]** DevOps Toolbox, **[IndyDevDan]** Dan Eisler, **[KunChen]** Kun Chen, **[Pocock]** Matt Pocock, **[PocockSkills]** Matt Pocock's skills repo, **[Milan]** Milan Jovanovic, **[Owain]** Owain Lewis, **[Prime]** ThePrimeagen, **[Tim]** Tech With Tim, **[Nebula]** Web Dev Cody/Nebula, **[Zen]** Zen van Riel.

---

## Core Principles

1. **The human gates the start and the end; the agent owns the middle.** Almost every workflow brackets agent work with human judgment: planning up front, review/validation at the end [KunChen, Titus, IndyDevDan, Medin]. "Agents left alone produce horseshit" [Prime]. The software factory replaced only one station ("person builds the thing") with AI; review remains a human bottleneck you must not skip [Dex, Titus].

2. **Read the code — or at least hold the product logic.** The "lights-off factory" (nobody reads code) fails: you eventually hit a bug no agent can fix and trudge through slop for weeks [Dex]. There are two escape hatches on this axis: review *interfaces/modules/plans* rather than every line [Pocock], and *risk-based review* — skip diffs for low-risk changes, deep-review risky ones [KunChen]. But even the most hands-off practitioner still verifies output and sets the quality bar [Nebula, KunChen].

3. **Context is your most precious resource — manage it like currency.** Models have a "dumb zone": output degrades above roughly 100–200K tokens, far below the advertised 1M window [Sloth, Dex, PocockSkills]. Consequences everyone converges on: one fresh session per task [Sloth, Medin, Owain], specs/tickets to carry state across sessions [PocockSkills, Owain], compact-and-restart near the dumb zone [Dex], and keep always-loaded memory files tiny [KunChen, Sloth].

4. **Specify first, implement second — the spec is a contract.** A spec (what, why, constraints, *what you're NOT doing*, verification per task) removes agent guesswork [Owain]. Plans fail at roughly ×10 cost per abstraction layer: a bad line in a plan costs ~100 lines of code; a bad PRD line costs ~1000 [Medin]. Separate documents by audience: PRD (what/why, humans), design doc (how, engineers), AI spec (execution, agents) [Owain, Dex].

5. **Verify with evidence, every time.** Agents cannot tell if their own output is correct [Sloth]. The universal answer: tests written *before* implementation [Sloth, Medin], type checkers/linters as always-on deterministic reviewers [IndyDevDan, Titus], and *run the thing like a user* — real API calls [Zen, Owain], real database integration tests [Milan], browser automation with screenshots [Sloth, Medin], or headless self-driving (JSON mode) [Prime]. Kun Chen's pipeline records evidence (screenshot/video/log) you can inspect [KunChen].

6. **Make the AI layer a first-class, evolving artifact.** Agents get better over time *if* you improve your side: curated memory/rules files [Holtz, Titus, KunChen], skills/commands for repeatable processes [Medin, PocockSkills, Sloth], on-demand reference docs [Medin, Dex]. Every bug is an opportunity to evolve the AI layer so it can't happen again — "system evolution" is the highest-leverage step [Medin].

7. **Isolation enables parallelism.** Work trees / sandboxes let many agents (or agent sessions) work concurrently without tripping over each other [Sloth, KunChen, IndyDevDan, Nebula, OpenCode, Prime, Holtz]. Concurrency only works when the codebase is architected into small, isolated modules [Nebula].

8. **The value is in the iteration loop, not the generated code.** Eleven lines of code with a brilliant review loop beats a thousand lines without one [Titus]. Real edge cases surface in QA, not in any spec [Pocock]. Prompts are what matter — code is consumable sawdust that gets discarded when the next model lands [Holtz].

9. **Slow is smooth, smooth is fast.** Wait for tests to go green, reviews to complete, and CI to pass before merging [Titus]. A good workflow beats the next model — no subscription or model fixes a bad process [Titus, KunChen]; work the real bottleneck, don't just max tokens [Dex, Holtz].

10. **Stay the human that understands the product.** You're competing with a PM at Anthropic, not with the lab itself [Dex]. The endgame of delegation is the "captain": you spend your energy on users, direction, and the treasure map, while crewmates execute [KunChen]. But that requires *knowing your system so well you don't have to look* — the opposite of vibe coding [IndyDevDan].

---

## Areas of Convergence

1. **The overall skeleton is nearly identical everywhere:** idea → interview/grill/research → spec/PRD → break into small tasks (each sized to ~one context window) → fresh session per task → implement with embedded verification → independent review → human QA/review gate → commit/PR/merge → evolve the AI layer, repeat. [Owain, Pocock, PocockSkills, Medin, Titus, KunChen, Tim, Sloth]

2. **Planning is done by interrogation, not by you writing a doc.** The LLM interviews you, challenges your framing, checks the codebase first, and surfaces edge cases *you* must decide — multiple-choice questions, ~20–25 answers, each removing an assumption [Pocock, Medin, Tim, Owain]. "Explain the why, not just the what" [Pocock].

3. **Task/ticket size ≈ one context window.** Tickets are sized so each is one session's work: ~140K tokens / the "smart zone" [PocockSkills], "one PRD phase per PIV loop" [Medin], "implement T1 in a fresh session, commit, walk away" [Owain]. Tiny tasks get merged to avoid paying agent spin-up cost [Pocock].

4. **Verification strategy before code, and tests-first.** Define how success is measured *before* implementing [Medin], write tests before (or alongside) code rather than after [Sloth, Titus], integration tests against real infrastructure [Milan], and always-on linters/type checkers as deterministic first reviewers [IndyDevDan, Titus, Sloth].

5. **Independent review beats self-review.** A fresh-context reviewer (sub-agent) catches what the implementing agent can't see — main agents are bad at criticizing their own code [PocockSkills, KunChen]. Use *multiple independent* AI reviewers outside the implementing loop [Titus], plus deterministic code (linters, tests) which is the most reliable reviewer of all: no tokens, no hallucination [IndyDevDan].

6. **PRs and commits are gates *and* long-term memory.** The PR is the manual gate [Titus]; merging is enforced, not optional [Holtz]; standardized commit messages feed the git log so agents can reconstruct how the codebase evolved [Medin]; auto-commit so you can always roll back [Tim].

7. **Skills/rules are lean, plain text, and progressively disclosed.** Global memory stays minimal (~27 lines — it loads into every session) [KunChen]; skills expose only a tiny description until invoked [PocockSkills, KunChen, Sloth]; on-demand reference docs are read only when relevant [Medin]; files are plain text you're expected to edit to taste [Titus].

8. **Voice is a first-class input, not a gimmick.** Talking is ~3× faster than typing [KunChen] and easier to be descriptive with [Sloth]. Used by Holtz (whispering at the screen), Cole (brain-dumping specs), Tim (dictating prompts), Zen-adjacent workflows [Holtz, Medin, Tim, KunChen, Sloth].

9. **Environment must be ready before implementation.** Env vars, secrets, seeds, migrations and test accounts set up *up front* — otherwise agents silently mock-test and claim validation [Medin]. Know your tool/market capabilities before asking (read the docs) so you can direct the agent [Tim]. Ground the agent on the real API docs you're integrating with [Zen].

10. **"Commandify what you do twice; ask what you can remove."** Anything repeated becomes a reusable command/skill [Medin]; bloat is the enemy in every layer — skills, memory, features, prompts [Nebula, Sloth, KunChen]. The agent ecosystem rewards minimalism: batching many agents is only viable when context stays small [Sloth].

11. **Terminals + worktrees dominate the professional setups.** WezTerm/tmux/Neovim with agents in one pane, editor in another [KunChen]; every chat/session in its own work tree [Sloth, Prime, Nebula]; sessions saved/resumable, often stored in a queryable database [OpenCode, KunChen].

12. **Trust must be earned, so start gated and loosen as the system proves itself.** Run the workflow by hand first, then codify it [IndyDevDan]. Enforce structure you don't trust agents with at first (Conductor made direct file editing impossible; worktree → PR → merge) [Holtz]. Teams eventually drop engineering review once the factory has proven itself [IndyDevDan].

---

## Areas of Divergence

1. **Formality of planning.** One end: heavy, phased, gated planning — PRD → rules → `/prime` → PIV loops [Medin], spec + tasks [Owain], six-phase gating where data structures/interfaces/to-dos are each human-approved *and implementation is reverted and reported on* before landing [Prime], full spec/design/task phases [Pocock, Titus]. Other end: minimal planning — no plan mode at all, just high-level user-story prompts, "the model is capable enough to one-shot most changes" [Nebula]; Matt skips planning entirely when a change fits one context window [Pocock/PocockSkills]. Note the underlying convergence: everyone decides *what/why* up front; the divergence is whether that decision is formalized into artifacts and multi-stage gates.

2. **How much code the human reads.** The widest genuine split. Read everything & every review: "[you] must read the code, or at least the logic" — the lights-off factory is a trap [Dex]; still reviews hunks one by one [Prime]; reviews generated code line-by-line for correctness [Milan]. Read interfaces, not implementations [Pocock]. Read nothing / risk-based: manage like an EM, low-risk diffs skipped entirely, deep-review only risky changes [KunChen]; someone built an 85K-line Rust app without ever reading the Rust [Nebula]. The practical middle used by most: humans stay fluent in the *product logic* (schemas, flows, failure modes), delegating line-level code understanding [Dex, KunChen].

3. **Autonomy of the agent.** Bounded autonomy everywhere, but the bounds differ a lot: human approves every phase [Prime]; PR is a mandatory gate [Titus, Holtz]; the pipeline babysits PRs to merge and agents run overnight unattended [KunChen, Pocock]; fully autonomous "CEO mode" orchestrating many agents, waking only to digestible reports [Holtz]; multi-agent teams that are "~95% okay" [OpenCode]. Related: multi-agent orchestration is either the goal (software factory: router agent → specialized sandboxed workflows) [IndyDevDan, Holtz, KunChen] or derided as over-engineered hype ("dead simple" wins; sub-agents only for research) [Medin, Sloth].

4. **Skills & memory files: how much, and where?** Curated-heavy: hundred-line CLAUDE.md of engineering practices; skills as first-class [Holtz]; whole ecosystems of skills with grilling/spec/implementation flows [PocockSkills, Sloth]. Lean: global memory ~27 lines, load conditionally into skills [KunChen]. None: no skills, no CLAUDE.md, just prompts — "intelligent models need less hand-holding" (the Claude Code author reportedly deleted 80% of his) [Nebula]. Cautionary notes on the skills gold rush: popularity ≠ quality; sketchy skills can exfiltrate keys or make agents *worse* [KunChen, Sloth].

5. **Token philosophy.** Maxxing: fast mode, "think extra hard", $22K/month, spend is the strategy [Holtz]. Discipline: $20-plan survival, fresh sessions, kill research tangents, price-check everything [Sloth]; token caps and stop conditions on overnight runs [KunChen]. Cost engineering: plan with the frontier model, implement with a cheaper workhorse [Sloth, OpenCode, IndyDevDan]; local/self-hosted GPU models for privacy [Zen]. (They agree on one thing: "don't optimize for loops — optimize for repeatable workflows"; token-spend style is personal.)

6. **Tool/harness loyalty.** Claude Code is the default demo surface [Sloth, Medin, KunChen, Pocock], but Codex is the "workhorse" [Holtz] and Coding Sloth's favorite [Sloth]; Cursor "performs a lot better" as a harness [Tim] and is Milan's tool [Milan]; OpenCode is the "best in existence" for its owner [OpenCode]; Pi is the minimal build-it-yourself choice [Sloth, KunChen]; Conductor orchestrates many agents at once [Holtz]; local models via LM Studio Link for privacy [Zen]. Convergence underneath: the workflow is harness-agnostic — memory files are symlinked across harnesses [KunChen]; don't treat any tooling terminology as gospel [Titus].

7. **Where knowledge lives.** In-repo: ADRs and external-context markdown so the model finds context deterministically via read/grep [Dex, Owain, Medin, Pocock]. In memory files: global + project rules [Titus, KunChen, Holtz]. In the prompt itself: describe the screen to the person next to you; use the codebase's own keywords [Nebula]. Most combine all three [Tim, Medin].

8. **Scope of "skills".** Skills as markdown workflow guides [PocockSkills, Medin, Sloth] vs skills as packaged integration instructions from vendors (ImageKit, GitHub MCP) that teach the model how to drive external tools [Tim] vs skills as autonomous skill-installers (meta-skills) [KunChen, OpenCode]. Consensus only on progressive disclosure and provenance.

9. **Greenfield vs brownfield process.** Explicitly different setups and rule sets [Medin]; different thinking about code golf vs. existing codebase safety [Holtz]; skills claimed to work on both, demoed on brownfield [PocockSkills]; most demos are actually greenfield apps [Tim, Zen, Owain].

10. **What "done" looks like.** Ship-to-merge with CI green and review comments addressed [Titus]; ship the weekly batch after manual verification, ~5–10% of features reverted [Nebula]; issues → AFK agent closes them [Pocock]; PRs the CEO merges from the sofa of a review sidebar [Holtz]; a game the agent plays 1,000 times to find the strongest strategy [Prime].

---

## Recommended Workflow

The converged workflow below is the intersection of the strongest repeated practices — each step cites where it comes from. Treat it as a default you customize; the one thing every source agrees on is that you should design your own.

### Phase 0 — Foundation: guardrails before code

- **Memory & rules, kept lean.** A small global memory file (personal preferences, measurement units, style rules — ~25–30 lines max, it loads every session) and a project file (repo layout, conventions, testing instructions). Symlink both so every harness reads the same files [KunChen, Titus, Sloth].
- **Guardrails first:** have the LLM scaffold a test suite and validate it *before* real work; add linting/type-checking; enable CI, Dependabot and CodeQL [Titus]. Standardize commit messages early — git log is the agent's long-term memory [Medin].
- **Install a small, trusted skill set.** One coherent set matching your style from a source you trust; tiny descriptions only; be suspicious of "magic" skills from random repos [Sloth, KunChen, PocockSkills]. Add MCP/tools with *agent ergonomics* in mind — CLI over token-hungry MCP servers [KunChen].

### Phase 1 — Ideation & grilling (human-heavy, voice-friendly)

- Brain-dump the raw idea aloud into the agent (voice dictation is standard practice) [Medin, Tim, KunChen, Holtz].
- **Grill session:** the agent explores the codebase (via a read-only sub-agent with its own context window, returning only a summary), then interviews you: challenges framing, checks the code first, asks clarifying questions, offers A/B trade-offs. Explain the *why*. ~20 questions; every answer removes an assumption [Pocock, Medin, Tim].
- Extract a **ubiquitous language** glossary (DDD-style) so later conversations are precise ("there's a bug in the materialization cascade") [Pocock].

### Phase 2 — Spec & plan (a contract, not a dissertation)

- Write a **PRD/spec**: problem, why, measurable success, constraints, explicitly out-of-scope, decisions already made. Keep human docs (PRD/design) separate from the agent's execution spec [Owain, Dex, Tim, Medin].
- Add **program design**: call stack, tests, files, types and method signatures — human-readable, no implementation details; settle "why did you put that file there?" questions cheaply, low in the context window where reasoning is cheapest [Dex].
- **Break into tickets,** each sized to one session/context window (~140K "smart zone"), each with acceptance criteria and a verification method; link blocking relationships; store in your issue tracker; merge trivial tasks to avoid agent spin-up cost [PocockSkills, Pocock, Owain].
- Set up **env vars, seeds, migrations and test accounts** now, so the agent can run real E2E without interruption [Medin].

### Phase 3 — Execute one ticket per fresh session

- Fresh context per ticket; the ticket/spec is the only context sent in [Medin, PocockSkills, Owain, Sloth].
- Delegate implementation fully, with verification instructions embedded: type-check, lint, unit + integration + E2E tests, browser automation with screenshots [Medin, Sloth]. Let the agent call the real backend APIs it integrates with, so it self-corrects its own API usage [Zen].
- **Parallelize with isolation:** one work tree per ticket/session (Treehouse-style automation if you have many); spawn parallel sessions only when changes can't collide [KunChen, Sloth, Nebula, IndyDevDan].

### Phase 4 — Validation (the human gate)

- **Deterministic + independent review:** linters/type checkers always; then route the work through *independent* reviewers — sub-agents in fresh context windows checking against the spec and against coding standards; fresh-context reviewers catch what the author missed [PocockSkills, Titus, KunChen].
- **Human review, risk-based:** review module shapes/interfaces/plans; deep-dive only on risky changes; at minimum hold the product logic (schemas, flows, failure modes) [Pocock, KunChen, Dex].
- **QA like a user, with evidence:** rebuild the app, walk the flow manually, report bugs as issues with auto-generated context; inspect recorded evidence (screenshots/video/logs) [Pocock, KunChen, Medin]. Expect real edge cases here — this is where the value is [Pocock, Titus].
- **Parallelize human QA with machine fixing:** while you test, an AFK loop works the feedback issues; iterate QA → feedback → fix → rebuild → re-QA [Pocock].

### Phase 5 — Integrate & ship

- PR as the manual gate; automated checks must go green; independent reviewers comment; you read the reviews and the code, decide accept/reject, tell the agent to address and push [Titus].
- Final merge decision by a human; merge, delete the branch [Titus, Holtz]. Record decisions/lessons in ADRs in the repo [Dex, Medin].

### Phase 6 — Evolve the system (the meta-loop)

- Every bug or mismatch → improve the AI layer first: tighten rules, add on-demand reference docs (e.g. `styles.md`), expand test coverage in commands, add failing-then-passing regression tests [Medin]. Meta-reason with the agent about what to add, but make small AI-layer edits yourself [Medin].
- Weekly product loop: use the app → accumulate requested changes → dispatch them concurrently → hand-review every session → ship [Nebula].
- Keep asking *what can I remove?* — in features, skills, memory, and prompts [Nebula, Sloth].

### Cross-cutting practices

- **Voice input** for anything you'd otherwise type longhand [KunChen, Holtz, Tim, Medin].
- **Context hygiene always:** kill research tangents, name the exact files, be stupidly specific [Sloth]; compact-and-restart near the dumb zone [Dex]; one task per session, no stacking [Sloth].
- **Cost engineering:** plan with the frontier model, implement with a cheaper/faster workhorse [Sloth, OpenCode, IndyDevDan]; set token caps and stop conditions on unattended runs [KunChen]; make it local if privacy matters [Zen].
- **Match rigor to context:** teams, paid products, long-lived codebases need the full gate-heavy pipeline [Dex, Titus]; pre-product-market-fit or prototypes can afford vibe-leaner, faster loops, throwing things at customers [Dex, Nebula]. The workflow should be designed around your actual day, not someone else's [Nebula, Medin].

---

*Compiled from: summaries/charlie-holtz-ai-coding-setup.md, chris-titus-ai-coding-workflow.md, coding-sloth-1000-hours-claude.md, cole-medin-agentic-coding-workflow.md, david-ondrej-agentic-engineering-workflow.md, devops-toolbox-opencode-workflow.md, indydevdan-agentic-engineering.md, kun-chen-agentic-engineering-workflow.md, matt-pocock-claude-code-real-feature.md, matt-pocock-skills.md, milan-jovanovic-engineering-approach-to-ai.md, owain-lewis-spec-driven-development.md, primeagen-ai-workflow.md, tech-with-tim-real-ai-coding-workflow.md, web-dev-cody-ai-coding-workflow-nebula.md, zen-van-riel-local-ai-coding-workflow.md*