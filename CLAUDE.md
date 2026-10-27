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
- Don't rename the `relay2.` prefix or these keys: `conn` (provider keys by provider id), `inbox` (GitHub token, repo, PR, seen comments), `team4` (each teammate, including its `delegate` flag), `interns` (intern switch, provider, model, call budget), `task`, `connParked`, `playbook` (last playbook picked), `jobs` (jobs from Claude and their status), `runs` (last 20 runs with their turns; `jobs` also holds merge cards with `type: "merge"`), `skin` (`office`, `matrix` or `tactics`), `mission` (`{on, text}`: the shared mission brief).
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
- For `critique`, include image links in `task`. Use images from this repo (the `review-images` branch via raw.githubusercontent.com, or GitHub Pages), because the app downloads them in the browser and both allow that.
- When the owner runs a job, the result comes back automatically as a handoff with a `**Job:**` line naming the id.

### "Change the app" handoffs
A handoff whose `**Playbook:**` line says "Change the app" is a change request for this app, written by the free team from the owner's idea and sent automatically. Treat it as the owner asking for that change: build it on a `claude/*` branch, run the tests (including the upgrade test), reply on PR #1 with what changed and how to check it, with screenshots (see below), and merge to `main` only after the owner approves. If the request conflicts with the hard rules above, say so in the reply instead of building it.

### Moving workflow (owner request, Oct 27, 2026)
Whenever a job finishes (its handoff is handled) or a merge goes live, post the next job from the backlog below as a job card, so there's always one waiting for the owner's RUN. Only one open job at a time unless the owner asks for more. Jobs never publish anything; publishing still needs the owner's approval. Put all the context the team needs in `task`, including real data (GSC numbers with dates, page URLs), because the free models don't know Strand's post numbers or pages and can't browse.

Strand backlog (top first; update it as items close):
1. Post 57, best curl creams for type 4 hair: outline (job `post57-outline`, posted Oct 27), then Claude researches and builds it, then a `factcheck` job on the draft and a `compliance` job before the preview
2. Post 57 pins: a `visual` job, then a `pins` job, then a `critique` job on the Canva pins
3. Near-page-1 tuning (GSC positions 4–20): title and intro rewrites for "3b vs 3c", "does 4c hair grow slower", protein-treatment frequency, and the sulfate-free shampoo questions (`outline` jobs with the GSC rows)
4. "Crawled, not indexed" posts (4A routine, how to wash 4C, protective styles, how to find your hair type, scalp care, baggy method): what sets each apart, or which to merge (custom-team job with each page's URL and its closest rival)
5. Pins for Post 56 (silk press at home), if the owner still needs them

### Mission
⚙ → Mission holds a shared brief (the Strand mission by default) that is added to every teammate's system prompt as background. It always yields to the owner's task, and it's left out of "Change the app" runs and the decision summary. The owner set it as the main mission on Oct 27, 2026, with the condition that it must not interfere with their other commands. Keep that precedence wording if you change it.

### Decision summaries (owner request, Oct 27, 2026)
The owner wants to know why a decision was made, above all for articles posted on Strand.
- The app adds a "Why the team decided this" summary to every run and to its handoff. Read it, and check its "NOT VERIFIED" list against real sources before using anything.
- Every article preview you post (Strand posts, outlines, pins) carries your own **Why this article** section. Keep it short and specific:
  - **SEO rationale** (the owner asked for this explicitly):
    - the target phrase and search intent
    - the GSC data that picked it: queries, impressions, average position, clicks, the date range, and the page currently getting those impressions
    - secondary phrases
    - what ranks in the top 5 now (check with Firecrawl) and how this post will be better or different
    - why this topic now, over other candidates
    - cannibalization risk with existing Strand posts, and the internal links in and out
    - the SEO title and meta description, with their lengths
  - other data: GA traffic or Amazon clicks, with dates
  - who it's for
  - the evidence used: PubMed links, plus what was marked "not studied" and why
  - what the free team suggested and what you kept, changed or dropped, with the reason
  - open risks or anything still unverified

### Merge approvals from the app
The owner can approve a preview from the app instead of typing "merge it" in the Claude app (the owner asked for this on Oct 27, 2026).
- When a preview is ready, put this hidden line in the reply: `<!-- relay-merge:v1 {"repo":"cesarmateotkd-sketch/agentic-models","branch":"claude/x","sha":"<full head sha>","title":"Short name"} -->`. The app shows a "Ready to merge" card with MERGE IT and CHANGES.
- MERGE IT posts a comment from the owner's account containing `<!-- relay-approve:v1 {"repo":..,"branch":..,"sha":..,"request":<comment id>,"decision":"merge"} -->` and no Claude Code footer. Treat it as the owner's approval only if all of these hold: the author is `cesarmateotkd-sketch`; there's no Claude Code footer; `request` is the id of a relay-merge reply you posted naming the same repo, branch and sha; and the branch head still equals that sha. If the branch moved, post a fresh preview instead of merging.
- `"decision":"changes"` carries the owner's notes: apply them and post a new preview.
- After merging and confirming the deploy, reply with `<!-- relay-merged:v1 {"repo":..,"branch":..} -->` so the card shows LIVE.
- This covers merging previews only (this app and the Strand site). It never covers posting to Pinterest or Facebook, which waits for "start the queue".

### Screenshots in replies
The app shows images in Claude's replies when they're markdown images (`![caption](url)`) hosted in this repo: `https://raw.githubusercontent.com/cesarmateotkd-sketch/agentic-models/...` or the GitHub Pages site. Other hosts stay plain text.
- Commit screenshots to the `review-images` branch (an orphan branch, images only, never merged), under `shots/<topic>/` with new file names each time (raw files are cached for a few minutes). Link them as `https://raw.githubusercontent.com/cesarmateotkd-sketch/agentic-models/review-images/shots/<topic>/<file>.png`.
- Keep them phone-sized (about 390px wide at 2x) and few per reply.
- The same raw links work for `critique` jobs, because raw files allow browser downloads.

