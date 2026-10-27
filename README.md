# AI Relay Office

A single-file mobile web app that runs a small team of AI agents on free model providers, dressed up as a Game Boy Advance game. You type a task and press **A**. Each teammate is a pixel NPC who walks from their desk to the whiteboard to take a turn:

- **Helpers** add input (a plan, ideas, fact checks).
- **Builders** write the deliverable and revise it after feedback.
- **Judges** reply `APPROVED` on the first line, or list what to fix.

The relay repeats for up to *Max rounds*. It stops early once every judge on duty that round has approved the latest draft. The final version goes at the bottom of the log with a **COPY FINAL** button. Tap any NPC to read what they last said. Press **B** to stop a run. The Team editor is locked while a run is in progress.

It's plain HTML, CSS and JS with no build step and no backend. The app calls each provider straight from your browser.

## Your keys stay on your phone

You paste API keys under **Connections**. They're saved only in your browser's `localStorage` and are sent only to the provider they belong to. They are never in this repo. Clearing Safari's website data deletes them.

## Getting free keys

| Provider | Where | Notes |
|---|---|---|
| **OpenRouter** | [openrouter.ai/settings/keys](https://openrouter.ai/settings/keys) | Sign in, then **Create Key**. Models ending in `:free` cost nothing but are limited to about 50 requests a day, so give them teammates that only speak in round 1. |
| **Groq** | [console.groq.com/keys](https://console.groq.com/keys) | Sign in, then **Create API Key**. Fast, with generous free daily limits. |
| **Google Gemini** | [aistudio.google.com/apikey](https://aistudio.google.com/apikey) | Sign in with a Google account, then **Create API key**. Uses the AI Studio free tier. |
| **Mistral** | [console.mistral.ai/api-keys](https://console.mistral.ai/api-keys) | Choose the free **Experiment** plan (phone verification), then create a key. Prompts on the free plan may be used for training. |
| **Custom** | — | Any OpenAI-compatible API. Enter its base URL (ending in `/v1` or similar) and a key if it needs one. |

After pasting a key, tap **TEST**. "WORKING" means the app could list that provider's models. Tap a teammate's Model field to pick from that list.

## Install on iPhone

1. Open the site in **Safari**.
2. Tap **Share**, then **Add to Home Screen**.

It opens full screen with its own pixel icon. The site root (`index.html`) redirects to `AgentOffice.html`.

## Playbooks, jobs and history

**Playbooks** (the Task box) are ready-made setups for Strand's repeat work. Each has its own team, task template and a judge that checks a pass/fail list against Strand's rules:
- **Pin batch:** 5–7 pins with different angles, titles (keyword in the first 40 characters), descriptions, overlay text and alt text.
- **Post outline:** SEO title and meta description, structure, quick picks, an evidence plan (each claim with a study or "not studied"), FAQs, internal links and where the disclosure goes.
- **Fact-check:** every claim with a verdict and how to verify it. Claude checks the flagged ones against PubMed before anything is published.
- **Compliance check:** FTC disclosure, Amazon Associates, Pinterest and Meta ad rules, one line per rule.
- **Visual brief:** 3 pin concepts with a Canva prompt, in the Strand palette.
- **Pin critique (image):** paste image links and Gemini reviews the actual pins.
- **Change the app:** describe what you'd like the app to do differently. The team writes a clear change request and sends it to Claude automatically. Claude builds it on a preview branch and replies with how to check it; it only goes live after you approve.
- **Custom team:** the team you set up under ⚙.

If a playbook's provider has no key, that teammate moves to one you've connected, so one key is enough.

**Jobs from Claude** appear at the top once the Claude inbox is connected. When Claude finishes a preview, a **Ready to merge** card appears there too: tap **MERGE IT** to put it live, or **CHANGES** to send notes. Tap **RUN**; when the team finishes, the result goes back to Claude automatically.

**Why the team decided this:** every finished run ends with a short summary of why it ended where it did, built only from what the team said. It covers what was decided, why (who raised what), how it was checked, and, for article playbooks, an SEO rationale: target phrase and intent, the data behind the choice, secondary phrases, why this topic now, and overlap with existing posts. Post outlines must include that rationale, and the judge checks it. A NOT VERIFIED list names any claim nobody confirmed. It's saved in History and sent to Claude with the result.

**History** keeps your last 20 runs, saved step by step, so locking the phone or closing the app doesn't lose work. If the phone locks during a model call, the app retries that call once when you come back.

**⚙ (top right)** holds the skin, your keys (Connections) and the custom team (Team).

**Skins:** *Office* (classic) or *Matrix*: same layout, green code rain, agents in suits and sunglasses (their tie shows their role), and Neo, which is Claude, always in the room. The narrator becomes the Operator, and A/B are the red and blue pills. *Tactics (PSX)*: a stone war room with stained glass, a war map, torches and a crystal. Knights judge, Black Mages write and White Mages help, Claude is a blond hero in blue, the menus are parchment, and A/B become ○ and ×.

Characters are 16×20 shaded pixel sprites with front, back and side views. They take a 4-step walk with arm swing, blink and breathe while idle, write on the board during their turn, and jump when the team is approved.

If a playbook's writer fails (wrong model name, rate limit), it tries once more on another connected provider. Judges wait until there's a draft, and a run with no draft sends nothing to Claude.

## Interns (teammates delegating work)

Interns are **off by default** (Team → Interns → Use interns), because three at once can hit Groq's free per-minute limit. When on, a teammate with **Can hand work to interns** checked may split its turn into up to 3 sub-tasks. Small intern sprites run to the side table next to the whiteboard and work at the same time. The teammate then writes its turn using their notes, which appear in the log under "INTERNS HELPED". Interns can't hire interns of their own.

- **Interns use** (under Team → Interns) sets the interns' provider and model. The default is Groq GPT-OSS 120B, which is fast and has generous free limits. If that provider has no key, interns use the teammate's own model.
- **Max calls per run** (default 30) caps how much delegation a run can do. Past it, teammates answer on their own.
- Delegation starts off for OpenRouter teammates, because its free models allow only about 50 requests a day.
- Each delegated turn costs 2 calls for the teammate plus 1 per intern. The status line shows the total calls when a run ends.

## Sending results to Claude Code (Claude inbox)

The app can hand a finished run to a Claude Code session. Each handoff goes in as a comment on a GitHub pull request the session is subscribed to: [PR #1, "Relay inbox (do not merge)"](https://github.com/cesarmateotkd-sketch/agentic-models/pull/1). Claude does the work and replies on the PR. The app checks for replies every 20 seconds while it's open, and the CLAUDE NPC walks to the board to deliver each one.

**One-time setup**
1. In Safari, go to github.com → Settings → Developer settings → Personal access tokens → **Fine-grained tokens** → **Generate new token** ([direct link](https://github.com/settings/personal-access-tokens/new)).
2. Set **Repository access** to *Only select repositories* → `agentic-models`.
3. Under **Repository permissions**, set **Pull requests** to *Read and write*. Leave everything else off.
4. Pick an expiry (90 days is fine), generate, and copy the token.
5. In the app, open **Connections → Claude inbox**. Repo and PR are prefilled. Paste the token and tap **TEST**.

**Use it:** after a run, type an optional note for Claude under the final result and tap **SEND TO CLAUDE**. Claude's replies can include screenshots (from this repo only), shown in the log; tap one to see it full size.

**Connecting a new Claude Code session:** only one session listens at a time, and sessions end after a while. Start a new one and tell it: *"subscribe to the relay inbox PR in cesarmateotkd-sketch/agentic-models"*.

**Safety**
- The app only shows replies posted from your account that end with the Claude Code footer. The repo is public, so anyone else's comments are ignored.
- Claude only acts on handoffs posted from your account.
- Risky steps (pushing to `main`, deleting things, posting publicly) still get a yes from you in the Claude app.
- The token can only read and comment on pull requests in this one repo.

## Where your keys are saved

Keys and the GitHub token are saved in the browser on your phone, and they survive app updates. Two iPhone details:
- **The home-screen app and Safari keep separate storage.** Keys typed into the site in Safari don't appear in the home-screen app, so enter them in the app itself.
- **Deleting the home-screen icon, or clearing Safari's website data, erases them.** Keep your keys in a password manager so you can paste them back.

## Updating the app

- **Edit:** change `AgentOffice.html` (everything is in that one file) and push to `main`. GitHub Pages redeploys in about a minute.
- **Pick up the update on your phone:** close the home-screen app fully (swipe it away) and reopen it. If it still shows the old version, open the site in Safari and pull to refresh, then reopen the app. Your keys and team stay, because they live in `localStorage`, not in the code.
- **Models:** default models, presets and NPC names are in the `PROVIDERS` and `PRESETS` objects near the top of the script. Free model names change often. If a teammate starts failing with "model name may be wrong", tap its Model field and pick a current one.
- **Icons:** `icons/icon-180.png` (apple-touch-icon) and `icons/icon-512.png` (manifest). If you replace them, give the new files new names, or iOS may keep showing the old icon.

## Files

- `AgentOffice.html`: the whole app
- `index.html`: redirects the site root to the app
- `manifest.json`, `icons/`: home-screen install
