# Setup Guide — Project 3: Email → Categorization + Gmail Labels

This guide documents the full process of building the email auto-classification workflow, including common errors and how to resolve them.

---

## Prerequisites

- [n8n.cloud](https://n8n.cloud) account
- A Gmail account
- Estimated time: 2–3 hours

---

## Part 1 — Create the Gmail labels

Labels must exist before automating. In Gmail (web):

1. Left sidebar → scroll to **"More"** → **"Create new label"**.
2. Create these 5 labels:
   - `🔴 Urgente`
   - `💼 Clientes`
   - `🧾 Facturación`
   - `🤝 Soporte`
   - `📰 Newsletters`

> 💡 Note exactly how you wrote them (emoji included), because you'll reference them the same way in n8n.

---

## Part 2 — Create the workflow in n8n

1. Create a new workflow named `Email Categorization`.

---

## Part 3 — Node 1: Gmail Trigger

1. **Add first step** → search `Gmail` → **Triggers** section → **"On message received"**.
2. **Credential:** create a new one (Gmail OAuth2 API) → **"Connect my account"** / "Sign in with Google".
3. Authorize with the Gmail account and accept the permissions.
4. **Poll Times:** leave the default (every minute).

> ⚠️ **Common OAuth error (`access_denied`):** if the window closes during authorization and shows "Insufficient parameters for OAuth2 callback" or `access_denied`, it's usually due to multiple active Google accounts in the browser. **Fix:** use an **incognito window**, sign in to n8n only, and authorize with a single account. Also check that pop-ups are allowed.

---

## Part 4 — Node 2: Switch (classifier)

1. From the Gmail Trigger, add a **Switch** node.
2. **Mode:** Rules.
3. Create **5 Routing Rules**, one per category.

### Configuring each rule

For each rule:
1. **Operator:** change from "contains" to **Boolean → is true**.
2. **Value field:** set to **Expression** mode and write the category's expression.

**Rule 1 — Urgent:**
```javascript
{{ ["urgente","urgent","asap","error","no funciona"].some(p => ($json.Subject || "").toLowerCase().includes(p)) }}
```

**Rule 2 — Clients:**
```javascript
{{ ["propuesta","cotización","cotizacion","presupuesto","proposal","quote","contratar"].some(p => ($json.Subject || "").toLowerCase().includes(p)) }}
```

**Rule 3 — Billing:**
```javascript
{{ ["factura","invoice","pago","payment","recibo","transferencia","cobro"].some(p => ($json.Subject || "").toLowerCase().includes(p)) }}
```

**Rule 4 — Support:**
```javascript
{{ ["ayuda","help","soporte","support","duda","consulta","how to"].some(p => ($json.Subject || "").toLowerCase().includes(p)) }}
```

**Rule 5 — Newsletters:**
```javascript
{{ ["newsletter","boletín","boletin","promoción","promocion","unsubscribe","oferta","descuento"].some(p => ($json.Subject || "").toLowerCase().includes(p)) }}
```

### Switch options

Go to **Options → Add option** and enable:
- **"Send data to all matching outputs":** ON (lets an email receive multiple labels).
- *(Optional)* **"Fallback Output":** to route emails that match no category.

> Note: with expressions using `.toLowerCase()`, the "Ignore Case" option isn't strictly necessary, since the lowercase conversion happens inside the expression itself.

---

## Part 5 — Nodes 3 to 7: Apply labels (Gmail)

From **each Switch output**, add a Gmail node:

1. From the matching output, drag → search `Gmail` → **"Add Label to Message"** action.
2. **Resource:** Message.
3. **Operation:** Add Label.
4. **Message ID:** select the message `id` coming from the trigger → `{{ $json.id }}`.
   - ⚠️ Use the message `id`, NOT `historyId`, NOT `threadId`, and NOT n8n's internal IDs (`workflow.id`, `$execution.id`).
5. **Label:** select the matching label from the list (🔴 Urgente, 💼 Clientes, etc.).

Repeat for all 5 categories.

---

## Part 6 — Publish and test

1. **Publish/activate** the workflow. **This is essential:** a workflow only reacts to new emails when active. If only run manually, it processes old loaded data, not emails that arrive afterward.
2. Send yourself test emails with different keywords in the subject ("urgente", "invoice", "ayuda", "oferta"...).
3. Verify in Gmail that each email receives its matching label.

---

## Errors and fixes

| Error / Situation | Cause | Fix |
|-------------------|-------|-----|
| `access_denied` connecting Gmail | Multiple active Google accounts | Use an incognito window with a single account |
| Email arrives but isn't labeled | Workflow wasn't published/active | Publish the workflow; only then does it listen for new emails |
| Detects "urgente" but not "URGENTE" | Case-sensitive comparison | Use `.toLowerCase()` in the expression |
| Can't add a second condition (OR) in a rule | This Switch version allows one condition per rule | Use a `.some()` expression to evaluate several words |
| Confusion with the Message ID field | Several "id" values exist in the data | Use the message `id` (`{{ $json.id }}`), not historyId or threadId |

---

## Possible future improvements

- **AI classification** (Project 10): instead of keywords, use a model that understands the email's content and intent, catching cases rules miss.
- **Search the email body too**, not just the subject.
- **"Unclassified" category** via Fallback Output, to review emails no rule caught.
