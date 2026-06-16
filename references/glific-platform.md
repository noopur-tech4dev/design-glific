# Glific Platform Knowledge

## What Glific is

Glific is an **open-source WhatsApp communication platform built for NGOs and social sector organisations**, primarily in India. It enables organisations to have structured, automated, and human-assisted two-way conversations with their beneficiaries at scale over WhatsApp.

It is not a general chatbot builder. Its users are NGOs working in health, education, livelihoods, and social welfare — reaching beneficiaries who are often first-time smartphone users on low-end Android devices with limited data.

**Key hosted instances:** glific.com (cloud-hosted); organisations get their own subdomain.  
**Tech stack:** Elixir/Phoenix backend, React frontend, PostgreSQL.  
**WhatsApp access:** via BSP (Business Solution Provider) connected to the WhatsApp Business API.

---

## Core concepts

### Contacts
People the NGO communicates with — beneficiaries, field workers, volunteers. Each contact is a WhatsApp number. Contacts can be tagged, grouped into collections, and assigned to staff. All conversations are stored and searchable.

### Flows
Automated conversation sequences — the "chatbot" layer. A flow is a decision tree of messages, questions, and branching logic. When a contact sends a message or is enrolled in a flow, Glific walks them through it step by step. Flows are built in a visual builder and can trigger other flows, update contact fields, send templates, or hand off to a human.

### HSM Templates (Highly Structured Messages)
Pre-approved WhatsApp message formats required for **outbound messages** — messages sent to contacts who have not initiated a conversation in the last 24 hours. Every HSM template must be submitted to Meta (WhatsApp's parent company) and approved before use. This is a hard platform constraint, not a Glific limitation.

Key HSM constraints (absorbed from the redesign project):
- **Template ID (element name) is permanent** — cannot be changed after submission to Meta
- **Each language version is a separate Meta submission** — EN and HI of the same template are reviewed independently; they can have different statuses (approved/pending/rejected)
- **Button structure is shared across language versions** — you cannot have 2 buttons in EN and 1 in HI; the slot structure must match
- **Category affects billing** — Utility vs Marketing vs Authentication have different per-message costs on WhatsApp Business API; Meta can reclassify a template's category after approval
- **Approval takes 24–48 hours** — Glific has zero visibility into Meta's review pipeline; no progress indicator is possible or honest
- **Rejection requires a full resubmission** — you fix the content and re-submit; the original rejected version stays in the system as a record
- **One tag per template** — organisational tags are Glific-internal, do not go to Meta, used for filtering and organising the template library

### Broadcasts
Sending an HSM template to a large group of contacts at once. Requires an approved template. The PM selects a template, picks a contact collection or applies filters, and sends. Delivery is asynchronous — WhatsApp delivers per-message; Glific aggregates delivery stats.

### Staff / Users
NGO staff who log into Glific. Roles: admin, manager, staff. Staff can be assigned to conversations and contacts. The primary user doing template management is a **Programme Manager** (see Persona section).

---

## The Programme Manager persona

This is the primary user of the HSM template management flow. Understanding them is critical for every design decision.

**Who they are:**
- A non-technical NGO programme staff member — not an engineer, not a designer
- Responsible for communications with beneficiaries: writing message content, managing templates, sending broadcasts
- Often fluent in Hindi or a regional language; English is a working language but not native
- Uses Glific daily but does not know (or need to know) how the API works

**What they understand:**
- WhatsApp as a user — they know what a message looks like, what a button does
- Their programme content — the message they want to send, the variables, the language
- Basic form workflows — filling fields, clicking submit, seeing a confirmation

**What confuses them:**
- The concept of "element name" (opaque technical jargon → we rename to "Template ID" in UI)
- Why they can't edit a template after submission (the permanence is non-obvious)
- The difference between Utility and Marketing categories (billing impact is not visible to them)
- Why approval takes days and there's no progress bar
- Multi-language management when templates have 5+ language versions
- Technical rejection reasons from Meta (often written in API-speak)

**Design implications:**
- Always show feedback — what happened, what state things are in, what to do next
- Never silently fail or silently succeed — confirmations and error states are essential
- Permanent decisions (Template ID) need prominent, repeated warnings before submission
- Rejection flows must be forgiving — clear what was wrong, easy to fix and resubmit
- Multi-lingual should feel like a natural extension, not an advanced feature

---

## WhatsApp / Meta constraints (platform-level)

These are hard constraints that come from WhatsApp's Business API — not Glific product choices. They must inform every feature touching templates or messaging.

| Constraint | Implication |
|---|---|
| 24-hour session window | Free-form messages only possible within 24hrs of last contact message. Outside that, must use an approved HSM template. |
| Template review by Meta | 24–48hr wait, no programmatic status check, opaque rejection reasons |
| Template ID permanent | Once submitted, the element name cannot be changed. UI must make this impossible to miss before first submission. |
| Per-language submission | Each language is an independent Meta review. A template can be approved in EN and rejected in HI simultaneously. |
| Category classification | Meta classifies each template as Utility, Marketing, or Authentication. Meta can reclassify after approval. Changes affect billing. |
| Button slot structure fixed | Button count and type (URL, phone, QR) are part of the template definition. All language versions must share the same structure. |
| Variable placeholders | Body text uses `{{1}}`, `{{2}}` etc. for dynamic values. Meta requires sample values at submission. |
| Media constraints | Image: 5MB max, Document: 16MB max, Video: 16MB max. Audio and stickers not supported. |

---

## Notifications and status model

- **Approved** — template is live; can be used in flows and broadcasts
- **Pending** — submitted to Meta, awaiting review (24–48hrs)
- **Rejected** — Meta declined; PM must fix content and resubmit
- **Meta reclassification** — Meta changed the category; surfaced via in-app notification and inline badge on list; affects billing

Glific surfaces Meta-originated events (approvals, rejections, reclassifications) via its Notifications section. The HSM list page shows per-language status via coloured dots on language chips (green = approved, amber = pending, red = rejected).
