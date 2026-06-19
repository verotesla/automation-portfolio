# Project 1: Google Forms → Google Sheets + Automated Email

![Status](https://img.shields.io/badge/Status-Done-success)
![Difficulty](https://img.shields.io/badge/Difficulty-Basic-green)
![Time](https://img.shields.io/badge/Time-2%20hours-blue)
![Tools](https://img.shields.io/badge/Tools-n8n%20%7C%20Google%20Forms%20%7C%20Sheets%20%7C%20Gmail-purple)

🌐 **English** | [Español](./README.es.md)

## Description

An automation that captures Google Forms responses, saves them to Google Sheets, and sends an automatic confirmation email to the customer, all in under a minute and with no manual work.

This is the foundational automation for thousands of small businesses that need to capture inquiries, organize data, and confirm receipt automatically.

## Use cases

- **Consulting:** capture inquiries from potential clients.
- **Services:** take booking/appointment requests.
- **E-commerce:** process product questions.
- **Education:** register interested students.
- **Professional services:** lawyers, accountants, therapists.

## Tech stack

| Tool | Role | Cost |
|------|------|------|
| n8n | Workflow orchestration | Free trial / paid tier |
| Google Forms | Data capture | Free |
| Google Sheets | Database | Free |
| Gmail | Email sending | Free |

## Workflow architecture

```
┌─────────────┐   ┌────────────────────────┐   ┌─────────────────┐
│ Google Form │──▶│ Google Sheets Trigger  │──▶│ Gmail Send       │
│ (submission)│   │ (n8n detects new row)  │   │ (confirmation)   │
└─────────────┘   └────────────────────────┘   └─────────────────┘
```

## Metrics

| Metric | Value |
|--------|-------|
| Response time | < 30 seconds |
| Automation rate | 100% |
| Availability | 24/7 |
| Cost | $0 (free tier) |

## Concepts learned

- Triggers and webhooks.
- OAuth2 authentication and Service Accounts.
- API integration (Google Forms, Sheets, Gmail).
- Data mapping between nodes.
- Dynamic variables in workflows.

## Lessons learned

**What worked:** the Google Sheets Trigger is very stable, Gmail delivery is reliable, and simplicity makes the workflow easy to maintain.

**What was tricky:** the Google Cloud setup has many steps, and OAuth2 authorization occasionally fails on the first try.

**Future improvements:** real-time email validation, automatic inquiry categorization, CRM integration (Salesforce, HubSpot), and an optional confirmation SMS.

## How to implement

See [`setup-guide.md`](./setup-guide.md) for the full step-by-step guide.

## Freelance value

This is one of the most in-demand projects on freelance platforms, with a typical rate of **$200–400 USD**. Small and medium businesses need it to capture and organize inquiries.

## Screenshots

See the [`/assets`](./assets) folder for screenshots of the form, sheet, email, and workflow.

## Contact

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
