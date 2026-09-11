# Case Study — Customer Inquiry Automation

## Context

Small teams often receive customer inquiries through online forms and then manually perform the same follow-up steps:

- Review each new response
- Send a confirmation to the customer
- Notify the responsible person internally
- Copy data between systems

This creates repetitive administrative work and increases the chance that a request is overlooked.

## Objective

Build a simple, reusable automation that:

1. Captures inquiries through Google Forms.
2. Stores responses in Google Sheets.
3. Detects new responses automatically.
4. Normalizes customer data.
5. Sends a confirmation to the customer.
6. Sends an internal business notification.
7. Runs from a self-hosted n8n environment.

## Solution

The implementation uses Google Forms as the input layer and Google Sheets as the response store.

n8n polls the response sheet every minute. When a new row is detected, the workflow maps the external field names to a stable internal schema and fans out into two Gmail actions.

```text
Google Form
    ↓
Google Sheets
    ↓
Google Sheets Trigger
    ↓
Normalize Customer Data
    ├──→ Customer Confirmation
    └──→ Business Notification
```

## Technical Design

### Trigger

`Google Sheets Trigger` monitors the linked response sheet for `Row Added`.

### Normalization Layer

The workflow maps:

```text
Name      → customer_name
Email     → customer_email
Phone     → customer_phone
Message   → customer_message
Timestamp → submitted_at
```

This makes downstream nodes independent of the original Google Forms field names.

### Fan-out

One normalized item is passed to two independent branches:

- Customer-facing confirmation
- Internal business notification

This demonstrates a simple fan-out pattern in n8n.

### Authentication

Google Sheets and Gmail use OAuth2 credentials created in Google Cloud.

The n8n instance is self-hosted in Docker with persistent storage.

## Validation

The workflow was validated with an end-to-end production test.

A new Google Forms response was submitted after the workflow was published.

The test confirmed:

- The response reached Google Sheets.
- The Google Sheets Trigger detected the new row automatically.
- The normalization node produced all expected fields.
- The customer confirmation email arrived successfully.
- The internal notification email arrived successfully.
- The automation completed without manually clicking Execute Workflow.

## Outcome

The project demonstrates a complete automated intake flow that connects a form, spreadsheet, workflow engine, and email service.

The final implementation is more robust than a simple one-email demo because it includes:

- A normalization layer
- Two independent notification branches
- OAuth2 integrations
- Self-hosted n8n
- Docker-based persistence
- A sanitized public workflow export

## Limitations

This project intentionally remains a foundational automation.

Current limitations include:

- Polling interval of up to one minute
- Local n8n availability depends on the host computer being running
- No retry/error workflow
- No duplicate detection
- No CRM integration
- No centralized logging
- No email verification
- Generic email templates

These limitations provide clear opportunities for later portfolio projects.

## Security Considerations

The public repository does not include:

- Client secrets
- OAuth tokens
- Personal notification addresses
- Live Google Sheet IDs
- Private n8n credential data

A sanitized workflow template is provided instead.

## Future Improvements

Possible extensions include:

1. Add Salesforce or HubSpot
2. Add AI-based inquiry categorization
3. Score and route leads automatically
4. Add duplicate detection
5. Add error-handling and retry workflows
6. Add Slack, SMS, or WhatsApp notifications
7. Move n8n to a cloud-hosted Docker environment
8. Add monitoring and centralized logs

## Key Takeaway

The main value of this project is not the individual tools. It is the end-to-end integration pattern:

**capture → store → detect → normalize → branch → notify**

That same pattern can be reused in many real business processes.
