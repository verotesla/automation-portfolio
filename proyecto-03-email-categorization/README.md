# Project 3: Email → Auto-Categorization + Gmail Labels

![Status](https://img.shields.io/badge/Status-Done-success)
![Difficulty](https://img.shields.io/badge/Difficulty-Basic-green)
![Time](https://img.shields.io/badge/Time-3%20hours-blue)
![Tools](https://img.shields.io/badge/Tools-n8n%20%7C%20Gmail-purple)

🌐 **English** | [Español](./README.es.md)

## Description

An automation that classifies incoming Gmail messages and applies labels based on their content. When a new email arrives, the workflow analyzes the subject, identifies which category it belongs to based on keywords, and applies the matching Gmail label, all without manual work.

This project demonstrates the Gmail node (reading and labeling), conditional classification logic with the Switch node, and using expressions to evaluate multiple criteria efficiently.

## Use cases

- **Freelancers and consultants:** automatically separate client, billing, and support emails.
- **Technical support:** identify and prioritize urgent emails as they arrive.
- **Small businesses:** keep the inbox organized with no manual effort.
- **High email volume professionals:** reduce time spent sorting messages.

## Tech stack

| Component | Tool | Role |
|-----------|------|------|
| Source / destination | Gmail | Receives emails and stores labels |
| Orchestration | n8n (Cloud) | Detects, classifies, and labels |
| Trigger | Gmail Trigger | Fires when a new email arrives |
| Classification | Switch (with expressions) | Evaluates the subject against keywords |
| Action | Gmail (Add Label) | Applies the matching label |

## Workflow architecture

```
                              ┌──────────────────┐
                         ┌───▶│ Gmail: 🔴 Urgent  │
                         │    └──────────────────┘
                         │    ┌──────────────────┐
                         ├───▶│ Gmail: 💼 Clients │
┌──────────────┐  ┌──────┴─┐  └──────────────────┘
│ Gmail Trigger│─▶│ Switch │  ┌──────────────────────┐
│ (new email)  │  │(5 rules)├─▶│ Gmail: 🧾 Billing     │
└──────────────┘  └──────┬─┘  └──────────────────────┘
                         │    ┌──────────────────┐
                         ├───▶│ Gmail: 🤝 Support │
                         │    └──────────────────┘
                         │    ┌─────────────────────┐
                         └───▶│ Gmail: 📰 Newsletters│
                              └─────────────────────┘
```

## Categories and keywords

| Label | Keywords detected |
|-------|-------------------|
| 🔴 Urgent | urgente, urgent, asap, error, no funciona |
| 💼 Clients | propuesta, cotización, presupuesto, proposal, quote, contratar |
| 🧾 Billing | factura, invoice, pago, payment, recibo, transferencia, cobro |
| 🤝 Support | ayuda, help, soporte, support, duda, consulta, how to |
| 📰 Newsletters | newsletter, boletín, promoción, unsubscribe, oferta, descuento |

## Classification logic

Instead of a single word per category, each Switch route uses an **expression** that evaluates a whole list of keywords at once:

```javascript
{{ ["urgente","urgent","asap","error","no funciona"]
   .some(p => ($json.Subject || "").toLowerCase().includes(p)) }}
```

What this expression does:
- Takes the category's keyword list.
- `.some(...)` returns true if **at least one** keyword matches.
- `.toLowerCase()` makes detection **case-insensitive** ("URGENTE", "Urgente", "urgente" are treated the same).
- `($json.Subject || "")` avoids errors if an email arrives with no subject.
- `.includes(p)` checks whether the subject contains the word.

The Switch is configured with **"Send data to all matching outputs"**, allowing an email to receive **multiple labels** if it fits more than one category (e.g., "Urgent invoice" → 🧾 Billing + 🔴 Urgent).

## Concepts learned

- Configuring the **Gmail Trigger** and OAuth2 authentication with Google.
- Using the **Switch** node in "Rules" mode for conditional routing.
- Writing **expressions** in n8n (JavaScript) to evaluate multiple criteria.
- Case handling with `.toLowerCase()`.
- Applying labels with the **Gmail → Add Label** node.
- The difference between **running manually** and **publishing/activating** a workflow (key for it to react to real events).
- Multiple Switch outputs ("Send data to all matching outputs").

## How to implement

See [`setup-guide.md`](./setup-guide.md) for the full step-by-step guide.

## Freelance value

An email-organization automation like this is worth an estimated **$200–400 USD** on freelance platforms. It's in high demand among professionals and SMBs who receive high email volume and need to stay organized with no manual effort.

> 💡 Evolution: this project classifies by keyword rules. An advanced version with AI classification (understanding the email's content and intent) is planned as Project 10 of the portfolio.

## Screenshots

See the [`/assets`](./assets) folder for screenshots of the working workflow.

## Contact

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
