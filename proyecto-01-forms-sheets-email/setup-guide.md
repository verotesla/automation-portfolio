# Setup Guide — Project 01: Customer Inquiry Automation

This guide reproduces the workflow using a self-hosted n8n instance running in Docker.

> The public workflow export is sanitized. You must configure your own Google resources and credentials after import.

## 1. Prerequisites

- Docker Desktop
- Docker Compose
- A Google account
- Access to Google Forms, Google Sheets, and Gmail
- A Google Cloud project
- n8n self-hosted locally

The implementation used n8n `2.26.8`.

## 2. Start n8n with Docker

A safe example configuration is included in `deployment/`.

Copy:

```text
deployment/docker-compose.example.yml
deployment/.env.example
```

into your local n8n working directory.

Rename:

```text
.env.example → .env
```

Generate a strong encryption key and replace the placeholder in `.env`.

Then run:

```bash
docker compose up -d
```

Open:

```text
http://localhost:5678
```

See `deployment/README.md` for details.

## 3. Create the Google Form

Create a form named:

```text
Customer Inquiry
```

Add these required fields:

1. `Name` — Short answer
2. `Email` — Short answer
3. `Phone` — Short answer
4. `Message` — Paragraph

## 4. Link the Form to Google Sheets

In Google Forms:

1. Open **Responses**.
2. Click the Google Sheets icon.
3. Create a new spreadsheet named:

```text
Customer Inquiry - Responses
```

The response sheet should contain fields similar to:

```text
Timestamp | Name | Email | Phone | Message
```

Submit at least one test response.

## 5. Configure Google Cloud

Create or select a Google Cloud project.

Enable these APIs:

- Google Sheets API
- Google Drive API
- Gmail API

Google Forms API is not required for this architecture because Google Forms writes directly to Google Sheets.

## 6. Configure the OAuth Consent Screen

In **Google Auth Platform**:

1. Configure the app branding.
2. Set the audience to **External**.
3. Keep the app in **Testing** while developing.
4. Add your own Google account under **Test users**.

Suggested app name:

```text
n8n Automation Portfolio
```

## 7. Create OAuth Clients

Create a **Web application** OAuth client.

Use this redirect URI for local n8n:

```text
http://localhost:5678/rest/oauth2-credential/callback
```

You can use separate OAuth clients for Google Sheets and Gmail to keep integrations isolated.

Example names:

```text
n8n Google Sheets
n8n Gmail
```

Never commit the Client Secret to GitHub.

## 8. Import the Workflow

In n8n:

1. Create a workflow.
2. Use **Import from file**.
3. Select:

```text
workflows/project-01-customer-inquiry-automation.json
```

The public export is intentionally inactive and contains placeholder resource values.

## 9. Configure Google Sheets Trigger

Open the `Google Sheets Trigger` node.

Create/select your Google Sheets OAuth2 credential.

Configure:

```text
Poll Times: Every Minute
Document: Customer Inquiry - Responses
Sheet: Form Responses 1
Trigger On: Row Added
```

The sanitized workflow export does not contain your live spreadsheet ID, so reselect the document and sheet.

## 10. Configure Data Normalization

The `Normalize Customer Data` node maps:

```text
Name      → customer_name
Email     → customer_email
Phone     → customer_phone
Message   → customer_message
Timestamp → submitted_at
```

The expressions are already included in the workflow export.

## 11. Configure Gmail

Open both Gmail nodes and create/select your Gmail OAuth2 credential.

### Customer Confirmation

Recipient:

```javascript
{{ $json.customer_email }}
```

Subject:

```text
We received your inquiry, {{ $json.customer_name }}
```

The HTML message is already included in the workflow.

### Business Notification

Replace the placeholder recipient:

```text
business@example.com
```

with your internal notification email.

Subject:

```text
New customer inquiry - {{ $json.customer_name }}
```

## 12. Test the Workflow

Before publishing, test each node manually.

Confirm that:

- Google Sheets data is detected.
- The normalization node outputs all five internal fields.
- The customer email renders correctly.
- The business notification contains all expected values.

For safe testing, temporarily send both emails to an address you control.

## 13. Publish the Workflow

In n8n 2.x, publish the workflow so that the Google Sheets Trigger runs automatically.

After publishing:

1. Submit a **new** Google Forms response.
2. Do not click **Execute Workflow**.
3. Wait up to the polling interval.
4. Confirm both emails arrive automatically.
5. Check the **Executions** tab for the production execution.

## 14. Export Safely

When exporting a workflow for a public repository:

- Remove credential references
- Remove personal email addresses
- Replace live spreadsheet IDs with placeholders
- Remove instance-specific metadata
- Keep the public template inactive by default

The workflow in this repository has already been sanitized.

## Troubleshooting

### OAuth returns `access_denied`

Verify that:

- The app is in Testing mode.
- Your Google account is listed as a Test user.
- You are signing in with the same account.

If the browser is managed by an organization and OAuth behaves unexpectedly, retry from a personal browser profile or another browser not restricted by corporate policies.

### `invalid_client` / invalid client secret

Confirm that the Client ID and Client Secret belong to the same OAuth client.

If necessary, create a new OAuth client dedicated to the affected integration.

### Workflow only runs when clicking Execute

The workflow must be **published** for production triggers to run automatically.

### Docker container keeps restarting after adding `N8N_ENCRYPTION_KEY`

The encryption key in `.env` must match the key used by the existing n8n data volume. The safest approach is to define the key before the first production start and store it securely.

## Security Notes

Never commit:

```text
.env
Client Secret
OAuth tokens
API keys
Passwords
n8n credential exports
```

Use `.env.example` only for placeholder values.
