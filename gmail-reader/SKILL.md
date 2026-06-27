---
name: gmail-reader
description: "Lee el inbox de Gmail a pedido: lista, navega por número, lee contenido completo, busca. Preferencia del usuario: respuesta rápida tipo tabla numerada, navega con solo el número."
version: 2.1.0
author: makerjoe
platforms: [windows]
prerequisites:
  credential_files: [google_token.json, google_client_secret.json]
  commands: []
  environment_variables: []
---

# Gmail Reader

Lee el inbox de Gmail a pedido vía OAuth2 (google-workspace).

## Comandos básicos

```bash
GAPI="python /c/Users/makerjoe/AppData/Local/hermes/skills/productivity/google-workspace/scripts/google_api.py"

# Listar inbox (últimos 10)
$GAPI gmail search "in:inbox" --max 10

# Solo no leídos
$GAPI gmail search "in:inbox is:unread" --max 10

# Buscar
$GAPI gmail search "from:algo@dominio.com"
$GAPI gmail search "subject:factura"

# Leer email completo
$GAPI gmail get MESSAGE_ID
```

## Flujo de trabajo (preferencia del usuario)

1. Listar inbox con `--max 10` → mostrar como tabla numerada (N° | De | Asunto | Fecha)
2. Marcar no leídos con 🟡, relevantes con 🔵
3. Usuario responde con solo el número → leer ese email con `gmail get`
4. Resumir el contenido: qué es, quién lo manda, si requiere acción

## Manejo de enlaces a artículos en emails

Cuando un email contiene enlaces a artículos (Medium, blogs, etc.) y el usuario pide leerlos:

1. **Medium** → desbloquear con el skill `freedium-unlock`:
   - Cargar: `skill_view("freedium-unlock")` y seguir sus pasos
   - Si freedium falla: buscar cross-post en dev.to con r.jina.ai
   - Si todo falla: ofrecer abrir en Chrome del usuario via `cmd //c start`
2. **Dev.to / sitios sin Cloudflare** → r.jina.ai funciona bien:
   ```bash
   curl -sL "https://r.jina.ai/URL" -H "User-Agent: Mozilla/5.0"
   ```
3. Si nada funciona, informar al usuario y ofrecer abrir el link en su Chrome

## Output esperado

- `gmail search` → `[{id, threadId, from, to, subject, date, snippet, labels}]`
- `gmail get` → `{id, threadId, from, to, subject, date, labels, body}`

## Troubleshooting

| Problema | Solución |
|----------|----------|
| `NOT_AUTHENTICATED` | Re-ejecutar OAuth: `setup.py --auth-url` |
| `HttpError 403` | API no habilitada o falta scope |
| Token expired | Auto-refresh, si falla: `setup.py --auth-code <code>` |
