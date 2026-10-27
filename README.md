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

## Updating the app

- **Edit:** change `AgentOffice.html` (everything is in that one file) and push to `main`. GitHub Pages redeploys in about a minute.
- **Pick up the update on your phone:** close the home-screen app fully (swipe it away) and reopen it. If it still shows the old version, open the site in Safari and pull to refresh, then reopen the app. Your keys and team stay, because they live in `localStorage`, not in the code.
- **Models:** default models, presets and NPC names are in the `PROVIDERS` and `PRESETS` objects near the top of the script. Free model names change often. If a teammate starts failing with "model name may be wrong", tap its Model field and pick a current one.
- **Icons:** `icons/icon-180.png` (apple-touch-icon) and `icons/icon-512.png` (manifest). If you replace them, give the new files new names, or iOS may keep showing the old icon.

## Files

- `AgentOffice.html`: the whole app
- `index.html`: redirects the site root to the app
- `manifest.json`, `icons/`: home-screen install
