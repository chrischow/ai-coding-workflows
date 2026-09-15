# Charlie Holtz — AI Coding Setup (Conductor)

[Link to video](https://www.youtube.com/watch?v=fQmlML9Lay4/n)

**Who:** Charlie Holtz, co-founder of **Conductor** (YC Summer 24) — an app that orchestrates multiple coding agents on a Mac. The headline: he builds **Conductor with Conductor** — "using conductor to build conductor" — and spends most of his day in it.

## The Setup & Daily Workflow

- **Physical setup:** A $20 gooseneck microphone from Amazon. Everyone in the open-plan office got one to encourage **talking to computers** more — you can lean over and whisper "merge PR 3475" without disrupting the room.
- **Constantly kicking off tasks:** Cmd+N opens a new task; he speaks into his computer, e.g. *"take a look at the latest Linear issue and give me a rough pass at how you'd solve it"*. Tasks run in the sidebar while he switches to other chats.
- **Keyboard-shortcut heavy:** Everything gets a shortcut (e.g. Cmd+Shift+Y) which explains why so many apps now copy the "open in" button pattern he helped popularise. He reviews PRs inside Conductor, leaving GitHub-style comments ("this looks a little weird to me — why do we need this?") which kick off new agent runs.
- **Experimentation-first:** He constantly spins up throwaway workspaces to try ideas — four PRs in review, plus a pile of in-progress ideas that "may never see the light of day". Winners get **promoted** (random idea → internal setting → experimental setting).
- **Conductor "on the go":** Speaks into his phone ("let's add a new feature where I can change the theme to hacker mode"), clicks conduct, and his Mac starts working on it remotely.
- **Status tracking / CEO mode:** Left sidebar shows status (in progress → PR created → merged into "done" folder). A new **dashboard page** shows what all agents are working on in one place so he can advance them to the next action. Goal: *"feel like the CEO of a little company"* — agents bring digestible reports, you redirect or merge.

## Caveman Mode & Manual Editing

He almost never writes code by hand. "Caveman mode" (typing with a keyboard into a file) exists but is a last resort — for occasional Tailwind class tweaks or editing an ENV file. Most small edits are done by **highlighting code and telling the AI** about comments, or just speaking at the screen ("that button looks a little too wide — can you make it smaller?").

## Other Apps & Customisation

- **Telegram** — talks to his **OpenClaw** agent (recent addition).
- **Spokenly** — text-to-speech on Ctrl+Space, running a local model (**Parakeet**). His Mac has 128GB RAM partly to run local models; he's ordered a bottom-of-the-line MacBook to force himself to work on lowest specs.
- **CLAUDE.md & skills files:** Hundreds of lines of curated engineering practices, e.g. *"We're a startup. You're probably used to writing enterprise code but that's not how we do things around here."*
- **Settings he stands by:** Always **fast mode** (not the default, but required for token-maxing), **Context 7 MCP** for documentation, and running Claude with **dangerously accept all permissions** — not the default, but the standard way to run in Conductor.

## "Slop-Free" Zones

Clear boundaries between human-written and AI-written code/docs. AI *may* contribute to slop-free zones, but **every line must be read by a human** — including literal comments like "do not touch if you are an AI, this is for human eyes only". This prevents a vicious cycle where the AI sees bad code and writes worse code, and it also preserves positive examples.

## Tech Stack

Tauri app using the native Safari web renderer; backend is technically Rust but **~90–95% TypeScript**. The web app is **Elixir (Phoenix)** — tiny (only login) — and he pushes for more Elixir whenever he can.

## Philosophy: Don't Let the AI Be Your Architect

- Even the **workspace abstraction** (a work-tree wrapper) and the **UI layout** (chats left, conversation middle, review sidebar right) were human-thought-through decisions. AI-made UI "doesn't feel crafted", and crafted feel is core to Conductor.
- Ideal architecture: the **core built on human-written APIs and contracts** the AI doesn't touch, with **large free-reign chunks** where the AI can soak up tons of ideas without endangering infrastructure. He admits the current boundaries are still murky.
- **Enforced workflows:** When they launched, feedback was "this is crazy — I can barely manage one agent". They deliberately made it impossible to edit files directly: a workspace must be a work tree → create a PR → you must merge it.
- **Staying ahead:** They push past comfort zones on purpose. Why so much work on cloud right now? Laptops stop agents when closed, but the world is moving to agents that run **10× longer and are 10× smarter** in environments not constrained by local CPU.
- **Conviction build:** Not analytics or A/B testing — they just **use the product every single day** and judge by gut feel ("when I click this, it feels right that it opens in the center").

## Codex vs Claude Code

- **Codex = the workhorse**: powers through a specific problem, unafraid of a ton of tool calls, will debug with you for a long time. Reaches for it when it's time to "get stuff done".
- **Claude/Opus = the partner**: more back-and-forth, more creative. Reach for it instinctively when **building out a new feature**.

## On Terminals

Terminals were replaced by GUIs in the 80s for a reason — *"humans are spatial visual creatures"*; CLIs suit AI brains better than human brains. You want to know chats are "over here" and the review panel is "over there", and there's a lot you simply can't do in a terminal.

## Token Maxxing

- **High water mark:** July 2025, **$22,000 on tokens in one month** (previous-gen models), with tens of thousands of lines of code that month.
- Big on **spending** — fast mode, "think extra hard", high effort — but **not** on lines of code; he keeps code minimal to avoid the codebase spiralling out of control. He thinks differently about a greenfield app versus an established codebase like Conductor.

## Workflow Changes (6 Months Ago → Now)

- Used to open an IDE and hand-fix hard PRs; used GitHub's web app heavily. Now reviews code changes and comments **inside Conductor**, and a recent **checks tab** pulls GitHub PR-check comments into Conductor (there are lots of PR checks running).

## Surprising Things People Have Done

- Someone built a **mobile version of Conductor** by spoofing IPC calls to the desktop app.
- **Gary** (GStack) taught him how hard you can go on **skills** — first-class citizens in GStack, interesting for onboarding. Conductor added **"Gary mode"**, which by default doesn't collapse tool calls (default is collapsed) — and shows Gary's face.

## What Feels Obvious to Them (But Not the World Yet)

- **Human–AI collaboration** is underexplored: talking to sub-agents, multiplayer chats where several people work with the AIs on the same thing, and the **orchestra metaphor** — you conduct at the orchestra level, zoom in to whisper to the out-of-tune trumpet, zoom out to the string section.
- **Code is becoming sawdust**: you no longer craft the code itself — you describe what you want and how it should be built, and code is just the sawdust left behind. **Your prompts are what matter**: when the next model generation lands, you rerun your prompts and get new code; the old code never really mattered.
- **Malleable software**: their prompt-request feature is an early experiment. The metaphor is **Call of Duty modding** — the game's skeleton is the same for everyone, but you mod skins/reload speeds to make it *feel yours*. He expects software to go the same way: crafted structure, moddable by the user.