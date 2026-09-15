# Zen van Riel — Local AI Coding Workflow (2026)

[Link to video](https://www.youtube.com/watch?v=3zSANOIBHYw)

## Setup
- Linux machine with an RTX 5090 (32 GB VRAM) runs the models; a MacBook is the main dev environment.
- Models used:
  - **Qwen 3.5 Coder** (35B params, mixture-of-experts) — fast (~100–140 tok/s) on GPU, but doesn't fully fit in VRAM, so some layers spill into system RAM, hurting performance.
  - **Qwen 3.5** (general model) — fits entirely on GPU, used later for better speed at the cost of some coding skill.

## Key lessons on model placement
- Fitting a model by offloading params to system RAM doesn't mean it's usable — data shuffling between RAM and VRAM is slow.
- Agentic coding needs very large context windows where compute cost scales exponentially, so only models that truly fit on GPU at acceptable speeds are practical.

## LM Studio Link (cross-device)
- LM Studio's linking feature exposes an encrypted connection between devices, letting the MacBook use the model "locally" even though it runs on the Linux GPU.
- Setup: log into LM Studio on both machines, browse to Linking on the MacBook, the Ubuntu machine appears, select the loaded model as a linked model.

## Connecting Claude Code to LM Studio
- LM Studio exposes multiple API endpoints: the LM Studio chat API, an OpenAI-compatible endpoint, and an **Anthropic-compatible endpoint** (`/v1/messages`) — the relevant one for Claude Code.
- The presenter had Claude Code research its own setup and export two environment variables that override the Anthropic base URL and API key, pointing Claude Code at the local LM Studio API.

## Important caveats when using local models with Claude Code
- **Slow responses**: Claude Code injects a huge system prompt (thousands of tokens), so "hello" takes a long time — local model speed in an empty chat is misleading. Most promo videos miss this; it gets worse as the repo grows. Mitigations: customize the prompt, or use a leaner CLI provider.
- **Model identity confusion**: Because the system prompt says it's Claude, the Qwen model answers "I'm Sonnet" — models believe what the system prompt tells them.
- **Context window limit**: With LM Studio's default 4,000-token context, requests hang indefinitely (no clear error) since the Claude Code system prompt alone exceeds it. Fix: raise context to ~80k–200k tokens.
- **Context overflow behavior**: LM Studio can be configured (e.g., truncate the middle of conversation history) to keep chatting past the limit; Claude Code may also auto-summarize history.

## Agentic workflow for building an app
- Goal: build a full-stack app (Next.js + TypeScript) as a dashboard that proxies requests from the frontend to the LM Studio API, mimicking LM Studio's "loaded models / server health" UI.
- The LM Studio REST API documentation was pasted into the conversation to ground the model on the real API.
- Used **plan mode** with the local model → it explored the (empty) codebase, asked clarifying questions, and produced 7 spec files (~65k tokens of planning).
- Implementation ran in **bypass-all-permissions mode inside a dev container** so work could proceed unattended; accepted slower speeds (200k-token context) since speed didn't matter.
- Key recommendation: **explicitly ask for sub-agents per task** — fresh Claude Code instances with clean context windows report back to the main agent, stretching the limited local context window much further.

## Debugging / realism
- Local models made up details (e.g., a hardcoded "Nvidia RTX 3080") and produced several bugs; states that the same iterative bug-fixing applies even to top cloud models, but local models are weaker and produce more bugs.
- Powerful technique: **let the AI agent call the backend APIs it's integrating with**, so it can self-assess its own API calls and align output formats with the code it writes.
- Final result: a working dashboard showing the real models loaded in LM Studio (verified the Qwen 3.5 model), with some remaining rough edges (e.g., hardcoded 256k context window). The model was running on the Linux box via LM Studio Link, not on the MacBook.

## Verdict
- The workflow is much more powerful than anything possible two years ago, but still not equivalent to top cloud models (e.g., Claude Opus).
- Recommended for privacy enthusiasts; local AI coding has never been better.