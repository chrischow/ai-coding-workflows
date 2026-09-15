# ThePrimeagen's Six-Phase AI Coding Workflow

[Link to video](https://www.youtube.com/watch?v=Aie0nYktsNA)

**Source:** Video transcript where ThePrimeagen (Prime) demonstrates his personal LLM-assisted programming workflow, built because he spent two weeks traveling without a desktop computer and still wanted to produce quality code from his phone.

## Motivation

- He travels without a keyboard/setup, so he needed a way to do *real* programming on the go — not just "make me a game" vibecoding.
- Core belief: **agents left alone produce "horseshit."** Simply saying "AI, go do the thing" ruins a project, so the human must stay in control of every step.
- Goal: still be the person doing the coding and reviewing; he wants the AI to follow his plan phase-by-phase rather than act autonomously.

## The Workflow: "Six-Phase Work" (technically 7 phases)

| Phase | Name | What happens |
|-------|------|--------------|
| 0 | Research & plan | Come up with a plan for how phases 1–6 will be executed. |
| 1 | Data structures | Define all the data structures. Human reviews and corrects before proceeding. |
| 2 | Interfaces | Define all function/API stubs that need to be created or changed. Human edits/corrects ("don't add that, inline it into update"). |
| 3 | To-dos | Add explicit to-dos everywhere the codebase needs to change, walked one step at a time. |
| 4 | Implement, then revert | Implement the change, run the game in JSON mode to verify it works, then **undo all changes** and report where the agent had to break from the to-dos/interfaces/structures. |
| 5 | Invariants | Add invariants. (He notes LLMs are *horrible* at this step.) |
| 6 | Implement | Actually land the final implementation. |
| 7 | Profit / finish the game | The ultimate goal is shipping a game. |

The essence: **gating**. If the data structures don't look correct → don't go forward. If interfaces or to-dos don't look correct → don't go forward. Phase 4's revert-and-report loop is designed to expose design gaps — "here's where I had to break from your plan, you should address this."

He calls phase 4 the best change he's ever made to how he designs programs, even when coding by hand, because it feeds back exactly where a plan breaks down.

## Key Tools / Infrastructure

- **JSON mode** — The game (written in Odin) runs headless: instead of rendering to raylib it emits/consumes JSON. An agent can drive the full game (mouse operations, events, dragging cards) without rendering, record every JSON output to a file (e.g. `out.json`) for replay, and play at ~40,000 frames/second for super-fast testing. He built this instead of MCP because he wanted something more robust that can also emit events — effectively "a Playwright CLI for gamedev."
- **55** — His second AI-in-Vim/Neovim library (named after Halo's BR55 battle rifle; his first was SRS 99 / the sniper rifle — 55 is "a bit more spray, but still an accuracy weapon").
  - Each project lives in its own **git worktree** with pending changes.
  - `inspect` — takes every git hunk and puts it into the quickfix list for review.
  - Toggle diff lines, walk through changes, and send targeted instructions to the agent (e.g. "pull from master and resolve conflicts; enemies are no longer removed but marked dead").
  - **Cloud handoff** — the plan is to push in-progress work to the cloud so he can finish changes from his phone; he's still testing whether it works.

## Opinions & Notes

- He still reviews code badly — his self-identified weakness — so logic sometimes slips into the UI layer and he has to clean it up.
- Some problems are awful to describe in English (e.g. "return the highest z-ordered interactive element and do the mouse check there instead"). After ~7 failed attempts to get agents to write it while traveling, he wrote it himself in 15 minutes.
- He hand-rolled his own animation system for the game and is proud of it. Pixel art is by Adam C. Ununice (a human artist) — he believes "LLMs don't produce great code, but they produce even worse assets."
- Uses SuperMaven for tab-completion (~250ms latency); when he's locked in, he thinks it beats any LLM.
- Naming convention: skipping 69 ("the needler") — 55 is deliberate, not a machine gun sprayed across the codebase.
- Game repos: the Lua version (Mordoria V2) is public with a retained-mode UI; the newer Odin version uses immediate mode throughout. The public release of the game is the end goal.
- Future ambition: run an agent loop that plays 1,000 games as the blacksmith, inspect win percentages, discover which cards win most/least, and generate a written strategy — a real agent playing the game.