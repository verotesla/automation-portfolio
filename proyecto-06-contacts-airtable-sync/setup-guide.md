# Setup Guide — Project 6: Two-Way Sync Contacts ↔ Airtable

This guide documents how to build a safe two-way synchronization between Google Contacts and Airtable, using two workflows.

---

## Prerequisites

- [n8n.cloud](https://n8n.cloud) account
- Google account with contacts
- Airtable account
- Estimated time: 4–5 hours

---

## Part 1 — Airtable table

Create a table `Contactos` with these fields:

| Field | Type |
|-------|------|
| Nombre | Single line text |
| Email | Email or Single line text |
| Teléfono | Phone or Single line text |
| Empresa | Single line text |
| Última actualización | Single line text |
| Sincronizado a Google | **Checkbox** |

> The "Última actualización" field is kept as text and formatted from n8n with Luxon (`$now.toFormat(...)`), for full control over the format.

---

## WORKFLOW 1 — Google Contacts → Airtable

### Step 1: Schedule Trigger
Create workflow `Google Contacts to Airtable`. Add **Schedule Trigger** (hourly, or more frequent for testing).

### Step 2: Get many contacts
1. Add **Google Contacts → Get Many**.
2. **Credential:** OAuth2 (requires Contacts permission; an existing Google credential may need re-authorization).
3. **Return All:** enabled.
4. **Fields:** select Names, Email Addresses, Phone Numbers, Organizations.

> ⚠️ Common error: `personFields mask is required`. Fixed by selecting the Fields. Google requires specifying which data to fetch.

### Step 3: Filter (only contacts with email)
1. Add a **Filter** node between Get contacts and Airtable.
2. Condition: email is NOT empty.
   - Value: `{{ Object.values($json.emailAddresses || {})[0]?.[0] }}`
   - Operator: **is not empty**

> ⚠️ Without this filter, contacts without email cause the "Record must include columns to merge on" error.

### Step 4: Airtable Upsert
1. Add **Airtable → Create or Update (Upsert)**.
2. **Credential:** Airtable (grant access to the Contactos base when reconnecting).
3. **Base:** Contactos. **Table:** the table.
4. **Columns to match on:** **Email**.
5. Map (per the real Google People structure):

| Column | Expression |
|--------|-----------|
| Email | `{{ Object.values($json.emailAddresses || {})[0]?.[0] }}` |
| Nombre | `{{ $json.names?.displayName }}` |
| Teléfono | `{{ $json.phoneNumbers?.mobile?.[0] }}` |
| Empresa | `{{ $json.organizations?.name }}` |
| Última actualización | `{{ $now.toFormat('dd/MM/yyyy HH:mm:ss') }}` |

> 💡 Important: inspect the raw JSON of Get contacts before mapping. The real structure may differ from what's expected (e.g., `names` is a direct object, not a list).

6. Execute and verify in Airtable. Run again: it must NOT duplicate (idempotent upsert).

---

## Anti-loop preparation (one time)

Before building Workflow 2, mark as **Sincronizado a Google ✅** all contacts that already came from Google (those in Airtable). This leaves only the Airtable-born contacts as "not synced".

This is done once, manually in Airtable, to set a clean starting point.

---

## WORKFLOW 2 — Airtable → Google Contacts

### Step 1: Schedule Trigger
Create a separate workflow `Airtable to Google Contacts`. Add **Schedule Trigger**.

### Step 2: Airtable Search (not synced)
1. Add **Airtable → Search**.
2. **Base:** Contactos. **Table:** the table.
3. **Return All:** enabled.
4. **Filter By Formula:**
   ```
   NOT({Sincronizado a Google})
   ```

> This formula (Airtable syntax) returns only records with the checkbox unchecked. It's the heart of the anti-loop.

### Step 3: Google Contacts Create
1. Add **Google Contacts → Create**.
2. **Given Name:** `{{ $json.fields.Nombre }}`
3. In **Additional Fields**:
   - **Email → Value:** `{{ $json.fields.Email }}` (Type: Home, fixed value)
   - **Phone → Value:** `{{ $json.fields.Teléfono }}` (Type: Mobile, fixed value)

> ⚠️ Common error: putting the same expression in Type and Value. The **Type** is the label (home/mobile) and stays fixed or empty; the **Value** is the actual data. Email goes only in Email→Value; phone only in Phone→Value.

### Step 4: Airtable Update (mark as synced)
1. Add **Airtable → Update**.
2. **Base:** Contactos. **Table:** the table.
3. **Columns to match on:** id.
4. **id (using to match):** `{{ $('Search records').item.json.id }}`
5. **Sincronizado a Google:** enable (true).

> This closes the anti-loop: it marks the just-created contact so it won't be reprocessed.

---

## Final tests

1. Run Workflow 2 → creates the not-synced contacts in Google and marks them.
2. Run again → "No output data" (nothing left to sync). ✅ Anti-loop confirmed.
3. Create a new contact in Airtable (unchecked) → run → it's created in Google and marked.
4. Verify in Google Contacts that the contact appears complete (name, email, phone).

---

## Errors and fixes

| Error / Situation | Cause | Fix |
|-------------------|-------|-----|
| `personFields mask is required` | Fields not specified | Select Fields (Names, Emails, Phones, Orgs) |
| "Contactos" base doesn't appear | Credential lacked access | Reconnect Airtable and add the base |
| Expressions return `undefined` | Real structure differs from expected | Inspect raw JSON and adjust the paths |
| "Record must include columns to merge on" | Contacts without email + match by email | Add Filter "email is not empty" |
| Search returns all records | No contact was marked | Mark the Google ones manually once |
| Email/phone wrong in Google | Expression in Type instead of Value | Email→Value and Phone→Value; Type fixed |

---

## Possible future improvements

- **Conflict resolution:** handle simultaneous edits (last-write-wins or field merge).
- **Two-way update of existing contacts** (not just creating new ones).
- **Synced deletion:** reflect removals between systems.
- **Split name and surname** (Given Name / Family Name) instead of a single field.
