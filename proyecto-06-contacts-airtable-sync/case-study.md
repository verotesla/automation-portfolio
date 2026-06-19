# Case Study — Two-Way Sync Contacts ↔ Airtable

## The client (illustrative example)

**Company:** Horizonte Real Estate
**Industry:** Real estate
**Size:** 8 agents
**Contact:** Roberto Salas, sales manager

## The problem

Horizonte's agents managed their prospects in a shared Airtable base (with notes, sales stage, property of interest, etc.). But to call or message via WhatsApp, they needed the contacts on their phones, i.e., in Google Contacts.

The process was manual and problematic:

- Each agent manually copied prospects from Airtable to their phone.
- When someone captured a contact on their phone, it never reached the shared base.
- Duplicates and outdated data piled up on both sides.
- Time was lost and transcription errors crept in (mistyped phone numbers).

They needed both systems to stay in sync automatically, with no duplicates and no manual work.

## The proposed solution

An automatic two-way synchronization between Google Contacts and Airtable:

1. **Google → Airtable:** phone contacts are reflected in the shared base, updating without duplicating.
2. **Airtable → Google:** new prospects captured in the base automatically appear in Google Contacts, ready to contact from the phone.

All with two key guarantees: **zero duplicates** (via upsert) and **zero loops** (via the control column).

## Implementation

Two independent workflows were built in n8n. The first pulls Google contacts and inserts or updates them in Airtable using the email as a unique identifier (idempotent upsert). The second detects new contacts created in Airtable, creates them in Google, and marks them as synced so they aren't reprocessed, avoiding infinite loops.

The main technical challenge was handling the nested, irregular structure of Google's data and designing the anti-loop mechanism, which is what separates a safe sync from one that spirals out of control.

## Expected results

| Metric | Before | After |
|--------|--------|-------|
| Manual contact copying | Daily, per agent | Automatic |
| Duplicates | Frequent | Zero (upsert) |
| Outdated data | Constant | Synced |
| Transcription errors | Common | Eliminated |

## Project value

A two-way synchronization is priced between **$500 and $900 USD** on freelance platforms, due to its complexity. It's not a trivial integration: it requires handling duplicates, preventing loops, and working with irregular data structures. Few freelancers know how to implement it correctly, which makes it a differentiating, well-paid service.

## Key technical takeaway

This was the most technically demanding project in the portfolio to this point. It consolidated three data-engineering concepts: **idempotency** (running without duplicating, via upsert), **loop prevention** (with a state control column), and **handling nested data** from real APIs. It also reinforced an essential professional practice: inspecting the real structure of the data before mapping it, instead of assuming it.

---

*Note: This case study is illustrative, for portfolio purposes. Client data is fictional.*
