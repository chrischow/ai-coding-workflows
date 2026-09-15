# Spec-Driven Development — Workflow Summary

[Link to video](https://www.youtube.com/watch?v=RhaF4LVAVng)

## What is spec-driven development?

Instead of prompting an AI agent first and "figuring things out as you go," you define a **specification up front** — a short document describing your intent: what you're building, the constraints (what you're *not* building / doing), and the key decisions. It acts as a **contract** for how the code should behave.

**The problem it solves:** telling an agent to "add authentication" leaves a huge number of implementation decisions open. Agents will guess and make choices that don't align with what you actually want. A spec removes that guesswork — e.g., "we want JWT tokens, no password reset, no OAuth, no new dependencies" is far clearer than "add auth."

## Terminology (common confusion)

| Document | Audience | Purpose |
|---|---|---|
| **PRD** (Product Requirements Doc) | Humans (PMs, stakeholders) | *What* we're building, *why*, business value |
| **Architecture / Design Doc** | Engineers | *How* we're building it — architecture, constraints, scalability, security |
| **AI Spec** | AI agents | An **execution document** — a plan the agent executes, not a debate |

## What goes in an AI spec

1. **What & Why** — context about what is being built and why (helps the agent fill in guesswork / make decisions).
2. **Constraints** — libraries to use or avoid, design patterns, error handling, out-of-scope items. Agents are eager to build, so shrink scope to exactly what you want.
3. **Discrete tasks** — each task defines: what to build, which files to touch, and **how to verify it's complete** (e.g., build passes, tests). Same way you'd hand tasks to engineers on a team.

## The workflow

1. **Generate a spec** using a metaprompt (e.g., a `/spec` slash command) — briefly describe the feature; the agent drafts a spec file.
2. **Review & iterate on the spec** — verify details are correct, edit constraints, make sure the agent won't have to guess. The spec is a living document: you can fix it after seeing bad results, unlike waterfall design.
3. **Break the work into tasks** (the agent can help).
4. **Execute tasks independently** — one task per session ("read the spec and implement T1"), review the code, iterate, and commit before moving on.
5. **Test & verify** — run builds/tests and actually exercise the new code (e.g., fetch a real URL) to confirm behavior.

This incremental, task-by-task loop mirrors how software has always been built: you never hand an engineer a whole feature doc — you give them individual tasks and review as you go.

## Key benefits

- **Agent can't implement things you don't want** — full control over scope and decisions.
- **Higher quality code** — reviewing small, committed increments is far easier than unpicking 10,000 lines an agent wrote in one go.
- **Resumable across sessions** — implement task 1, commit, walk away, and pick up task 2 in a fresh session.

## Examples from the video

**JWT auth:** *Why* — users share a single account; need individual accounts for billing and personal settings. *What* — register, login, refresh tokens; 1-hour access tokens, 7-day refresh tokens. *Constraints* — PostgreSQL via Prisma, defined error handling, no new dependencies, no tokens stored in DB; out-of-scope items listed. *Tasks* — user model → endpoints → login/refresh → end-to-end tests.

**Hybrid search (PG Vector):** many details an agent would get wrong (embedding dimensions, FastAPI, OpenAI embeddings library) — all pinned down in the spec, then broken into tasks starting with "add the PG vector extension."

**Live demo (YouTube IQ app):** used `/spec` to design a "video analysis" feature (paste a YouTube URL → pull transcript → generate insights on hook, pacing, key points, improvements). The agent proposed `youtube-transcript` npm package; the author edited the spec to use the **Superdata API** (100 free requests, more reliable, no rate limiting) — showing specs are meant to be edited. Then a fresh session ran "implement T1," the agent built the transcription service and verified the build; the author reviewed the code, flagged the regex-based video-ID extraction as suspicious, and verified the feature live via a real URL fetch.

## Frameworks — opinionated take

- **Openspec** — lightweight CLI that scaffolds spec files; fine.
- **GitHub Spec Kit** — heavyweight (requirements/user stories → technical design → implementation tasks → execution phases). Found **overkill**: user stories in markdown, PMs editing markdown, and architecture docs reduced to markdown don't make sense. Traditional PRDs and design docs should stay separate from agent execution.

## Bottom line

There's nothing new here — engineering teams have always done this: PRD (what/why, for humans) → technical design doc (how) → discrete tasks handed to individual developers. The **only difference** is that the tasks are handed to **agents**. For any non-trivial AI-assisted work: write a specification first, store it on local disk as a persistent file, review and edit it, ensure the tasks are correct and complete — then work through it incrementally.