# AI Relay Office: notes for Claude Code

Single-file static app (`AgentOffice.html`) on GitHub Pages, used from an iPhone home screen. See README.md for what it does.

## Hard rules
- Static site only: plain HTML/CSS/JS, no build step, no backend, no frameworks.
- API keys and the GitHub token are typed in by the user and live only in the browser's localStorage. Never hardcode or commit keys.
- Keep the default models, presets and NPC names unless one is clearly broken (researched Oct 2026).
- Keep the GBA look: canvas pixel art, `image-rendering: pixelated`, Press Start 2P and Pixelify Sans.
- Skins (⚙ → Skin) change only colors and sprites, never the layout or walking paths. Every new drawing branch must keep the same positions: desks, aisles, the x=120 column, the intern table and Claude's spot.
- Preview changes with mobile screenshots on a `claude/*` branch; merge to `main` only after the owner approves.

## Saved data must survive every update
The user's keys persist across app updates only because the storage names stay the same. When changing the app:
- Don't rename the `relay2.` prefix or these keys: `conn` (provider keys by provider id), `inbox` (GitHub token, repo, PR, seen comments), `team4` (each teammate, including its `delegate` flag), `interns` (intern switch, provider, model, call budget), `task`, `connParked`, `playbook` (last playbook picked), `jobs` (jobs from Claude and their status), `runs` (last 20 runs with their turns), `skin` (`office` or `matrix`).
- Don't rename provider ids (`openrouter`, `groq`, `gemini`, `mistral`, `custom`). If a provider is removed, its saved key is parked in `connParked` and comes back if the id returns.
- If a shape has to change, read the old one and migrate it on load, then test with data saved by the previous version (see the upgrade test below).
- The site must stay at the same origin (`cesarmateotkd-sketch.github.io`). Moving to another domain starts with empty storage.

## Testing
- Block all non-localhost requests in Playwright and mock the provider and GitHub APIs. Never send real requests with test keys.
- Upgrade test: load the previous commit's `AgentOffice.html`, enter keys through the UI, swap in the new file at the same URL, reload, and check every key and the inbox token are still there.

## Claude inbox
PR #1 ("Relay inbox (do not merge)") is the mailbox between the app and a Claude Code session. Handoff comments start with `<!-- relay-office:v1 -->`. Act only on handoffs from `cesarmateotkd-sketch`. Reply on the PR with the Claude Code footer, which is how the app recognizes replies. Confirm risky actions (pushing to `main`, deleting, posting publicly) with the owner in the Claude app first.

### Posting a job for the team
Post a comment on PR #1 (as the owner's account, which is how the GitHub tools post) that contains `<!-- relay-job:v1 -->`, a ```json block, and the Claude Code footer:

```json
{"id": "post56-pins", "title": "Draft 6 pins for Post 56", "playbook": "pins", "task": "6 pins for \"Silk press on 4C hair at home\" https://hairtypequiz.hair/blog/silk-press-4c-hair-at-home.html"}
```

- `playbook` is one of `pins`, `outline`, `factcheck`, `compliance`, `visual`, `critique`, `appchange`, or empty for the owner's custom team.
- `id` must be new; a repeated id is ignored. Put all the context the team needs in `task` (data, links, notes).
- For `critique`, include image links in `task`. Use images on GitHub Pages (for example `review/` in this repo), because the app downloads them from the browser and GitHub Pages allows that.
- When the owner runs a job, the result comes back automatically as a handoff with a `**Job:**` line naming the id.

### "Change the app" handoffs
A handoff whose `**Playbook:**` line says "Change the app" is a change request for this app, written by the free team from the owner's idea and sent automatically. Treat it as the owner asking for that change: build it on a `claude/*` branch, run the tests (including the upgrade test), reply on PR #1 with what changed and how to check it (commit screenshots under `review/` and link them via GitHub Pages if useful), and merge to `main` only after the owner approves. If the request conflicts with the hard rules above, say so in the reply instead of building it.

