|---
name: CRM system-create-cr
description: "Walk the user through creating a Change Request (change request record) in CRM system via the enterprise software company-CRM system MCP. Defaults Record Type to enterprise software company, discovers picklists live, asks one field at a time, confirms before insert, and retries on validation-rule errors. Triggers on: create a change request, create a CR, new CR in CRM system, log a change request, open a CR, file a CR."
---

# CRM system Change Request Creation

Guide the user through creating a `change request record` record in CRM system using the `enterprise software company-CRM system` MCP server. Ask **one field at a time**. Always discover picklist values live. **Record Type defaults to enterprise software company** unless the user explicitly overrides.

---

## Core rules

- **Record Type is pinned to enterprise software company.** Always use `the configured change request record type for the CRM system = 0123l000001TQMVAA4`. Do not ask the user to pick a record type, and do not run the `RecordType` query. Only override if the user explicitly names a different record type.
- **Discover picklists live.** Call `describeSObject` (scoped by `fieldNamePattern`) for each picklist you need. Picklist values change without notice — never hardcode them from this file.
- **Never set system or formula fields.** Do not attempt to set `Name` (auto-number), `Private__c` (trigger-set), or any `*_Link__c`, `Days_Open__c`, `Weeks_Open__c`, `Total_Days_Open__c`, `Affected_Customer_Count__c`, `Initial_Opened_Date__c`. Don't populate legacy migration fields (`Legacy_CR_Number__c`, `Legacy_CR_System__c`) on new CRs.
- **Confirm before insert.** Show a full summary of the record and get an explicit "yes" before calling `create`.
- **Ask one field at a time.** The user chose a step-by-step flow. Don't batch questions.
- **Treat validation errors as a loop, not a failure.** If the insert is rejected by a validation rule, quote the message, ask for the missing/corrected value, and retry.

---

## Step 1 — Record Type (fixed)

**Always use the enterprise software company record type:** `the configured change request record type for the CRM system = 0123l000001TQMVAA4`.

Skip the `RecordType` query and the "which record type?" question entirely. Only if the user explicitly says "use record type X" (e.g. "use IT", "make it a GasBuddy CR"), run the query below and pick their choice instead:

```sql
SELECT Id, Name, DeveloperName
FROM RecordType
WHERE SobjectType = 'change request record' AND IsActive = true
ORDER BY Name
```

**Always use per-field describes.** For each picklist, call `describeSObject` scoped by `fieldNamePattern` right before you ask the user that question. **Never attempt a full-object describe** on `change request record` — it always exceeds the response token limit. Even with `fieldNamePattern` the response re-serializes all `childRelationships` and `recordTypeInfos` (~40k tokens of unavoidable overhead) — ignore that noise and read the `fields.items[]` array.

---

## Step 2 — Collect fields (one at a time)

Ask in this order. For picklists, present the **full list returned by describe** (not a hardcoded list from memory). For dependent picklists, see Step 3.

| # | Question | API field | Type | Notes |
|---|---|---|---|---|
| 1 | One-line subject? | `Subject__c` | string(255) | The only field that is strictly required at the schema level. |
| 2 | Class? | `Class__c` | picklist | Use values from describe. |
| 3 | Type? | `Type__c` | picklist | Use values from describe. |
| 4 | Which product? | `Product__c` | picklist | **Known MCP truncation** — see note below the table. |
| 5 | Priority? | `Priority__c` | picklist | Use values from describe. |
| 6 | Description — what should change and why? | `Description__c` | textarea(32000) | Multi-line ok. |
| 7 | Who is affected and how? | `Customer_Impact__c` | textarea(32000) | Recommend for priorities "Critical" / "Urgent". |
| 8 | Steps to reproduce? | `Steps_to_Reproduce__c` | textarea(32000) | **Only ask if Class = `Defect`.** |
| 9 | Risk level? | `Risk__c` | picklist | Optional. Use values from describe. |
| 10 | Scope / size? | `Scope__c` | picklist | Optional. Use values from describe. |
| 11 | Requested completion date? | `Requested_Completion_Date__c` | date | Optional. Format YYYY-MM-DD. |
| 12 | Target version(s)? | `Target_Version__c` | string(255) | Optional. |
| 13 | Existing ADO / JIRA / Aha ticket? | `Azure_DevOps_Record_ID__c`, `JIRA_Record_ID__c`, `Aha_Record_ID__c` | string | Plain ID strings. **Do not build URLs** — the `*_Link__c` formula fields do that automatically. |

Skip any field the user declines. Don't pad the record with empty strings.

### Product field truncation (row 4)

The `enterprise software company-CRM system` MCP truncates the `Product__c` picklist to ~10 values (A–C range only) regardless of page size. **This is an MCP limitation, not a CRM system limit** — confirmed by real CRs using values like "Enterprise" that don't appear in the truncated list.

