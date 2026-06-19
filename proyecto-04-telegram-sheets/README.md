# Project 4: Telegram Bot → Google Sheets + Smart Replies

![Status](https://img.shields.io/badge/Status-Done-success)
![Difficulty](https://img.shields.io/badge/Difficulty-Basic-green)
![Time](https://img.shields.io/badge/Time-3%20hours-blue)
![Tools](https://img.shields.io/badge/Tools-n8n%20%7C%20Telegram%20%7C%20Google%20Sheets-purple)

🌐 **English** | [Español](./README.es.md)

## Description

A Telegram bot that automatically logs every message it receives to a Google Sheet and replies differently based on the message content. It combines data logging with a keyword-based auto-reply system.

When a user messages the bot, the message is saved to Sheets (with readable date, user, content, and chat ID) and the bot replies appropriately when it detects greetings, price questions, hours, support, or location.

> 📌 **Design note:** the original plan used WhatsApp as the input channel. Telegram was chosen because its API is more accessible (no business verification or Meta WhatsApp Cloud API required), letting the focus stay on automation concepts. WhatsApp integration is covered in later portfolio projects (13 and 19).

## Use cases

- **Automated customer service:** answer FAQs (price, hours, location) with no human intervention.
- **Inquiry logging:** keep a sorted, queryable history of all received messages.
- **Order or report intake:** capture messages from a Telegram channel and archive them automatically.
- **First-contact bot:** filter and guide users before handing off to a human agent.

## Tech stack

| Component | Tool | Role |
|-----------|------|------|
| Input/output channel | Telegram (bot) | Receives messages and sends replies |
| Orchestration | n8n (Cloud) | Captures, saves, classifies, and replies |
| Trigger | Telegram Trigger | Fires on each new message |
| Storage | Google Sheets | Logs each message |
| Classification | Switch (with expressions) | Decides the reply based on keywords |
| Reply | Telegram (Send Message) | Sends the appropriate message |

## Workflow architecture

```
┌────────────────┐   ┌──────────────────┐   ┌────────────┐
│ Telegram       │──▶│ Google Sheets    │──▶│  Switch    │
│ Trigger        │   │ (Append Row)     │   │ (6 outputs)│
│ (new message)  │   │ saves the message│   └─────┬──────┘
└────────────────┘   └──────────────────┘         │
                                                   ├─▶ Telegram: Greeting
                                                   ├─▶ Telegram: Price
                                                   ├─▶ Telegram: Hours
                                                   ├─▶ Telegram: Help
                                                   ├─▶ Telegram: Location
                                                   └─▶ Telegram: Default (Fallback)
```

## Bot replies

| If the message contains... | The bot replies... |
|----------------------------|--------------------|
| hola, buenas, hi, hello | Welcome greeting |
| precio, costo, cuánto, cotización | Pricing info |
| horario, abierto, atención, hora | Business hours |
| ayuda, soporte, problema, help | Support message |
| ubicación, dónde, dirección | Business location |
| *(anything else)* | Receipt confirmation (Fallback) |

## Data logged to Google Sheets

| Column | Content | Source |
|--------|---------|--------|
| Date | Readable date/time (dd/MM/yyyy HH:mm:ss) | Converted Telegram timestamp |
| User | Sender name | `message.from.first_name` |
| Message | Message text | `message.text` |
| ChatID | Chat ID (for replying) | `message.chat.id` |

## Classification logic

Each Switch route uses an expression that evaluates a keyword list at once. Important: the Switch receives the data **after** it passes through Google Sheets, so it evaluates the `Mensaje` field (the column name), not the original Telegram `message.text`:

```javascript
{{ ["hola","buenas","hi","hello"]
   .some(p => ($json.Mensaje || "").toLowerCase().includes(p)) }}
```

The Switch is configured with a **Fallback Output** that catches any message not matching a category, guaranteeing the bot always replies.

## Date conversion (timestamp → readable)

Telegram delivers the date as a Unix timestamp (seconds). It's converted to a readable format with the Luxon library built into n8n:

```javascript
{{ DateTime.fromSeconds($json.message.date).toFormat('dd/MM/yyyy HH:mm:ss') }}
```

This turns `1781556829` into `15/06/2026 14:30:45`.

## Concepts learned

- Creating a Telegram bot with **BotFather** and connecting via token.
- Configuring the **Telegram Trigger** (message reception).
- Saving data to **Google Sheets** (Append Row) with field mapping.
- **Date conversion** with `DateTime` (Luxon): from Unix timestamp to readable format.
- Differentiated replies with the **Switch** node and a **Fallback Output**.
- Sending messages with Telegram and disabling n8n's automatic attribution.
- **Key lesson:** data changes structure as it passes through each node. The Switch evaluates what comes out of Google Sheets (`Mensaje`), not the original `message.text`. Always check each node's INPUT panel to use the correct field names.

## How to implement

See [`setup-guide.md`](./setup-guide.md) for the full step-by-step guide.

## Freelance value

A Telegram bot with logging and auto-replies is worth an estimated **$250–450 USD** on freelance platforms. It appeals to small businesses that want automated first-contact service and a tidy inquiry history, without the cost of more complex platforms.

> 💡 Evolution: this bot replies via keyword rules. A version with conversational AI (understanding natural language and keeping context) is planned in projects 13 and 16 of the portfolio.

## Screenshots

See the [`/assets`](./assets) folder for screenshots of the workflow and the bot in action.

## Contact

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
