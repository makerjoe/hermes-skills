# Freedium Unlock — Real Session Examples

## Session: 27 Jun 2026

All three articles from a Medium Daily Digest email were successfully unlocked
via freedium-mirror.cfd using the Hermes cloud browser.

### Article 1: "The OpenCode revolution: more than just another chatbot"

| Field | Value |
|-------|-------|
| Author | Fabio Matricardi |
| Published | Jun 9, 2026 |
| Read time | 10 min |
| Original URL | `https://medium.com/artificial-intel-ligence-playground/the-opencode-revolution-more-than-just-another-chatbot-21706215701f` |
| Freedium URL | `https://freedium-mirror.cfd/https://medium.com/artificial-intel-ligence-playground/the-opencode-revolution-more-than-just-another-chatbot-21706215701f` |
| Extracted | ~14,035 chars via `document.querySelector('article').innerText` |

Key topics: OpenCode as an autonomous coding agent (not just chatbot),
analogy "chatbot = GPS, OpenCode = autopilot", 3 interfaces (CLI, Desktop,
Web), 75+ AI providers, privacy with local models.

### Article 2: "VSCode-ing With Local LLMs"

| Field | Value |
|-------|-------|
| Author | Adam Fisher (@therightstuff) |
| Published | Jan 24, 2026 |
| Read time | 3 min |
| Original URL | `https://therightstuff.medium.com/vscode-ing-with-local-llms-365b7533ccdf` |
| Freedium URL | `https://freedium-mirror.cfd/https://therightstuff.medium.com/vscode-ing-with-local-llms-365b7533ccdf` |

Key topics: Ollama + Qwen3-Coder:30B on M2 MacBook (32GB), 16K context,
OpenCode custom provider config in `~/.config/opencode/opencode.json`,
battery drain issue (2-2.5h), Plan mode bug.

### Article 3: "[Free] How to run Claude Code for free using OpenCode Zen and Minimax M2.5"

| Field | Value |
|-------|-------|
| Author | Nitin Gavhane |
| Published | May 28, 2026 |
| Read time | 4 min |
| Original URL | `https://medium.com/@nitingavhane/free-how-to-run-claude-code-for-free-using-opencode-zen-and-minimax-m2-a7fee3f0a547` |
| Freedium URL | `https://freedium-mirror.cfd/https://medium.com/@nitingavhane/free-how-to-run-claude-code-for-free-using-opencode-zen-and-minimax-m2-a7fee3f0a547` |

Key topics: Claude Code + OpenCode Zen provider + Minimax M2.5 free,
no GPU/setup needed, config via `ANTHROPIC_BASE_URL` pointing to
opencode.ai/zen, benchmarks comparable to Claude Opus 4.6.

## Workflow Notes

- freedium-mirror.cfd is a Svelte SPA — `browser_navigate` returns before
  the form is rendered. Always snapshot to get current ref IDs before typing.
- After clicking "Unlock", the article page loads in 2-4 seconds.
- Extraction via `browser_console(expression="document.querySelector('article')?.innerText")`
  returns the full article text including headings, code blocks, and metadata.
- The "Go back" button on the article page hovers at the bottom-right —
  snapshot after the article loads to find its ref ID.