When you reach row 4:

1. Call `describeSObject` with `fieldNamePattern: "Product__c"` anyway (for transparency).
2. Tell the user: *"The MCP only returns ~10 products (A–C). Type the exact product name you want — e.g. Enterprise, GasBuddy, Workforce, Bridge — and I'll send it as-is. CRM system will reject invalid values and we'll retry."*
3. Send whatever the user types in the `create` payload. If CRM system rejects with `INVALID_OR_NULL_FOR_RESTRICTED_PICKLIST`, go to Step 6's retry loop.

Do **not** force the user to pick from the truncated list — they almost certainly want a value that isn't in it.

### Skip-rest shortcut

After the user skips their **first** optional field (rows 9–13: Risk, Scope, Requested Date, Target Version, External ticket IDs), offer:

> "Skip the rest of the optional fields and go to confirmation?"

If the user says yes (or "skip all", "done", "just create it"), jump directly to Step 4. Don't keep asking about Scope / Date / Version / External IDs individually. This shortcut does **not** apply to required-ish fields (Subject, Class, Type, Product, Priority, Description) or to conditionals (Customer Impact for Critical/Urgent, Steps to Reproduce for Defect) — always ask those.

---

## Step 3 — Dependent picklists (only if the user wants them)

These four fields have `validFor` bitmaps and are likely controlled by Record Type or Product:

- `Product_Menu__c`
- `Product_Topic__c`
- `Product_Area__c`
- `Topic__c`

If the user wants to set any of these:

1. Re-run `describeSObject` with `fieldNamePattern` scoped to the specific field **after** Record Type and Product are chosen.
2. Present the full picklist and warn: "the system may reject values that don't apply to your record type or product — I'll retry if so."
3. Do not attempt to decode the `validFor` bitmap manually; rely on the server's validation to reject invalid combinations.

---

## Step 4 — Confirm

Show a tidy summary before inserting. Always include "Record Type: enterprise software company" as the first line so the user sees the default.

```
Record Type:       enterprise software company
Subject:           Upgrade auth middleware to meet compliance
Class:             Enhancement
Type:              Internal
Product:           Platform Services
Priority:          2 - Urgent
Risk:              Medium
Scope:             Medium
Requested Date:    2026-05-30
Description:       <first 120 chars>… (full value will be sent)
Customer Impact:   <first 120 chars>…
```

Ask exactly: **"Create this CR? (yes / edit <field> / cancel)"**

- `yes` → proceed to Step 5.
- `edit <field>` → re-ask just that field, re-show the summary.
- `cancel` → stop. Do not insert.

---

## Step 5 — Create

Call `mcp__enterprise software company-CRM system__create`:

```json
{
  "sObjectType": "change request record",
  "record": {
    "the configured change request record type for the CRM system": "0123l000001TQMVAA4",
    "Subject__c": "...",
    "Class__c": "...",
    "Type__c": "...",
    "Product__c": "...",
    "Priority__c": "...",
    "Description__c": "..."
  }
}
```

(Use a different `the configured change request record type for the CRM system` only if the user explicitly requested a non-enterprise software company record type in Step 1.)

Include only fields the user actually answered.

On success, fetch the auto-number `Name`:

```sql
SELECT Id, Name FROM change request record WHERE Id = '<new-id>'
```

Present to the user:

```
Created CR <Name> — Id <Id>
https://profdata.lightning.force.com/lightning/r/change request record/<Id>/view
```

The enterprise software company org's Lightning base URL is `https://profdata.lightning.force.com` — always use this exact URL pattern. Don't call `getOrganizationInfo` to derive it.

---

## Step 6 — Validation errors (retry loop)

Example error: `FIELD_CUSTOM_VALIDATION_EXCEPTION: Product is required for IT record type`.

Do this, not that:

1. **Quote the error verbatim** to the user.
2. **Ask specifically** for the field the error names. Don't re-walk the whole flow.
3. **Merge the new value** into the existing record payload and retry `create`.
4. **Cap at 3 retries.** If still failing, stop and surface the full error; don't loop indefinitely.

If the error is about a picklist value not being valid for the record type (common with dependent picklists), re-describe the field and re-ask — don't just pass the user's original answer again.

---

## Anti-patterns (don't do these)

- Don't skip the Step 4 confirmation, even if the user says "just create it."
- Don't hardcode picklist values from this file — always re-fetch via `describeSObject`.
- Don't ask the user to pick a record type. enterprise software company is the default; override only when the user names another record type unprompted.
- Don't set `OwnerId` unless the user explicitly asks to assign the CR to someone else (leave it to the default trigger).
- Don't populate formula or legacy-migration fields.
- Don't mask validation errors with your own interpretation — quote them verbatim first.
