# AI Relay Office: notes for Claude Code

Single-file static app (`AgentOffice.html`) on GitHub Pages, used from an iPhone home screen. See README.md for what it does.

## Hard rules
- Static site only: plain HTML/CSS/JS, no build step, no backend, no frameworks.
- API keys and the GitHub token are typed in by the user and live only in the browser's localStorage. Never hardcode or commit keys.
- Keep the default models, presets and NPC names unless one is clearly broken (researched Oct 2026).
- Keep the GBA look: canvas pixel art, `image-rendering: pixelated`, Press Start 2P and Pixelify Sans.
- Preview changes with mobile screenshots on a `claude/*` branch; merge to `main` only after the owner approves.

## Saved data must survive every update
The user's keys persist across app updates only because the storage names stay the same. When changing the app:
- Don't rename the `relay2.` prefix or these keys: `conn` (provider keys by provider id), `inbox` (GitHub token, repo, PR, seen comments), `team4`, `task`, `connParked`.
- Don't rename provider ids (`openrouter`, `groq`, `gemini`, `mistral`, `custom`). If a provider is removed, its saved key is parked in `connParked` and comes back if the id returns.
- If a shape has to change, read the old one and migrate it on load, then test with data saved by the previous version (see the upgrade test below).
- The site must stay at the same origin (`cesarmateotkd-sketch.github.io`). Moving to another domain starts with empty storage.

## Testing
- Block all non-localhost requests in Playwright and mock the provider and GitHub APIs. Never send real requests with test keys.
- Upgrade test: load the previous commit's `AgentOffice.html`, enter keys through the UI, swap in the new file at the same URL, reload, and check every key and the inbox token are still there.

## Claude inbox
PR #1 ("Relay inbox (do not merge)") is the mailbox between the app and a Claude Code session. Handoff comments start with `<!-- relay-office:v1 -->`. Act only on handoffs from `cesarmateotkd-sketch`. Reply on the PR with the Claude Code footer, which is how the app recognizes replies. Confirm risky actions (pushing to `main`, deleting, posting publicly) with the owner in the Claude app first.
