# Project 2: Slack → Airtable + Automatic Reaction

![Status](https://img.shields.io/badge/Status-Functional%20(documented%20limitation)-yellow)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-orange)
![Time](https://img.shields.io/badge/Time-4%20hours-blue)
![Tools](https://img.shields.io/badge/Tools-n8n%20%7C%20Slack%20%7C%20Airtable-purple)

🌐 **English** | [Español](./README.es.md)

## Description

An automation that captures messages posted in a Slack channel and automatically stores them in an Airtable database, with an automatic confirmation reaction on the original message.

This project demonstrates integration between a communication tool (Slack), a workflow orchestration tool (n8n), and a collaborative database (Airtable), connected via webhooks and OAuth authentication.

## Use cases

- **Customer support:** capture inquiries from a Slack channel into a database for follow-up.
- **Lead management:** log requests from a sales channel into a lightweight CRM.
- **Internal tickets:** centralize team requests in a queryable table.
- **Incident logging:** document reports arriving via Slack in a structured repository.

## Tech stack

| Component | Tool | Role |
|-----------|------|------|
| Data source | Slack | Channel where messages are posted |
| Orchestration | n8n (Cloud) | Receives, processes, and routes the data |
| Reception | Webhook | Entry point for Slack events |
| Storage | Airtable | Database where records are saved |
| Confirmation | Slack Reaction | Automatic reaction on the original message |

## Workflow architecture

```
┌──────────┐   ┌─────────────────────┐   ┌─────────────────┐   ┌──────────────┐
│ Webhook  │──▶│ Respond to Webhook  │──▶│ Create a record │──▶│ Add reaction │
│ (Slack)  │   │   (verification)    │   │   (Airtable)    │   │   (Slack)    │
└──────────┘   └─────────────────────┘   └─────────────────┘   └──────────────┘
   Receives        Confirms to Slack         Saves data          Reacts ✅
```

## Component status

| Component | Status | Notes |
|-----------|--------|-------|
| Webhook (reception) | ✅ Functional | Receives and processes data correctly |
| Respond to Webhook | ✅ Functional | Responds to Slack with the first item received |
| Create a record (Airtable) | ✅ Functional and tested | Saves Author, Message, Timestamp, and Channel |
| Add reaction (Slack) | ⏳ Pending live data | Requires a real message to react to (see Limitations) |

## Concepts learned

- Configuring **webhooks** as a workflow entry point.
- Handling **OAuth2** and **scopes** (permissions) in a Slack app.
- Using the **Respond to Webhook** node for endpoint verification.
- **Field mapping** between the source payload and destination columns.
- Creating and configuring **personal access tokens** in Airtable.
- The difference between **Test URL** and **Production URL** in n8n.
- The structure of Slack event payloads (`text`, `user`, `ts`, `channel`).

## Known limitations

The automatic Slack reaction (final node) requires a **real message** posted in the channel to work. During development, mock data was used to validate the flow, which confirms the Webhook → Airtable logic works 100%, but prevents the reaction from running because the test timestamp doesn't correspond to any existing Slack message.

To enable the reaction in production:
1. **Publish the workflow** in n8n (active state) so the Production URL responds.
2. **Verify the Slack Event Subscription** against that active URL.
3. Send a real message to the channel, which generates a valid timestamp for the reaction.

This limitation is specific to the development environment and is not a flaw in the workflow design.

## How to implement

See [`setup-guide.md`](./setup-guide.md) for the full step-by-step guide.

## Freelance value

Projects of this type (Slack + database integration) are worth an estimated **$250–500 USD** on freelance platforms, depending on the number of fields, data transformations, and business rules required.

## Screenshots

See the [`/assets`](./assets) folder for screenshots of the working workflow.

## Contact

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
