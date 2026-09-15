# AI Developer Workflows over "Loop Engineering" — IndyDevDan

[Link to video](https://www.youtube.com/watch?v=VQy50fuxI34)

Dan Eisler (IndyDevDan) argues that "loop engineering" is a hype-filled, inaccurate rebrand of the software development life cycle, and that the real goal is designing **AI developer workflows (ADWs)** that run inside a **software factory**.

## The three actors of value creation

Building with agents means combining three actors, each with a hidden cost:

1. **Engineers** — most expensive; show up at the beginning (prompting/planning) and the end (reviewing/validation). These two constraints bracket the whole system.
2. **Agents** — flexible but least reliable and consume tokens.
3. **Code** — the unsung hero: deterministic, always runs the same way, no token cost, no hallucination. The most reliable of the three ("code > engineers > agents").

The game of agentic engineering is knowing when and where to place each actor.

## The shape of an ADW (progressive scaling)

- **Base case:** engineer prompts an LLM, engineer reviews the result.
- **First loop:** add a linter as code with a condition — on failure, route results back into the build agent; on success, pass through. This routing-back is what people call a "loop," but it's just one piece.
- **More deterministic code:** add formatters, type checkers, then tests. Feed failures back into the build agent repeatedly until everything passes, then final engineering review.
- **Consolidate and parallelise:** push linting/type-checking/testing/validation into dedicated agents ("scale compute to scale impact / add compute to add confidence"). Add planning agents. Separate searching (scout agent) from planning (plan agent).
- **Isolation:** first per-agent **work trees** (parallelism, agents don't trip over each other), then full **agent sandboxes** — every agent gets its own machine with complete isolation; the engineer can jump into a sandbox to inspect work, review, then merge and ship.
- **Kanban/ticketing as the wrapper:** input arrives from support, product, and engineers. Tickets are code-driven through states. Most teams need an engineer to translate tickets into mid/low-level prompts; advanced teams skip this once the org can write good prompts (the engineer's real job is meta-work on the agentic layer, not babysitting tickets). Code moves the ticket into planning → scout + plan agents → code updates the ticket → build agent → test agent loop → CI/CD (fail routes back to build) → out of the sandbox → engineer review → ship.

## Production-down scenario

Support files a ticket that goes straight to Slack/Teams. An engineer immediately prompts a **scout agent** routing into a **surgical hotfix agent** — a specialised agent with its own memory, templated to get the fix out ASAP, no optimisation, no doing it "fancy." Human-in-the-loop approval/rejection creates a single loop. On approval, multiple sandboxes run in parallel racing for the fastest working solution; passing/failing loops go back to the hotfix agent and engineer; the engineer validates and ships the fix. The question to every org: do you have an agentic workflow for production crashes?

## The software factory

At the top level, a routing/factory agent intakes tickets, marks them in progress, inspects the codebase, and routes each item (feature, bug, chore, hotfix) to a **specialised agent-sandbox workflow** at the right price/performance/speed — e.g., a chore gets one agent with a workhorse (even lightweight) model, while planners and scouts get state-of-the-art models. The best teams eventually drop engineering review because the system has proven itself. The core thesis: build **the system that builds the system**. Engineers do meta-work on the **agentic layer** (agents, prompts, skills, system prompts) and rarely touch the app layer — the app is for the agents. This is the opposite of vibe coding: agentic engineering is knowing your system so well you don't have to look; vibe coding is not knowing and not looking.

## Practical advice for building ADWs

1. **Keep it simple (KISS).** Start with the simplest workflow: a build agent plus a separate linter, run via an agent SDK (not a skill that makes the agent call code). On lint failure, feed results back to the build agent with the same session ID. Only then add a couple of nodes (type checker, linter) solving real problems. Separate agents and code — separation of concerns all the way through — because skill-based workflows still have the agent executing everything. Tests get massive validation problems at scale.
2. **Do it by hand first.** Run the workflow end-to-end yourself (using terminal agents is fine), step into each node, run each condition, watch each function, do the review, ship — then codify it as a combination of agents, engineers, and code. Design with pencil and paper or mermaid diagrams.
3. **Use agents AND code, not just agents.** Move skill work into code when you get serious about production: code wins on token cost, performance, reliability, and speed. You need a place for the results between steps (context engineering), and it takes time and feels like throwing it all in a skill — resist that. Classic engineering patterns (isolation, decoupling, single interfaces) matter even more now, and you must test each node transition (plan → build → status updates → testing → fail), because a correctly built ADW is multiplied hundreds or thousands of times.

## Key message

Don't optimise for loops — optimise for repeatable AI developer workflows: the right combination of engineers, agents, and code at the right time, at the right performance, price, and speed, executed tens to thousands of times with consistent results. The author's materials: agenticengineer.com's "tactical agentic coding" (8 lessons, 6 upgradeable, 30-day refund before lesson 4), "agentic horizon" (multi-agent orchestration, "agent experts" — specialised agents that beat out-of-the-box ones), and the free blog "thinking in threads."