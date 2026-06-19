# Case Study — Inquiry Capture with Fan-out (Typeform → Asana + Slack + Sheets)

## The client (illustrative example)

**Company:** PixelForge Studio
**Industry:** Design and web development agency
**Size:** 6 people
**Contact:** Daniel Ortega, director

## The problem

PixelForge captures clients through a form on their website. The downstream process was manual, and opportunities slipped through:

- Inquiries arrived by email and someone had to copy them into a spreadsheet.
- The team didn't find out in time that a new inquiry had arrived.
- No follow-up task was created, so some inquiries went unanswered.
- There was no single place to see the status of each prospect.

The result: lost prospects from delays and missing follow-up, plus wasted time on copy-and-paste.

## The proposed solution

An automated flow that, on each form submission, triggers **three actions at once** (fan-out pattern):

1. **Logs** the inquiry to a Google Sheet (queryable backup).
2. **Notifies** the team in a Slack channel instantly.
3. **Creates a task** in Asana to ensure follow-up.

This way no inquiry is lost: it's archived, the team is notified immediately, and there's always an assigned task to follow up.

## Implementation

Built in n8n with a Typeform trigger that branches into three independent paths. Each branch takes the data directly from the form and sends it to its destination: Google Sheets (log), Slack (alert), and Asana (task). All three run in parallel from a single event.

## Expected results

| Metric | Before | After |
|--------|--------|-------|
| Inquiry logging | Manual (copy/paste) | Automatic |
| Time to learn of an inquiry | Hours | Instant (Slack) |
| Inquiries with assigned follow-up | Inconsistent | 100% (Asana task) |
| Lost inquiries | Frequent | Zero |

## Project value

A fan-out capture automation is priced between **$400 and $700 USD** on freelance platforms, depending on the number of destinations and business rules. Its appeal is that it automates the full capture-and-assignment process, connecting several tools the business already uses. The return is direct: each recovered prospect is worth far more than the cost of the automation.

## Key technical takeaway

This project consolidated the **fan-out** pattern: a single trigger that branches into several independent, parallel actions. The key technical point was that all branches reference the same source data with `$('Typeform Trigger').item.json[...]`, instead of chaining one after another. It also reinforced diagnosing integration errors (Slack Channel ID, bot permissions) and handling field names with special characters.

---

*Note: This case study is illustrative, for portfolio purposes. Client data is fictional.*
