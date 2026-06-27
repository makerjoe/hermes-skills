---
name: freedium-unlock
category: research
description: >-
  Unlock Medium member-only articles via freedium-mirror.cfd. Uses browser
  automation to paste the URL, click Unlock, and return the full article text.
  Fallback: opens the article URL in the user's local Chrome if freedium fails.
required_toolsets: [browser]
related_skills: [browser-automation]
---

# Freedium Unlock — Bypass Medium Paywall

Unlock Medium member-only articles by routing through freedium-mirror.cfd, a
working Medium paywall bypasser.

## Trigger

- You tried to read a Medium article directly or via r.jina.ai/web_extract and hit a paywall ("Member-only story")
- The user asks you to read a Medium article and says "usa freedium", "desbloquea", or "unlock"

## Requirements

- **Browser toolset** must be enabled in Hermes (`hermes tools enable browser`)
- Hermes cloud browser (`browser_navigate`, `browser_type`, `browser_click`, `browser_console`)
- No special auth needed — freedium-mirror.cfd is publicly accessible
- Hermes cloud browser may have bot detection; if freedium doesn't load:
  1. Retry with fresh navigation
  2. Open the article URL in the user's local Chrome via `cmd //c start "" "https://..."`
  3. Load `browser-automation` skill for local Chrome CDP setup with stealth

## Step-by-Step

### 1. Get the exact Medium article URL

Search for the article title or author in the user's context, Gmail, or session history.
Medium article URLs look like:
- `https://medium.com/@username/title-slug-XXXXXXXXXXXX`
- `https://medium.com/publication-name/title-slug-XXXXXXXXXXXX`
- `https://therightstuff.medium.com/vscode-ing-with-local-llms-365b7533ccdf`

### 2. Navigate to freedium-mirror.cfd

```python
from hermes_tools import terminal

# Browser approach (cloud Hermes browser):
browser_navigate("https://freedium-mirror.cfd/")
```

Wait for the page to render (it's a Svelte SPA).

### 3. Find the form elements

From the snapshot, locate:
- `textbox "Article URL"` (the input field)
- `button "Unlock"` (the submit button)

Ref IDs change per navigation — always take a snapshot first.

### 4. Type the URL and click Unlock

```python
browser_type(ref=eXX, text="https://medium.com/...")
browser_click(ref=eYY)
```

Wait ~3-5 seconds for the article to load.

### 5. Extract the article text

```python
# Get full article text
result = browser_console(expression="document.querySelector('article')?.innerText || 'Not found'")
# result is in result.result
```

### 6. Present to the user

Summarize or display the full text depending on how much the user wants.

### 7. For multiple articles

Navigate back via the "Go back" button on the article page (ref=e30 approx),
then repeat steps 3-6 with the next URL.

## Alternative: Opening in User's Local Chrome

If freedium-mirror.cfd fails (HTTP 000, redirect to lander, empty page):

```bash
cmd //c start "" "https://freedium-mirror.cfd/https://medium.com/..."
```

Or open the Medium article directly in the user's Chrome (they may be logged in):

```bash
cmd //c start "" "https://medium.com/@username/title-slug-..."
```

## Known URLs — Medium Mirror Sites (as of Jun 2026)

| Site | Status |
|------|--------|
| freedium-mirror.cfd | ✅ Working (Svelte SPA, form-based) |
| freedium.xyz | ❌ Domain for sale |
| freedium.site | ❌ 404 |
| readmedium.com | ❌ Next.js redirect loop |
| mdium.xyz | ❌ Dead |
| medium.bet | ❌ Redirect |
| freedium.cfd / .life / .one / .club | ❌ All dead |

## Pitfalls

> See `references/examples.md` for concrete unlocked-article examples from a real session (27 Jun 2026).

- **freedium-mirror.cfd uses client-side rendering** — browser_navigate returns before the page is fully rendered. Always allow 2-3 seconds before interacting.
- **Ref IDs change every navigation** — never hardcode them. Always browser_snapshot first.
- **Bot detection**: the Hermes cloud browser may trigger Cloudflare. If the page shows empty or a "Just a moment..." challenge, retry or abort.
- **Rate limiting**: opening too many articles in quick succession may trigger rate limits. Space requests 10+ seconds apart.
- **Article doesn't exist**: freedium returns a 404-style page. Check if the article title appears before extracting.
- **Cloud browser vs local Chrome**: the cloud Hermes browser may not have a user profile with Medium login. If freedium works but the article is still paywalled, try the local Chrome fallback with `browser-automation` skill.
- **Session cookies don't persist**: each `browser_navigate` to freedium-mirror.cfd starts fresh — no cached session. That's fine, no login needed.
