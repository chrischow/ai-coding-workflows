# My Real AI Coding Workflow - Tech With Tim

[Link to video](https://www.youtube.com/watch?v=gpOfsGW1xRk)

Summary of Tech With Tim's video where he builds an **AI shorts tool** completely from scratch with AI — showing his real (messy, pragmatic) process: environment setup, prompting, tools, and debugging "issues that are bound to happen when coding with AI." Central theme: **the tooling and planning you set up beforehand are what make the difference** — the final app was built with ~5–10 prompts, but only because the environment was pre-configured.

## Phase 1: Planning & ideation (do this before any code)

- **Start with the idea, then research** — most people skip this and get poor results. Spend time up top: research alternatives, see what already exists, pick the tech stack and tools, write a high-level outline.
- **Use AI for the research too** — he built a research slideshow with Claude Code, dumping in his idea and going back and forth with questions to figure out tools, flow, and what the app should look like. This research happens in a **separate window from the code editor** (Claude Code chat), because it's meant to be saved and referenceable across devices.
- **Know your tool capabilities before you start** — he read the docs for each tool first, so he knows what to ask the model to do (e.g. ImageKit can reframe on the face, resize/crop/transform, burn captions; Groq has Whisper large-v3-turbo). "Because I have that baseline context... I can better inform the AI model."

### His demo app & stack
- **App**: drop in a long 16:9 YouTube video → AI generates multiple 9:16 shorts (good moments, captions, face-reframed).
- **Whisper (via Groq)** — audio transcription (note: Groq with a Q, not Grok — easy spelling mistake to catch).
- **Claude (Sonnet)** — picks the clip moments, writes titles and captions.
- **ImageKit** — actual video generation/processing: face reframing, captions, format conversion, upload/download, streaming. Free tier (max 100MB uploads; Pro = 2GB).
- **Next.js web app**.

## Phase 2: Cursor IDE setup

- Open a folder, select the frontier Claude model in the agent window (Claude Opus at the time, since Sonnet 4.5 wasn't available).
- Model settings: **fast mode** on, **thinking mode** on, **1M context window**, effort at **medium** ("the sweet spot" — higher takes much longer and costs more).
- Prefers the classic agent view where he can **see the files being written** over the new chat-only agents view.
- **Why Cursor**: same models as Claude Code/Codex, but his personal experience is that Cursor's coding harness "performs a lot better." Recommends switching to faster models (GPT 5.5, Composer 2.5) for large refactors/repeated mistakes or quick UI tweaks.
- Uses **Whisper Flow** (free voice-to-text dictation, works natively in Cursor and can tag files) to dictate prompts — speaking is much faster than typing.

## Phase 3: Provide initial context

- Dictate a long first prompt covering: the goal, the product idea, and **all decisions already made** (Next.js web app, which tools, which API keys will be provided).
- Ask the model to create a **high-level plan / architecture document** and to **interview you with questions before proceeding** (Cursor has a built-in UI for answering them). The output lives as markdown in the repo, so any future chat / parallel agent can return to the initial context and know what the app is and what's already been decided.

## Phase 4: Agent skills & MCP servers (the key step)

- Almost every modern tool (ImageKit, Firecrawl, Think, etc.) ships **agent skills + MCP servers** so the model understands how to use it — no copy-pasting docs or sharing URLs.
- He installed **ImageKit's agent skills** via a terminal command (Cursor picks them up automatically, feeding the model's context) and **added the ImageKit MCP server** to `cursor MCP.json` (Cursor can add the servers itself when asked), then authenticated by pasting his private key.
- Also added the **GitHub MCP server** so the model can create repos.
- Result: the model can manage the ImageKit account directly (upload, transforms `?query-params` on the video URL, audio extraction, adaptive bitrate streaming) without the developer reading docs during the build.

## Phase 5: Add persistent rules

- Cursor's **rules** feature: always injected into the prompt for every session. Example rule added: *"commit after any major changes automatically"* — so the model handles git commits on its own and you can roll back. He also had it create the GitHub repository and init git up front.

## Phase 6: Build & debug loop (the reality of AI coding)

- Fill in API keys in `.env.local`, then kick off the build — optionally using Cursor's **multi-agent/multitask mode**. Caveat: "sometimes it just goes crazy" — only worth it when planning/setup was done well.
- **Debugging approach**: copy/paste the error message *plus context* ("the upload worked and the video is in ImageKit, but transcription is failing — can we debug this?"), so the model knows which phase works and which doesn't.
- **Interject immediately when the model drifts**: he caught it trying to send the whole video to Groq for transcription and corrected it — extract pure audio first via ImageKit's URL transformations (much smaller, fast, and ImageKit handles it).
- **Use screenshots for UI bugs**: even though vision models can see the UI, he highlights/photographs the specific problem (e.g. "the transcription works but we just have the original video showing five times — actually generate the five transformed clips with captions, face reframing, 9:16, in parallel").
- Real bugs hit in the demo: transcription phase failing, clips rendering as the original video instead of transformed versions, missing burned-in captions, captions positioned too low.

## Key takeaways

- **The upfront investment is what makes it fast**: deep research/decisions → persistent context docs → skills/MCP/rules setup → then the build itself was only ~5–10 prompts. Without the tooling and planning, the project "would have taken significantly longer and we may not have even gotten a completed project."
- **Your knowledge of the tools guides the model** — reading docs is how you know the right corrective instruction to give mid-build.
- **Expect and accept back-and-forth** — mistakes, wrong directions, and small errors are just "the reality of coding with AI"; read the model's tool calls to catch wrong paths early.
- Finished state: clips generated in parallel from ImageKit, face-tracking reframing, thumbnails, burned-in captions — a working web app, saved to GitHub throughout.