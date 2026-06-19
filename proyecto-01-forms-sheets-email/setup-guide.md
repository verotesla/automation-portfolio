# Setup Guide — Project 1: Google Forms → Sheets + Email

This guide walks through building a workflow that captures form responses, logs them to Google Sheets, and sends an automatic confirmation email.

---

## Prerequisites

- Google account (Gmail)
- [n8n.cloud](https://n8n.cloud) account
- Estimated time: 45 minutes

---

## Part 1 — Google Cloud setup

### Create the project
1. Go to [console.cloud.google.com](https://console.cloud.google.com).
2. Project selector (top left) → **New Project**.
3. Name: `n8n-automations` → Create.

### Enable the APIs
Search for and enable these three APIs:
- Google Forms API
- Google Sheets API
- Gmail API

### Create a Service Account
1. Menu → **Credentials**.
2. **Create Credentials** → **Service Account**.
3. Name: `n8n-sa` → Create and Continue.
4. Role: **Editor** → Continue → Done.

### Download the JSON key
1. Service Accounts → `n8n-sa@...`
2. **Keys** tab → **Add Key** → **Create new key** → **JSON**.
3. Download and store it securely.

---

## Part 2 — Google Form

1. Go to [forms.google.com](https://forms.google.com) and create a form: `Customer Inquiry`.
2. Add four fields:
   - Name — Short answer (required)
   - Email — Short answer + Email validation (required)
   - Phone — Short answer (required)
   - Message — Paragraph (required)
3. Publish the form and copy the link.
4. In the **Responses** tab, click the Google Sheets icon to auto-create the linked sheet.

---

## Part 3 — Configure n8n

### Create the workflow
1. n8n.cloud → **New Workflow**.

### Add the Google Sheets Trigger
1. **+** → search "Google Sheets" → **Google Sheets Trigger**.
2. Create new credentials → Service Account JSON → paste the downloaded JSON contents.
3. Document: the form responses sheet.
4. Sheet: "Form Responses 1".
5. Trigger On: **Row Added** → click **Fetch Test Event**.

### Add Gmail Send
1. **+** → search "Gmail" → **Send Email**.
2. Create new credentials → Google OAuth2 → authorize Gmail.

### Configure the email
- **To:** select the form's "email" field.
- **Subject:** `Confirmation of your inquiry`
- **Message:**
  ```
  Hello,

  Thank you for contacting us.
  We've received your inquiry and will respond soon.

  Best regards,
  The team
  ```

### Publish
1. Click **Publish** (top right).
2. Version Name: `v1.0 - Initial setup` → Save.

---

## Part 4 — Test

1. Open the Google Form and fill it out.
2. Verify:
   - Data appears in Google Sheets ✅
   - Confirmation email received ✅

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| "Unable to sign" | Use an incognito window or set up the JSON manually |
| Email doesn't arrive | Check Spam; free tier has a 100/day limit |
| Data not in Sheets | Check permissions; share the Sheet with the service account email |
| Workflow doesn't run | Make sure it's Published (button should be green) |

---

## Useful links

- [n8n Docs](https://docs.n8n.io)
- [Google Forms API](https://developers.google.com/forms)
- [Gmail API](https://developers.google.com/gmail/api)
