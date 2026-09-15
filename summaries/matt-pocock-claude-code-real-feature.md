# Matt Pocock — Building a Real Feature with Claude Code

[Link to video](https://www.youtube.com/watch?v=hX7yG1KVYhI/ne)

Matt Pocock demonstrates his complete workflow for building a real feature with Claude Code, end to end, on his "course video manager" app (React Router, TypeScript, Node, Drizzle, Postgres, Effect for the backend, lots of testing). The philosophy throughout: treat the LLM like a colleague you'd delegate to — focus on architecture and feedback loops, not upfront planning.

## 1. Grilling: turning vague ideas into a hardened spec

- He dictates rough, woolly ideas into a **Grill Me** skill. The input is deliberately messy — a feature idea ("ghost courses", direct create/delete for real lessons) without clear requirements, which he says is how most dev days start.
- Key principle: explain the **why**, not just the **what**. If the LLM only knows what to build, it can't suggest alternatives. When he hadn't explained the why for ghost courses, that was flagged as a gap.
- The session starts with an **explore phase**: a sub-agent reads lots of codebase files in its own context window and returns only a summary to the parent agent. Very token-efficient; "you need it in every single session."
- The LLM plays the role of a smart senior dev (he compares it to a former boss famous for asking great questions). It:
  - **Challenges framing**: "delete lesson already handles both ghost and real lessons directly — what in the UI forces the convert-to-ghost flow?"
  - **Asks clarifying questions at the right moments**, e.g. "Does direct create apply inside ghost courses too?" — forcing him to make precise decisions rather than leaving ambiguity.
  - **Fleshes out woolly language**: "a ghost course has no file path, so what does 'real lesson' mean inside it?" This pushed him to define the materialization cascade.
  - **Checks the code first** whenever a question can be answered by looking at the code (part of the skill design), rather than asking the human.
  - **Surfaces trade-offs** with options A/B when asked, e.g. two create buttons vs. a cascade-triggering modal, ghost-course UI choices.
- He uses the **side-question feature** (ask a quick question without polluting chat history) to inspect the course write service.
- Result: ~22 minutes of grilling produced just 8 bullet points of decided scope — but now "it's all on rails." The hard, human-in-the-loop part is done; the human decided, the LLM challenged, and every edge case was either decided or consciously deferred.

## 2. The ubiquitous language document

- Inspired by Domain-Driven Design: a shared glossary of terms between the domain expert (him) and the dev (the LLM), so they can talk precisely.
- He asks the LLM to update the glossary after the session: new terms like **ghost course**, the verb **materialize** ("transitioning a ghost entity to a real entity by creating its on-disk representation"), and even **materialization cascade** — so later he can say "there's a bug in the materialization cascade" and it's fully understood.
- The glossary also makes naming trivial (function names like `materialize ghost`, `delete lesson` come straight from the shared vocabulary). The update is committed.
- He explains why the grill-me skill avoids the ask-user tool: calling a tool costs token overhead (JSON wrapping), so not calling a tool is more token-efficient.

## 3. Write a PRD

- A follow-up skill converts the Q&A session into a **PRD**. He loves question-and-answer format because the question and answer are collocated, which he believes creates attention "hot spots" — a rich document for the LLM to work from.
- The PRD skill **sketches out the major modules** without looking inside them (e.g. the course write service, a new materialization cascade method, two API routes, ghost course UI, plans deprecation). He reviews interfaces and module boundaries rather than implementations — and rejects scope creep he doesn't want (plans deprecation is pulled out).
- It asks which modules need tests; it initially claimed there was no test harness for the service — he says "look harder" — and the full suite is found. It also records **testing decisions** (TDD, feedback loops) so the LLM is more likely to follow good practices during implementation.
- The PRD is not reviewed (LLMs are good at summarizing); it's submitted as a **GitHub issue** — his backlog of record, since his AFK agent pulls issues from GitHub.

## 4. PRD → issues

- **PRD to issues** skill breaks the PRD into individual GitHub issues with blocking relationships ("blocked by #1").
- Slicing is sized for AFK agents: not too big, not too small. Tiny tasks (hide publish UI on ghost courses) get merged into bigger ones to avoid paying the cost of spinning up an agent for a one-liner. 6 issues become 4 slices (e.g. ghost course creation, direct delete action, materialization cascade).
- He doesn't review the issues; each references the parent PRD, gives acceptance criteria, blocking info and user stories ("if the PRD is the destination, the issues are the journey").

## 5. The AFK loop ("Ralph" / Sandcastle)

- A custom setup (provisionally named **Sandcastle**): a Docker container mounts the working directory, Claude runs inside it, and any commits it makes are extracted as patches and applied to the local repo. A prompt + Docker file let him run the loop again and again with different issue sets. Run via `pnpm raiph`.
- The agent picks up GitHub issues, works through them, runs **tests and types on every commit**, adds tests, commits with detailed messages, and **closes the issue** as it completes it. He can literally walk away — go for a walk, have tea, or start another grilling session.
- This is the **day shift / night shift** model (borrowed from Jamon): the human does the day shift (thinking, grilling, PRDs, issues), Claude does the night shift (implementation AFK).

## 6. QA: the human-in-the-loop iteration phase

- After the loop completes, the real work begins. He opens a fresh session and free-styles: "take the last 5 commits and create a QA plan" — saved as a GitHub issue with step-by-step instructions for testing every part (something he's considering adding to all his skills).
- He rebuilds the app and walks through the QA plan manually, reporting bugs via an in-app **feedback button** that creates GitHub issues from his dictated feedback (with auto-generated titles).
- His key insight: **QA is where the real edge cases surface** — things no grilling or spec could have planned for: a minified React error on ghost course creation, no loading state, no redirect after creation, a non-git-repo failure leaving the database and file system out of sync (needs rollback of directory creation), wanting a confirmation modal before deleting a real lesson.
- Crucially, the **Ralph loop runs in parallel with his QA** — he submits feedback, relaunches the loop, and keeps testing while it fixes things. He labels certain issues "human in the loop only" so the agent skips them.
- Some QA feedback is pure design iteration he couldn't resolve in the abstract: the two "create ghost lesson / create real lesson" buttons felt wrong in reality, so he asked for a single "add lesson" with a **"create on file system" checkbox** instead. Sometimes you only know the right UI after seeing the wrong one built.
- When behavior changes enough, he closes the QA plan to remove it from the agent's context so it isn't treated as a source of truth.

## Takeaways

- **Specs-to-code never fully works.** The QA loop inevitably reveals weird edge cases that are genuinely hard to plan for; the value is in iterating: QA → feedback issues → AFK fixing → rebuild → re-QA.
- **Spend the effort up front** (grilling, ubiquitous language, PRD) because that's the human-in-the-loop part; from the 8 bullet points onward it's mostly on rails.
- **Review inputs and outputs, not code.** He rarely reads implementation; he reviews module shapes, interfaces, and test plans, poking into code only to sanity-check the track.
- The magic is **parallelising human QA with machine fixing** — the human's day shift and the agent's night shift compound.
- It's a flexible backlog: any time he can queue a batch of bug fixes and let the agent chew through them.