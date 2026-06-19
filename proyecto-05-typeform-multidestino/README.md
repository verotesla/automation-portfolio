# Project 5: Typeform → Asana + Slack + Google Sheets (Fan-out)

![Status](https://img.shields.io/badge/Status-Done-success)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-orange)
![Time](https://img.shields.io/badge/Time-4%20hours-blue)
![Tools](https://img.shields.io/badge/Tools-n8n%20%7C%20Typeform%20%7C%20Asana%20%7C%20Slack%20%7C%20Sheets-purple)

🌐 **English** | [Español](./README.es.md)

## Description

An automation that captures Typeform responses and, from a single submission, triggers **three actions in parallel**: it logs the request to Google Sheets, notifies the team on Slack, and creates a follow-up task in Asana.

This project introduces the **fan-out** pattern (one trigger → multiple simultaneous actions), fundamental in real-world automations where a single event must propagate to several systems at once.

## The fan-out pattern

Unlike a linear flow (one step after another), here the trigger branches into three independent paths that run at the same time:

```
                          ┌──────────────────────┐
                     ┌───▶│ Google Sheets        │  Log / backup
                     │    │ (Append Row)         │
┌─────────────────┐  │    └──────────────────────┘
│ Typeform Trigger│  │    ┌──────────────────────┐
│ (form submitted)│──┼───▶│ Slack (Send Message) │  Team notification
└─────────────────┘  │    └──────────────────────┘
                     │    ┌──────────────────────┐
                     └───▶│ Asana (Create Task)  │  Follow-up task
                          └──────────────────────┘
```

**Advantage:** the three actions are independent. If one fails, the other two still run. And all of them draw from the same source (the form), referenced with `$('Typeform Trigger')`.

## Use cases

- **Lead capture:** a web request generates a log, a sales-team notification, and a follow-up task, with no manual work.
- **Client onboarding:** an intake form triggers data archiving, team notification, and a setup task.
- **Ticket management:** a support request is logged, announced, and assigned as a task, all at once.
- **Event registration:** each signup is saved, announced, and generates a confirmation task.

## Tech stack

| Component | Tool | Role |
|-----------|------|------|
| Form | Typeform | Captures the request |
| Orchestration | n8n (Cloud) | Receives and distributes to 3 destinations |
| Trigger | Typeform Trigger | Fires on each form submission |
| Log | Google Sheets | Saves the request (queryable backup) |
| Notification | Slack | Notifies the team in a channel |
| Follow-up | Asana | Creates a task in a project |

## Form fields

| Question (Typeform) | Type |
|---------------------|------|
| ¿Cuál es tu nombre? | Short Text |
| ¿Cuál es tu correo electrónico? | Email |
| Tipo de servicio | Multiple Choice (Consultoría, Automatización, Soporte) |
| Descripción | Long Text |

## What each branch does

**Branch 1 — Google Sheets (Append Row):** saves a row with Date, Name, Email, Service, and Description. The date is generated with `$now` in a readable format.

**Branch 2 — Slack (Send Message):** posts a formatted message to the channel with the request data.

**Branch 3 — Asana (Create Task):** creates a task in the "Solicitudes de Servicio" project titled "Nueva solicitud: [Name] - [Service]" with email and description in the notes.

## Technical detail: trigger references

Since all three branches stem from the same trigger, they take their data directly from the trigger rather than the previous node:

```javascript
{{ $('Typeform Trigger').item.json["¿Cuál es tu nombre?"] }}
```

Field names use bracket-and-quote syntax `["..."]` because the Typeform questions contain spaces, accents, and question marks.

## Concepts learned

- **Fan-out pattern:** one trigger branched into multiple parallel actions.
- Connecting **Typeform** via Personal Access Token with custom scopes (least privilege).
- Referencing a specific node with `$('Node name').item.json[...]`.
- Handling field names with special characters (brackets and quotes).
- Integrating **Asana** (Create Task) via OAuth2, assigned to a project.
- Reusing credentials across portfolio projects (Slack and Google).
- Diagnosing Slack errors: `channel_not_found` (wrong Channel ID) and bot removed from channel.

## How to implement

See [`setup-guide.md`](./setup-guide.md) for the full step-by-step guide.

## Freelance value

A fan-out automation connecting a form with CRM/task management and notifications is worth an estimated **$400–700 USD** on freelance platforms. It's in high demand because it automates the full capture-and-assignment process, removing error-prone manual steps.

## Screenshots

See the [`/assets`](./assets) folder for screenshots of the workflow and the 3 branches working.

## Contact

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
