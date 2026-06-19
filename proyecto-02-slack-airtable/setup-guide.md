# Setup Guide — Project 2: Slack → Airtable + Reaction

This guide documents the full process of building the workflow, including common errors and how to resolve them.

---

## Prerequisites

- [n8n.cloud](https://n8n.cloud) account
- [Airtable](https://airtable.com) account
- [Slack](https://slack.com) workspace (free)
- Estimated time: 3–4 hours

---

## Part 1 — Create the Slack workspace and channel

1. Create a Slack workspace (e.g., `automation-test`).
2. Create a channel: `#consultas`.

---

## Part 2 — Create the Slack app

1. Go to [api.slack.com/apps](https://api.slack.com/apps).
2. **Create New App** → **From scratch**.
3. Name: `n8n-automation`. Select the workspace.

### Configure the scopes (permissions)

Under **OAuth & Permissions** → **Bot Token Scopes**, add:

```
app_mentions:read
channels:read
chat:write
incoming-webhook
reactions:write
users:read
```

### Get the credentials

- **Bot User OAuth Token** (starts with `xoxb-`): in *OAuth & Permissions*.
- **Signing Secret**: in *Basic Information* → *App Credentials*.

> ⚠️ Store both securely. Never push them to GitHub.

---

## Part 3 — Create the Airtable base

1. Create a base called `Slack Messages`.
2. Create these **fields (columns)**:

| Field | Type |
|-------|------|
| Author | Single line text |
| Message | Long text |
| Timestamp | Date |
| Channel | Single line text |

> 💡 **Common mistake:** creating the names as *rows* instead of *columns*. They must be fields (columns), not records (rows).

### Get the Airtable token

1. Avatar → **Account** → **API** section → **Go to developer hub**.
2. **Create new token** (Personal Access Token).
3. Scopes: `data.records:read`, `data.records:write`, `schema.bases:read`.
4. Associate the `Slack Messages` base.
5. Copy and store the token.

> Note: Airtable's classic API keys were deprecated in 2024. Personal Access Tokens are used instead.

---

## Part 4 — Build the workflow in n8n

The final workflow has **4 nodes** in this order:

```
Webhook → Respond to Webhook → Create a record (Airtable) → Add a reaction (Slack)
```

### Node 1 — Webhook
1. Add a **Webhook** node.
2. **HTTP Method:** POST.
3. **Respond:** change from "Immediately" to **"Using 'Respond to Webhook' Node"**.

> This is key: Slack sends a `challenge` parameter when verifying the URL, and needs a controlled response.

### Node 2 — Respond to Webhook
1. Add a **Respond to Webhook** node.
2. **Respond With:** "First Incoming Item".

### Node 3 — Create a record (Airtable)
1. Add an **Airtable → Create a record** node.
2. **Credential:** connect to Airtable (authorize via OAuth and select the base).
3. **Base:** `Slack Messages`. **Table:** `Table 1`.
4. **Mapping Column Mode:** Map Each Column Manually.
5. Map the fields to the Slack payload:

| Airtable field | Slack data |
|----------------|------------|
| Author | `user_name` (or `user`) |
| Message | `text` |
| Timestamp | `ts` |
| Channel | `channel` |

> 💡 **Common mistake:** if fields don't appear, use the "go to data" arrow to force a refresh, or re-select Base and Table.

### Node 4 — Add a reaction (Slack)
1. Add a **Slack → Add a reaction** node.
2. **Credential:** Slack account.
3. **Channel:** **"By ID"** mode → enter the Channel ID (e.g., `C0BABMRGAEA`).
4. **Message Timestamp:** the message `ts`.
5. **Emoji Code:** `thumbsup` (not `+1`; it must be the emoji name).

> To get the Channel ID: click the channel name in Slack → "About" → the ID starts with `C`.

---

## Part 5 — Connect Slack to n8n (Event Subscriptions)

1. **Publish/activate** the workflow in n8n (so the Production URL responds).
2. In n8n, copy the Webhook node's **Production URL**.
3. In Slack → **Event Subscriptions** → enable → paste the URL into **Request URL**.
4. Slack verifies the URL with a *challenge*.
5. Under **Subscribe to bot events**, add: `message.channels`.
6. Save and **reinstall the app** in the workspace.

---

## Errors and fixes

| Error | Cause | Fix |
|-------|-------|-----|
| "access_denied" on OAuth | Session/permissions | Use an incognito window; sign in again |
| "Could not load list" (channels) | Missing scopes | Add scopes and reinstall the app |
| "Your URL didn't respond with challenge" | Webhook in "Immediately" mode | Switch to "Respond to Webhook Node" |
| Airtable fields don't appear | Sync issue | Force refresh / re-select Base and Table |
| "Emoji code is required" | Invalid emoji code | Use `thumbsup`, not `+1` |
| Reaction fails with mock data | Fake timestamp | Requires a real message (see Limitations in README) |

---

## Environment limitation (important)

With mock data, the Webhook → Airtable nodes work perfectly, but the Slack reaction can't run because the test timestamp doesn't correspond to a real message. In production, with the workflow published and a real message in the channel, the reaction works with no further changes.
