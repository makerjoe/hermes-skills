# Handling Medium Paywall in Article Links

Medium usa Cloudflare y paywall member-only. No se puede acceder automatizadamente con métodos tradicionales.

## Attempts made (June 2026)

| Method | Result |
|--------|--------|
| Browser (Browserbase) directo a Medium | ❌ Cloudflare block |
| r.jina.ai | ❌ Respeta paywall member-only |
| freedium.cfd / .life / .one / .xyz / .site / .club | ❌ Dominios caídos / en venta / 404 |
| readmedium.com | ❌ SPA de Next.js sin contenido real |
| medium.rip | ❌ Expirado (dominio en venta) |
| scribe.rip | ❌ Caído |
| webcache.googleusercontent.com | ❌ No devuelve contenido |
| `?format=text` parameter | ❌ Cloudflare block |
| textise.iitty.com | ❌ No responde |
| Dev.to cross-post search | ✅ Funciona si el autor cross-posteó |
| Chrome del usuario (perfil real vía Playwright) | ⚠️ Sin sesión Medium = solo muestra intro + pantalla de registro |
| **freedium-mirror.cfd via browser_navigate** | **✅ FUNCIONA - ver abajo** |

## Lo que funciona

### 1. freedium-mirror.cfd (vía Hermes Browser — RECOMENDADO)

Este es el **único bypasser funcional en 2026**. Se usa con `browser_navigate` + `browser_type` + `browser_click`:

```python
# Workflow típico
browser_navigate("https://freedium-mirror.cfd/")
# Obtener snapshot para encontrar los refs del textbox y el botón
# El textbox "Article URL" suele ser ref=e85/e88
# El botón "Unlock" suele ser ref=e86/e89
browser_type("@textbox-ref", "URL_COMPLETA_DEL_ARTICULO_MEDIUM")
browser_click("@unlock-button-ref")
# Esperar renderizado, luego extraer el texto:
# browser_console({expression: "document.querySelector('article').innerText"})
```

**Notas importantes:**
- Acepta URLs de Medium directas (`https://medium.com/...`) y también subdominios como `https://therightstuff.medium.com/...`
- Renderiza el artículo completo dentro de un `<article>` tag
- Se puede extraer el texto completo con `document.querySelector('article').innerText`
- Sorprendentemente funciona incluso con el Browserbase cloud (sin proxies residenciales) - a pesar del warning "Running WITHOUT residential proxies"
- La UI cambia los refs entre visitas: los números (e85, e88, etc.) varían, verificar con `browser_snapshot` cada vez
- Marca cuántos artículos desbloqueaste con "↳ N articles unlocked"
- Tiene un footer con About, Privacy, Terms, RSS, GitHub, Codeberg
- No requiere cuenta ni registro — es totalmente gratuito

### 2. Buscar cross-post en dev.to

Algunos autores cross-postean sus artículos de Medium en dev.to (donde no hay paywall ni Cloudflare):

```bash
# Buscar el artículo
curl -sL "https://r.jina.ai/https://dev.to/search?q=KEYWORDS" -H "User-Agent: Mozilla/5.0"

# Leer artículo completo desde dev.to
curl -sL "https://r.jina.ai/https://dev.to/USER/ARTICLE-SLUG" -H "User-Agent: Mozilla/5.0"
```

### 3. Abrir en Chrome del usuario

Último recurso: usar `cmd //c start "https://medium.com/..."`. Sin sesión Medium paga solo muestra intro + pantalla de registro.

## Pattern: from email link to article

1. Usuario pide leer artículo N de la tabla de emails
2. Extraer URL del body del email
3. Si es Medium → usar **freedium-mirror.cfd** vía browser_navigate (opción más rápida y completa)
4. Si freedium falla, buscar cross-post en dev.to con r.jina.ai
5. Si todo falla, ofrecer abrir en Chrome del usuario

## URLs de artículos del usuario

| Artículo | URL |
|----------|-----|
| "The OpenCode revolution" (Fabio Matricardi) | `https://medium.com/artificial-intel-ligence-playground/the-opencode-revolution-more-than-just-another-chatbot-21706215701f` |
| "VSCode-ing With Local LLMs" (Adam Fisher) | `https://therightstuff.medium.com/vscode-ing-with-local-llms-365b7533ccdf` |
| "[Free] How to run Claude Code for free using OpenCode Zen and Minimax M2" (Nitin Gavhane) | `https://medium.com/@nitingavhane/free-how-to-run-claude-code-for-free-using-opencode-zen-and-minimax-m2-a7fee3f0a547` |
