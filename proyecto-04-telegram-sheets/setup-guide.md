# Setup Guide — Project 4: Telegram Bot → Sheets + Replies

This guide documents the full process of building a Telegram bot that logs messages to Google Sheets and replies based on keywords.

---

## Prerequisites

- [n8n.cloud](https://n8n.cloud) account
- Telegram account
- Google account (for Sheets)
- Estimated time: 2–3 hours

---

## Part 1 — Create the Telegram bot

1. In Telegram, search for **BotFather** (official bot with blue checkmark).
2. Start with `/start`, then send `/newbot`.
3. Set:
   - **Name** (free, e.g., "Vero Automation Bot").
   - **Username** (must end in `bot`, e.g., `vero_automation_bot`).
4. BotFather returns a **token** (e.g., `7123456789:AAH...`).
5. Copy and store the token securely.

> ⚠️ Never push the token to GitHub or share it publicly.

---

## Part 2 — Create the workflow and Telegram Trigger

1. In n8n, create a workflow: `Telegram to Sheets`.
2. **Add first step** → `Telegram` → Triggers section → **"On message"**.
3. **Credential:** create new → paste the BotFather token → save.

> 💡 Telegram's connection is simple: just the token, no OAuth or pop-ups.

### Test the trigger
1. **Execute step** on the trigger (it starts listening).
2. In Telegram, find the bot, press **Start**, and send it a message.
3. The message should appear captured in n8n.

> ⚠️ A bot can't receive messages until the user starts the conversation with **Start**.

---

## Part 3 — Create the Google Sheet

1. Create a sheet: `Telegram Mensajes`.
2. In row 1, create the headers (columns):

| A | B | C | D |
|---|---|---|---|
| Fecha | Usuario | Mensaje | ChatID |

---

## Part 4 — Google Sheets node (save)

1. Add a **Google Sheets → Append Row** node.
2. **Credential:** the Google account (reusable from Project 1).
3. **Document:** select `Telegram Mensajes`.
4. **Sheet:** the tab (Sheet1).
5. **Map the fields:**

| Column | Expression |
|--------|-----------|
| Fecha | `{{ DateTime.fromSeconds($json.message.date).toFormat('dd/MM/yyyy HH:mm:ss') }}` |
| Usuario | `{{ $json.message.from.first_name }}` |
| Mensaje | `{{ $json.message.text }}` |
| ChatID | `{{ $json.message.chat.id }}` |

> The Date expression converts Telegram's Unix timestamp to a readable format using Luxon (`DateTime`).

---

## Part 5 — Switch node (reply classifier)

1. Add a **Switch** after Google Sheets.
2. **Mode:** Rules.
3. Create 5 Routing Rules. In each: operator **Boolean → is true**, value field in **Expression** mode.

> ⚠️ **Common mistake:** forgetting to change the operator to **Boolean / is true**. Left on another operator, the rule won't evaluate correctly and the message falls through the wrong route or the fallback.

> ⚠️ **Important:** since the Switch receives data **after** Google Sheets, the expressions evaluate the `Mensaje` field (the column name), NOT `message.text`.

**Rule 1 — Greeting:**
```javascript
{{ ["hola","buenas","hi","hello"].some(p => ($json.Mensaje || "").toLowerCase().includes(p)) }}
```

**Rule 2 — Price:**
```javascript
{{ ["precio","costo","cuánto","cuanto","cotización","cotizacion"].some(p => ($json.Mensaje || "").toLowerCase().includes(p)) }}
```

**Rule 3 — Hours:**
```javascript
{{ ["horario","abierto","atención","atencion","hora"].some(p => ($json.Mensaje || "").toLowerCase().includes(p)) }}
```

**Rule 4 — Help:**
```javascript
{{ ["ayuda","soporte","problema","help"].some(p => ($json.Mensaje || "").toLowerCase().includes(p)) }}
```

**Rule 5 — Location:**
```javascript
{{ ["ubicación","ubicacion","dónde","donde","dirección","direccion"].some(p => ($json.Mensaje || "").toLowerCase().includes(p)) }}
```

4. **Options → Add option → Fallback Output:** enable it (creates an extra output for non-matching messages).

> Note: do NOT enable "Send data to all matching outputs" here, because we want ONE reply per message.

---

## Part 6 — Reply nodes (Telegram Send Message)

From **each Switch output** (including the Fallback), add a **Telegram → Send Message** node:

1. **Credential:** the bot.
2. **Chat ID:** `{{ $('Telegram Trigger').item.json.message.chat.id }}`
3. **Text:** the message for that category.
4. Disable the signature: **Add Field → Append n8n Attribution → OFF**.

| Output | Category | Suggested text |
|--------|----------|----------------|
| 0 | Greeting | ¡Hola! 👋 Gracias por escribir. ¿En qué puedo ayudarte? |
| 1 | Price | 💰 Con gusto te comparto información de precios. Un asesor te contactará pronto. |
| 2 | Hours | 🕐 Nuestro horario es de Lunes a Viernes, 9:00 a 18:00. |
| 3 | Help | 🛠️ Tu mensaje fue registrado y te atenderemos pronto. |
| 4 | Location | 📍 Estamos ubicados en [dirección]. ¡Te esperamos! |
| Fallback | Default | ✅ ¡Mensaje recibido y guardado! Gracias por escribir. |

> 💡 Tip: copy (Ctrl+C / Ctrl+V) an already-configured Telegram node and just change the text, to avoid reconfiguring Chat ID and signature each time.

---

## Part 7 — Publish and test

1. **Publish/activate** the workflow.
2. Send different messages to the bot from Telegram and verify:
   - Each message is saved to Sheets (with readable date).
   - The bot replies according to the keyword.
   - A message with no keyword gets the default reply.

> ⚠️ **Telegram limitation:** you can't listen for test ("Execute") and production (published) messages at the same time. If that notice appears, unpublish to test manually, or test directly in production by messaging the bot.

---

## Errors and fixes

| Error / Situation | Cause | Fix |
|-------------------|-------|-----|
| Bot doesn't receive messages in test | Conversation not started | Press **Start** on the bot first |
| "can't listen for test executions..." notice | Telegram doesn't allow test + production at once | Unpublish to test, or test in production |
| Bot always replies the same | Rules searched `message.text` but after Sheets the field is `Mensaje` | Use `$json.Mensaje` in the expressions |
| A rule doesn't classify | Operator wasn't Boolean / is true | Change the operator to **Boolean → is true** |
| "This message was sent with n8n" appears | Auto-signature enabled | Disable **Append n8n Attribution** |
| Unreadable date (long number) | Telegram sends a Unix timestamp | Convert with `DateTime.fromSeconds(...).toFormat(...)` |

---

## Possible future improvements

- **AI replies** (projects 13/16): understand natural language instead of keywords.
- **Telegram inline keyboard** (button menu) for quick options.
- **Notify a second channel** (e.g., alert an admin when a certain message type arrives).
