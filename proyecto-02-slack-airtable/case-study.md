# Case Study — Slack → Airtable + Automatic Reaction

## The client (illustrative example)

**Company:** TechSupport Solutions
**Industry:** Technical support for SMBs
**Size:** 12 employees
**Contact:** Laura Méndez, Support Coordinator

## The problem

TechSupport Solutions' support team received customer inquiries through a Slack channel. The process was entirely manual:

- An agent read each message and copied it into a spreadsheet.
- There was no quick way to know which inquiries had already been logged.
- Messages were lost during high-volume hours.
- There was no queryable record for later analysis.

Laura estimated the team spent **around 5 hours per week** just copying and organizing inquiries manually.

## The proposed solution

An automated workflow that:

1. **Detects** every message posted in the Slack inquiries channel.
2. **Saves** the author, message, date, and channel to an Airtable database automatically.
3. **Reacts** with a ✅ on the original message to visually mark it as logged.

## Implementation

Built in n8n with four steps: message reception (Webhook), confirmation to Slack (Respond to Webhook), storage (Airtable), and visual confirmation (Slack reaction).

The Slack → Airtable integration is fully functional and tested: each message is saved with its four structured fields (author, content, timestamp, channel), ready to filter, sort, and analyze.

## Expected results

| Metric | Before | After |
|--------|--------|-------|
| Logging time per inquiry | ~2 min (manual) | Instant (automatic) |
| Lost inquiries | Frequent | Zero |
| Queryable record | No | Yes (Airtable) |
| Weekly manual logging time | ~5 hours | ~0 hours |

## Project value

For a real client, an automation like this is priced between **$250 and $500 USD**, depending on the complexity of the fields and business rules. The ROI is clear: recovering ~5 hours of manual work per week justifies the cost within a few weeks.

## Key technical takeaway

This project consolidated webhooks, OAuth authentication, permission scopes, and field mapping between systems — a foundation reused in virtually any application-to-application integration.

---

*Note: This case study is illustrative, for portfolio purposes. Client data is fictional.*
