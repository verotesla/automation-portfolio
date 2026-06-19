# Setup Guide — Project 5: Typeform → Asana + Slack + Sheets

This guide walks through building a fan-out flow: a form that triggers three actions in parallel.

---

## Prerequisites

- [n8n.cloud](https://n8n.cloud) account
- [Typeform](https://typeform.com) account (free tier)
- Google account (Sheets)
- Slack workspace with app configured (from Project 2)
- [Asana](https://asana.com) account (free tier)
- Estimated time: 3–4 hours

---

## Part 1 — Typeform form

1. Create a new form: `Solicitud de Servicio`.
2. Add 4 questions:
   - ¿Cuál es tu nombre? (Short Text)
   - ¿Cuál es tu correo electrónico? (Email)
   - Tipo de servicio (Multiple Choice: Consultoría, Automatización, Soporte)
   - Descripción (Long Text)
3. Publish the form.

### Typeform token
1. Go to **Settings → Personal tokens** (admin.typeform.com).
2. **Generate a new token** → choose **Custom scopes** (least privilege).
3. Check: Accounts Read, Forms Read, Responses Read, Webhooks Read, Webhooks Write, Workspaces Read.
4. Generate and copy the token.

> 💡 Best practice: use "Custom scopes" instead of "All scopes". Only grant the permissions you need.

---

## Part 2 — Typeform Trigger in n8n

1. Create a workflow: `Typeform Multi-destino`.
2. **Add first step** → `Typeform` → **"On form submission"**.
3. **Credential:** paste the token.
4. **Form:** select the form.
5. Enable **Simplify Answers** and **Only Answers** (cleaner data).
6. **Test this trigger** → fill out and submit the form → confirm the data arrives.

> The Typeform Trigger creates the webhook automatically (that's why the webhook scopes are needed).

---

## Part 3 — Branch 1: Google Sheets

1. Create a sheet `Solicitudes de Servicio` with headers: Fecha, Nombre, Email, Servicio, Descripción.
2. From the **Typeform Trigger**, add **Google Sheets → Append Row**.
3. Select document and sheet.
4. Map:

| Column | Expression |
|--------|-----------|
| Fecha | `{{ $now.toFormat('dd/MM/yyyy HH:mm:ss') }}` |
| Nombre | `{{ $json["¿Cuál es tu nombre?"] }}` |
| Email | `{{ $json["¿Cuál es tu correo electrónico?"] }}` |
| Servicio | `{{ $json["Tipo de servicio"] }}` |
| Descripción | `{{ $json["Descripción"] }}` |

5. Execute step → verify a new row in the sheet.

---

## Part 4 — Branch 2: Slack

⚠️ The node must come from the **Typeform Trigger**, not from Google Sheets (it's a parallel branch).

1. From the trigger, add **Slack → Send a message**.
2. **Credential:** the Slack one (from Project 2).
3. **Send Message To:** Channel.
4. **Channel:** **"By ID"** mode → paste the Channel ID (e.g., `C0BABMRGAEA`).
5. **Message Text:**
```
🔔 Nueva solicitud de servicio

👤 Nombre: {{ $('Typeform Trigger').item.json["¿Cuál es tu nombre?"] }}
📧 Email: {{ $('Typeform Trigger').item.json["¿Cuál es tu correo electrónico?"] }}
🛠️ Servicio: {{ $('Typeform Trigger').item.json["Tipo de servicio"] }}
📝 Descripción: {{ $('Typeform Trigger').item.json["Descripción"] }}
```
6. Remove the signature: Options → disable "Include link to workflow".
7. Execute step → verify the message in the channel.

---

## Part 5 — Branch 3: Asana

1. In Asana, create a project: `Solicitudes de Servicio` (List view). Delete the sample tasks.
2. In n8n, from the **Typeform Trigger**, add **Asana → Create a Task**.
3. **Credential:** connect via **OAuth2** (one click to authorize).
4. **Workspace:** select the workspace.
5. **Name:**
```
Nueva solicitud: {{ $('Typeform Trigger').item.json["¿Cuál es tu nombre?"] }} - {{ $('Typeform Trigger').item.json["Tipo de servicio"] }}
```
6. **Notes** (in Additional Fields or the Notes field):
```
📧 Email: {{ $('Typeform Trigger').item.json["¿Cuál es tu correo electrónico?"] }}
📝 Descripción: {{ $('Typeform Trigger').item.json["Descripción"] }}
```
7. **Project Names or IDs:** select `Solicitudes de Servicio`.
8. Execute step → verify the task created in Asana.

---

## Part 6 — Final test and publish

1. **Publish/activate** the workflow.
2. Fill out and submit the form with new data.
3. Verify that a single submission triggers all 3 branches:
   - New row in Google Sheets
   - Message in Slack
   - New task in Asana

---

## Errors and fixes

| Error / Situation | Cause | Fix |
|-------------------|-------|-----|
| `channel_not_found` in Slack | Wrong Channel ID, or "From list" mode | Use "By ID" with the correct Channel ID |
| Slack message doesn't arrive | Bot was removed from the channel | Re-add the bot: `/invite @n8n-automation` |
| "joined #consultas" instead of the message | Bot joined the channel on execution | Run again; once joined, it sends the real message |
| Fields don't map | Names with accents/spaces | Use `$json["¿Cuál es tu nombre?"]` syntax |
| "Execute previous nodes to view input data" | Node has no trigger data | Run from the trigger or use "Execute workflow" |
| n8n signature appears | Attribution enabled | Disable "Include link to workflow" |

---

## Possible future improvements

- **Assign the Asana task** to an owner based on service type.
- **Confirmation email** to the client (fourth branch).
- **Filter/Switch** to route by service type (e.g., Support goes to a different channel).
- **Automatic due date** on the Asana task (e.g., +3 days).
