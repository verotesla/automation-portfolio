# Project 6: Two-Way Sync Google Contacts ↔ Airtable

![Status](https://img.shields.io/badge/Status-Done-success)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-orange)
![Time](https://img.shields.io/badge/Time-5%20hours-blue)
![Tools](https://img.shields.io/badge/Tools-n8n%20%7C%20Google%20Contacts%20%7C%20Airtable-purple)

🌐 **English** | [Español](./README.es.md)

## Description

A two-way synchronization between Google Contacts and Airtable: contacts flow in **both directions** automatically and safely. Google contacts are reflected in Airtable (without duplicating), and new contacts created in Airtable are created in Google Contacts (without generating infinite loops).

This project solves two of the hardest problems in data synchronization: **idempotency** (running the flow many times without creating duplicates) and **loop prevention** (stopping a change in one system from bouncing infinitely between both).

## Architecture: two coordinated workflows

The two-way sync was implemented with **two separate workflows**, a deliberate design choice: independent workflows are safer, easier to maintain, and let you turn off one direction without affecting the other.

### Workflow 1 — Google Contacts → Airtable

```
┌──────────┐   ┌───────────────────┐   ┌────────────────────┐
│ Schedule │──▶│ Get many contacts │──▶│ Airtable (Upsert)  │
│ Trigger  │   │ (Google People)   │   │ match by Email     │
└──────────┘   └───────────────────┘   └────────────────────┘
```

### Workflow 2 — Airtable → Google Contacts

```
┌──────────┐   ┌──────────────────────┐   ┌──────────────┐   ┌─────────────────────┐
│ Schedule │──▶│ Airtable Search      │──▶│ Google       │──▶│ Airtable Update     │
│ Trigger  │   │ (not synced)         │   │ Create       │   │ (mark ✅)           │
└──────────┘   └──────────────────────┘   └──────────────┘   └─────────────────────┘
```

## Key technical highlights

### 1. Idempotent upsert (Google → Airtable)

Instead of "always create" (which would duplicate on every run), it uses **Upsert** (update + insert) with **Email** as the matching column. If the contact already exists in Airtable, it's updated; if not, it's created.

**Result:** the workflow can run infinitely and the data stays clean, with no duplicates. This is called **idempotency** and is a fundamental property of reliable synchronization.

### 2. Loop prevention (Airtable → Google)

The biggest risk of two-way sync is the **infinite loop**: a change in A updates B, which re-triggers A, and so on forever. This was solved with a **control column** in Airtable: `Sincronizado a Google` (checkbox).

- Workflow 2 only processes contacts where the checkbox is **unchecked** (filter `NOT({Sincronizado a Google})`).
- After creating the contact in Google, it **checks the box** ✅.
- On the next run, that contact is no longer processed.

**Result:** each contact syncs exactly once. No loops. The system is safe to run continuously.

### 3. Handling nested data from a real API

The Google People API returns data with a nested, irregular structure. Safe-access expressions (`?.`) were used to extract it without breaking the flow when a field is missing:

```javascript
{{ $json.names?.displayName }}              // name
{{ $json.phoneNumbers?.mobile?.[0] }}       // phone
{{ Object.values($json.emailAddresses || {})[0]?.[0] }}  // email (irregular structure)
```

### 4. Filtering special cases

Many contacts have no email. Since the Upsert uses email as the identifier, a **Filter** node was added to pass only contacts with email, avoiding the "Record must include columns to merge on" error.

## Use cases

- **Sales teams:** capture leads in Airtable and want them available on their phone (Google Contacts) to call/message.
- **Centralized contact management:** keep a master base in Airtable synced with the personal address book.
- **Migration and backup:** mirror contacts between systems without losing data or duplicating.

## Tech stack

| Component | Tool | Role |
|-----------|------|------|
| Trigger | Schedule Trigger | Runs the sync periodically |
| Source/destination A | Google Contacts (People API) | Contact address book |
| Source/destination B | Airtable | Contact database |
| Orchestration | n8n (Cloud) | Coordinates both directions |
| Loop control | Airtable checkbox column | Prevents reprocessing contacts |

## Concepts learned

- **Schedule + Get/Search pattern:** sync by periodic polling when there's no event trigger.
- **Upsert and idempotency:** create or update without duplicating, using a matching column.
- **Loop prevention** with a state control column.
- **Safe access to nested data** with `?.` and handling irregular API structures.
- **Filters (Filter node)** to handle special cases (contacts without email).
- **Airtable Filter By Formula** (`NOT({field})`) for selective queries.
- **Two-workflow architecture** for safe two-way synchronization.

## Limitations and scope

This implementation covers creating new contacts in both directions safely. It does **not** include resolving simultaneous-edit conflicts (when the same contact is modified in both systems at once), which is an advanced "last-write-wins" or "field-merge" problem. The current design prioritizes safety (no loops, no duplicates) over real-time edit synchronization.

## How to implement

See [`setup-guide.md`](./setup-guide.md) for the full step-by-step guide for both workflows.

## Freelance value

A two-way synchronization between systems is worth an estimated **$500–900 USD** on freelance platforms, due to its technical complexity (handling duplicates, loops, and nested data). It's a service few know how to implement correctly, which makes it especially valuable.

## Screenshots

See the [`/assets`](./assets) folder for screenshots of both workflows working.

## Contact

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
