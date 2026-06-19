# Case Study — Telegram Bot with Logging and Auto-Replies

## The client (illustrative example)

**Business:** "Equilibrio" Yoga Studio
**Type:** Small studio with 2 instructors
**Situation:** Receives Telegram inquiries at all hours, even outside business hours

## The problem

The studio promotes its Telegram on social media and receives a constant flow of messages with the same questions: class prices, available schedules, location, and help with booking.

The specific problems:

- The instructors answered the same questions dozens of times a day.
- Many messages arrived overnight or during classes and went unanswered for hours.
- They had no record of how many people inquired or what they asked most.
- They lost potential clients who didn't get a timely response.

## The proposed solution

A Telegram bot that:

1. **Replies instantly** to FAQs (price, hours, location), 24/7.
2. **Logs every message** to a spreadsheet, with date, sender, and content.
3. **Gives a courtesy reply** to any message that doesn't fit a category, confirming it was received.

This way, basic inquiries resolve themselves and the instructors only step in when personalized attention is needed.

## Implementation

Built in n8n with a Telegram trigger that detects each message, a Google Sheets node that logs it (with the date converted to a readable format), and a Switch node that evaluates the content against keyword lists to send the right reply. A Fallback guarantees no message goes unanswered.

## Expected results

| Metric | Before | After |
|--------|--------|-------|
| FAQ responses | Manual, delayed | Instant, 24/7 |
| Unanswered messages | Frequent | Zero (always a reply) |
| Inquiry log | None | Automatic spreadsheet |
| Instructor time repeating answers | High | Minimal |

## Project value

A Telegram bot with logging and auto-replies is priced between **$250 and $450 USD** on freelance platforms. For a small business, the benefit is twofold: it saves staff time and improves the customer experience with instant replies, without paying for an expensive support platform.

## Key technical takeaway

This project integrated three skills: message capture (Telegram Trigger), structured storage (Google Sheets with date conversion via Luxon), and conditional reply logic (Switch with Fallback). The most valuable lesson was understanding that **data changes structure as it passes through each node**: the Switch had to evaluate the field coming out of Google Sheets (`Mensaje`), not Telegram's original data. Diagnosing this by checking each node's input panel is a fundamental automation skill.

---

*Note: This case study is illustrative, for portfolio purposes. Client data is fictional.*
