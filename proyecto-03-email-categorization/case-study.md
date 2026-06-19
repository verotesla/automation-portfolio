# Case Study — Email → Auto-Categorization + Gmail Labels

## The client (illustrative example)

**Professional:** Carlos Ramírez
**Role:** Freelance digital marketing consultant
**Situation:** Works solo and manages all communication by email

## The problem

Carlos receives 40–60 emails a day, mixed together with no order in his inbox: proposals from prospects, supplier invoices, support requests from current clients, newsletters he's subscribed to, and urgent emails needing immediate response.

The main problem wasn't the volume, but the **lack of prioritization**:

- Urgent emails got lost among newsletters and promotions.
- Finding billing emails at month-end took time.
- He couldn't quickly tell a new client (proposal) from an existing one (support).
- He spent time each morning just mentally "sorting" his inbox.

## The proposed solution

A workflow that automatically classifies each incoming email and applies a color label by type, as soon as it arrives:

- 🔴 **Urgent** — emails needing immediate attention
- 💼 **Clients** — proposals and prospects
- 🧾 **Billing** — payments, invoices, and receipts
- 🤝 **Support** — help requests from current clients
- 📰 **Newsletters** — bulletins and promotions

## Implementation

Built in n8n with a Gmail trigger that detects each new email, a Switch node that evaluates the subject against keyword lists (in Spanish and English), and five Gmail nodes that apply the matching label.

The classification was designed to be flexible: it detects multiple words per category, ignores case, and lets a single email receive multiple labels when applicable (e.g., an urgent invoice).

## Expected results

| Metric | Before | After |
|--------|--------|-------|
| Daily time sorting email | ~20 min | ~0 min |
| Urgent emails flagged instantly | No | Yes (🔴 label) |
| Inbox organized by type | No | Yes (automatic) |
| Manual classification effort | Daily | None |

## Project value

An email-organization automation is priced between **$200 and $400 USD** on freelance platforms. It's in high demand because it solves a universal pain (the messy inbox) with a low investment and a tangible daily benefit.

## Key technical takeaway

This project consolidated the Gmail node (reading and labeling), conditional logic with the Switch node, and above all the use of **expressions** to evaluate multiple criteria efficiently, instead of creating dozens of manual conditions. It also reinforced a fundamental n8n lesson: a workflow must be **published and active** to react to events in real time.

---

*Note: This case study is illustrative, for portfolio purposes. Client data is fictional.*
