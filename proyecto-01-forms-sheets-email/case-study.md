# Case Study — Dental Clinic "Sonrisas"

## The client (illustrative example)

**Company:** "Sonrisas" Dental Clinic
**Industry:** Dental services
**Size:** 1 owner + 3 employees
**Contact:** Dr. José Martínez

## The problem

The clinic received inquiries by phone but didn't log them. Patient details were lost, no availability was tracked, and prospective patients often ended up going to a competitor.

The pain points:

- 2–3 lost inquiries per week.
- No record of who called or when.
- Patients received no confirmation.
- About 1 hour per day spent logging data manually.
- Estimated loss: ~$800/month.

## The proposed solution

An automated online form that replaces the manual phone-and-paper process:

1. The patient fills out an online form (available 24/7).
2. Data is saved automatically to a Google Sheet.
3. The doctor receives an immediate email alert.
4. The patient receives an automatic confirmation.

This way no inquiry is lost: it's archived, the doctor is notified right away, and the patient feels attended to from first contact.

## Implementation

Built with n8n, Google Forms, Google Sheets, and Gmail. A form captures the patient's details, a Google Sheets trigger detects each new submission, and Gmail sends both the patient confirmation and the doctor's alert. Setup took about two days.

## Expected results

| Metric | Before | After |
|--------|--------|-------|
| Inquiries captured/week | 12 | 18 |
| Time spent logging | 1 hour/day | 0 |
| Patient confirmations | 0% | 100% |
| Lost inquiries | Frequent | Zero |

## Project value

A capture-and-confirm automation like this is typically priced at **$200–400 USD** on freelance platforms, plus optional monthly maintenance. The return is direct: each recovered patient is worth far more than the cost of the automation, so it tends to pay for itself within days.

## Replicability

The same workflow applies to many appointment-based and professional services: medical and dental practices, psychologists, vets, physiotherapists, salons, language academies, gyms, lawyers, accountants, and consultants.

## Key technical takeaway

This foundational project shows that simple automations can deliver high ROI. The core skills — triggers, OAuth2 authentication, and data mapping between Google services — are the building blocks for every more complex automation in the portfolio.

---

*Note: This case study is illustrative, for portfolio purposes. Client data is fictional.*
