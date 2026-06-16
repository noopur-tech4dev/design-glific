# Glific Design Patterns

A living library of product design patterns — distilled from feature-level decisions and generalised across the product. Each pattern was first observed on a specific feature but applies wherever the same problem recurs.

**Governance:** This file is updated by Noopur only, on approval. Claude proposes pattern additions at the end of sessions — they are not written here without explicit approval.  
**Last updated:** 16 June 2026  
**Source features:** HSM Templates redesign (D-01 through D-56)

---

## How to use this

When starting work on any new Glific feature, read this file before touching any prototype. Each pattern here was learned through iteration — the "alternative rejected" details show what we already know doesn't work, so you don't have to rediscover it.

---

## 1. Transparency and async processes

### P-01 · Never fake progress for external processes
**Pattern:** When Glific depends on an external system (Meta review, WhatsApp delivery, third-party API), never show a progress bar or real-time status indicator. Instead: tell the user what we know, what we don't, and what to expect. Use a clear static state (e.g., "Pending review — usually 24–48 hours, we'll notify you").  
**Why:** Fake progress creates false expectations. When it doesn't resolve in the implied timeframe, PMs assume something is broken and take corrective actions that make things worse.  
**Applies to:** Template review, broadcast delivery, contact imports, webhook callbacks, flow triggers on external events.  
**First observed:** D-06

### P-02 · Use the existing Notifications channel for async external events
**Pattern:** Events that originate from outside Glific (approvals, rejections, reclassifications from Meta; delivery reports from WhatsApp) surface in the existing Notifications section. Do not create a parallel in-product notification system per feature.  
**Why:** Parallel notification systems split the PM's attention and create inconsistency in how they learn about background events. One notification centre means one place to check.  
**Exception:** Inline indicators (e.g., amber badge on a list row) are acceptable as a secondary, persistent visual signal — they complement notifications, they don't replace them.  
**First observed:** D-07

### P-03 · Show specific names, not abstract counts, when action is required
**Pattern:** When a change (rejection, reclassification, status update) affects other records, show the names of those records — not a count. "Onboarding flow, Workshop reminder" not "2 flows affected."  
**Why:** A count requires the PM to go find which records are affected before acting. Names let them act immediately.  
**Applies to:** Rejected templates that break flows, deleted contacts in a collection, disabled integrations affecting multiple features.  
**First observed:** D-08

---

## 2. Irreversible actions and permanent state

### P-04 · Permanent decisions need visible friction before the point of no return
**Pattern:** Any field or action that cannot be undone after submission must be flagged prominently before the user crosses the line — not after. Use an amber info callout, not just a tooltip or placeholder text. After submission, show a locked state (read-only field with a lock icon and explanation).  
**Why:** NGO PMs assume everything can be edited. The absence of an explicit warning is read as implicit permission to change later. By the time the error surfaces, it may be too late.  
**Applies to:** Template IDs after submission to Meta, contact/flow IDs after creation, broadcast sends, any field that maps to an external system identifier.  
**First observed:** D-03

### P-05 · Destructive actions are never in row-level overflow menus
**Pattern:** Delete, archive, and other destructive actions must not appear in the 3-dot overflow menu on a list row. They require a deliberate flow — typically accessed from the record's detail or view page, with a confirmation step.  
**Why:** Row overflow menus are scanned quickly. A delete option in a menu alongside safe actions (copy, duplicate, send) is too easy to trigger accidentally. The cost of an accidental delete is high; the friction of a deliberate flow is low.  
**What belongs in row overflow menus:** Low-frequency, non-destructive utility actions (copy ID, duplicate, send broadcast).  
**First observed:** D-32

### P-06 · Structural fields shared across versions are locked after initial creation
**Pattern:** When a field applies to all versions of a record (e.g., a tag that applies to all language versions of a template, or button structure shared across language submissions), it must be locked for editing on the per-version form. Show an explanation: "This is shared across all versions and cannot be changed here."  
**Why:** Changing a shared field on a single version creates a silent inconsistency. The PM doesn't know they've affected other versions. Locking the field makes the shared nature visible and prevents accidental cross-version changes.  
**First observed:** D-17, D-42

---

## 3. Forms and input fields

### P-07 · Show constraints at the point of input, not only at rejection
**Pattern:** Constraints (file size limits, character limits, format rules, allowed values) must be visible at the field level before the user submits. Do not rely solely on post-submission error messages to communicate rules.  
**Why:** PMs build content, submit, get rejected, and have to rebuild. Surfacing the constraint upfront removes an entire wasted submission cycle. For external submissions (Meta review), that's 24–48 hours of delay per missed constraint.  
**Applies to:** Media upload fields (size limits), body text fields (character counts, formatting syntax), Template ID (format rules), variable placeholders (count limits).  
**First observed:** D-12

### P-08 · Never silently modify user input — show error states and leave input intact
**Pattern:** When a user types something invalid, show an immediate inline error state (red border, error message below the field, hint text hidden). Do not strip, autocorrect, or silently transform the invalid input. Leave exactly what they typed in the field.  
**Why:** Silent stripping feels like the keyboard is broken. PMs reported thinking their input wasn't being registered. Leaving the input intact + explaining the rule respects user agency and helps them understand what to fix.  
**Applies to:** Template ID format validation, variable placeholder syntax, phone number formats, any field with character restrictions.  
**First observed:** D-46

### P-09 · Keep field-level tools inside or adjacent to the field they affect
**Pattern:** Actions and tools that operate on a specific field (Add Variable, character counter, formatting buttons, AI Assist) belong in a toolbar docked to that field — not in the section header, not below a different field, not in a side panel.  
**Why:** When a tool is visually disconnected from its field, PMs are unsure what it acts on. Proximity makes the relationship obvious without documentation.  
**Implementation:** `.textarea-toolbar` bar docked below the textarea, always visible. Left: formatting. Centre: field-specific actions. Right: counter.  
**First observed:** D-11, D-20

### P-10 · Optional fields that improve outcomes should be visible, not hidden
**Pattern:** Optional fields that meaningfully improve the quality of the output (e.g., footer text, buttons, media attachment on a WhatsApp template) should be always visible with an "(Optional)" label. Do not put them behind a toggle, accordion, or "Advanced" section.  
**Why:** Hidden optional fields are never used. For NGO PMs, a lower-quality template means lower Meta approval rates and a worse experience for beneficiaries. The cost of showing an unused optional field is low; the cost of hiding a useful one is high.  
**Exception:** Genuinely technical options (e.g., Static vs Dynamic URL type) can live behind a collapsible "Advanced" disclosure within the relevant row — they should not be top-level required decisions.  
**First observed:** D-04

### P-11 · Present choices at the moment of relevance, not as upfront forks
**Pattern:** When a form action has multiple subtypes (e.g., URL button vs Phone button), don't require the user to choose the type before seeing any fields. Create the item first, then show the type toggle inline on the created row.  
**Why:** Upfront forks make the user decide in the abstract, before they've done anything. Inline type toggles let them decide in context — after they've already started the task.  
**Applies to:** Button type selection, media type selection, language selection before form fill.  
**Also applies to AI:** Don't fork users into manual vs auto-generate paths upfront. Let both modes coexist on the same form surface (see P-19).  
**First observed:** D-21

### P-12 · Rename technical identifiers to user-friendly labels in the UI
**Pattern:** When an API field name is technical jargon that non-technical users won't understand, rename it in the UI. Show the technical name only where necessary (e.g., as a sub-label for developers, or in a tooltip). The actual value sent to the API remains unchanged.  
**Why:** PMs use the UI label as their mental model. A confusing label creates confusion about the field's purpose and importance. A clear label communicates the field's meaning and consequences without requiring external documentation.  
**Examples:** "element_name" → "Template ID"; "wa_id" → "WhatsApp number"; "flow_uuid" → "Flow ID".  
**First observed:** D-02

---

## 4. Table and list patterns

### P-13 · Aggregate status columns that mask per-item variation are harmful
**Pattern:** When a record has multiple sub-items with independent statuses (e.g., language versions of a template, each with their own approval state), a single aggregate "Status" column is misleading. Replace it with per-item status indicators co-located with the items they describe.  
**Why:** An aggregate "Approved" status on a template with one rejected language is actively wrong — it hides the problem the PM needs to act on. Per-item status indicators (coloured dots on language chips, inline summary line) surface the real state.  
**First observed:** D-28

### P-14 · Secondary and utility actions belong in overflow menus — primary actions stay visible
**Pattern:** High-frequency, task-critical actions (Add language, View, Edit) belong as visible icon buttons on the row. Low-frequency, utility actions (Copy ID, Duplicate, Send broadcast) belong in the 3-dot overflow menu. The threshold question: "Would a PM need this action during a normal working session?" If yes → visible. If rarely → overflow.  
**Why:** Every visible button competes for attention. Overloading the actions column trains PMs to ignore it. The overflow menu is a reliable home for actions that are occasionally needed but shouldn't crowd the primary affordances.  
**First observed:** D-54

### P-15 · Handle scale gracefully — design for the common case without breaking the edge case
**Pattern:** Before designing any list or multi-item component, check the real data distribution. Design the primary experience for the most common case (e.g., 1–4 items). For edge cases (10+ items), add a progressive treatment (overflow badge, compact grid, pagination) that activates only when needed. The common case should see zero change.  
**Why:** Designing for the edge case first produces UIs that are cluttered for the 86% of users in the common case. Progressive enhancement keeps the UI clean for most users while remaining functional for outliers.  
**First observed:** D-22

### P-16 · Add actions sit at a fixed position below a list — never embedded as list items
**Pattern:** The "Add new item" action for any list should always be positioned below the list at a consistent location — never as a cell or row inside the list that shifts position as items are added or removed.  
**Why:** When the add button is embedded in the list, its position changes every time an item is added. PMs have to scan the list to find it. A fixed position below the list is unambiguous and follows the established data-above / action-below pattern.  
**First observed:** D-23

### P-17 · Show count indicators only when the count adds information
**Pattern:** Count badges (e.g., "×3") should appear only when the count is greater than the expected default. For lists where 1 is the normal case, suppress "×1" — show the plain label. Counts of 2+ are meaningful; counts of 1 are noise.  
**Why:** When every row shows "×1", the count becomes wallpaper — ignored. When it appears only for 2+, it carries a signal: "this one is different, pay attention."  
**First observed:** D-51

### P-18 · When selecting a record, show a preview before confirming
**Pattern:** When a PM is choosing a record to use (selecting a template, a contact collection, a flow), show a preview panel alongside the list before the selection is confirmed. The primary CTA ("Use this template") should be disabled until a selection is made.  
**Why:** PMs who work with large libraries cannot remember what `workshop_reminder` or `onboarding_v2` contains. A preview panel lets them verify content before locking in a choice that may be hard to undo.  
**Implementation:** Two-panel modal — searchable list on the left, preview on the right. Preview matches the actual rendered output (WhatsApp bubble, flow diagram, etc.).  
**First observed:** D-18

---

## 5. Navigation and page structure

### P-19 · Complex forms that represent formal submissions are full pages
**Pattern:** Forms that represent a formal, external submission (to Meta, to a third party, to an API that has consequences) should be dedicated full pages — not drawers, modals, or inline expansions. The page title should name the action explicitly.  
**Why:** Drawers and modals are the right container for quick edits and confirmations. A form that requires filling 8+ fields, uploading media, and submitting to an external system is a task, not an aside. A full page gives it appropriate weight and makes it bookmarkable.  
**First observed:** D-13

### P-20 · Always provide explicit back navigation — never rely on browser back
**Pattern:** Every create/edit/detail page must have an explicit "← Back to [list]" text button above the page heading. It should not be part of the form card.  
**Why:** Browser back is unpredictable when navigating from different entry points (direct URL, notification link, list row). An explicit back button always routes to the expected destination.  
**Implementation:** Ghost button (no border/background), 13px/600 weight, gray `#6b7280`, chevron icon, outside the form card.  
**First observed:** D-14

### P-21 · Separate context (what am I working on) from task (what am I doing)
**Pattern:** When a task requires constant reference to surrounding context (e.g., adding a language version requires seeing the existing language list), split the page into two distinct zones: a stable context zone (stays visible, read-only) and a task zone (form, editable, replaces/appears below context).  
**Why:** A single-card layout that mixes list context and form fields makes both feel unstable — the list changes when the form opens, the form is hard to locate when scrolling. Two cards give each zone a stable visual home.  
**First observed:** D-25

### P-22 · Separate inspect from act — view and edit are distinct affordances
**Pattern:** Clicking on a record or row should open a locked read-only view (inspect). A separate, explicitly labelled CTA (e.g., "Edit & Re-apply", "Edit template") opens the editable form (act). Do not auto-open an edit form on a record click.  
**Why:** PMs often click to understand what they're looking at before deciding whether to change it. Auto-opening an edit form on click conflates browsing with editing, risks accidental changes, and removes the user's ability to just look.  
**Applies to:** List row clicks, language row clicks, notification card CTAs, any record that has both a view state and an edit state.  
**First observed:** D-15, D-41

---

## 6. Status and error states

### P-23 · Status tabs separate action buckets — don't mix statuses in a flat list
**Pattern:** When records have distinct status states that require different actions (Approved → view only; Pending → wait; Rejected → fix and resubmit), use tabs to separate them rather than a single list with a status column. Auto-select the tab with the most urgent state (rejected > pending > approved) on load.  
**Why:** A flat list with a status column requires the PM to mentally filter the rows by status before they can act. Tabs pre-filter for them and surface the actionable bucket first.  
**First observed:** D-24

### P-24 · CTAs must match the current mode — never show Submit when there's nothing to submit
**Pattern:** The primary CTA on a form should reflect what the form is currently doing: "Submit for Approval" in create/add mode, "Reapply for Approval" in fix mode, "Delete this language" in view mode, hidden entirely in read-only mode. Do not show a generic Submit button in every state.  
**Why:** A CTA that doesn't match the context creates confusion about what clicking it will do. PMs in view mode who see "Submit for Approval" wonder if they're about to re-submit something they didn't mean to change.  
**First observed:** D-33

### P-25 · Changes with billing or cost implications must be impossible to miss inline
**Pattern:** When an external system makes a change that affects billing (e.g., Meta reclassifies a template's category), surface this as an inline indicator on the affected record in the list — not only in notifications. The indicator should be persistent (visible on every visit) until acknowledged.  
**Why:** A PM who misses a billing-relevant change may budget incorrectly for weeks. Inline persistence on the list row makes the change visible during normal workflow — not only when they happen to check Notifications.  
**First observed:** D-19, D-35

---

## 7. AI assistance

### P-26 · AI-assisted actions use consistent visual language
**Pattern:** All buttons that trigger AI assistance (AI Assist, AI Translate, Suggest content) use the same visual style: white background, green border (`#b9e4cc`), green text (`#119656`), 6px border-radius. They are never styled as plain text links, primary buttons, or disabled-looking controls.  
**Why:** Visual consistency across AI actions signals to PMs that they are in the same category — "this will help me write content, not submit a form." Inconsistent styling makes AI features look like separate, unrelated capabilities.  
**First observed:** D-43

### P-27 · Show AI tools upfront — don't gate on a prior selection or action
**Pattern:** AI Assist, AI Translate, and similar capability buttons should be visible as soon as the relevant form opens — not only after a specific field is filled, a language is selected, or another action completes.  
**Why:** Gating AI buttons on a prior step means PMs who would benefit from AI help don't discover it until they've already started writing manually. Upfront visibility sets the right expectation from the beginning of the task.  
**First observed:** D-29

### P-28 · Don't fork manual vs AI paths upfront — let them coexist
**Pattern:** When a task can be done manually or with AI assistance (e.g., translating content), don't make the user choose a method before starting. Present the form immediately; put the AI action as a toolbar button. The user decides after seeing the form, not before.  
**Why:** An upfront method picker adds a decision before any content exists. It also implies the two methods are mutually exclusive — a PM might AI-translate, then edit manually. Coexistence is the correct model.  
**First observed:** D-26

---

## 8. Technical and implementation patterns

### P-29 · Use CSS data-tip tooltips — never native title attributes
**Pattern:** All tooltips on interactive elements (icon buttons, badges, truncated text) should use CSS `data-tip` pseudo-element tooltips, not the native HTML `title` attribute.  
**Why:** Native `title` tooltips have a ~1 second browser delay and cannot be styled. PMs reported icon buttons having no tooltip at all (the delay made them think the feature was broken). CSS tooltips appear immediately on hover and are visually consistent.  
**Implementation:** `[data-tip]::after` (dark pill, Heebo font, `opacity` transition) + `[data-tip]::before` (arrow). Both hidden by default, shown on `:hover` via `opacity:1`.  
**First observed:** D-55

### P-30 · Absolutely-positioned dropdowns require overflow:visible on parent cards
**Pattern:** Any `.form-card` or container that holds an absolutely-positioned dropdown (tag picker, select menu, date picker) must have `overflow:visible`, not `overflow:hidden`. The scrollable list inside the dropdown should have its own `max-height` + `overflow-y:auto`.  
**Why:** `overflow:hidden` clips absolutely-positioned children at the card boundary — the dropdown appears to vanish or get cut off. This is a silent failure: no console error, no obvious cause.  
**First observed:** D-47

### P-31 · Reference content belongs immediately adjacent to the input it informs
**Pattern:** When showing source/reference content to help a PM complete a field (e.g., English template content shown while translating to Hindi), inject the reference strip directly above the input field — not at the section header.  
**Why:** If reference content is at the top of the section, the PM must scroll up to read it, then scroll down to type. Adjacent placement eliminates scroll and working memory load.  
**First observed:** D-27

---

## Pattern proposal log

Proposals raised by Claude during sessions, pending Noopur's approval:

*(empty — first proposals to be raised at end of next feature design session)*

---

## Patterns superseded or narrowed

- **D-34 superseded by D-51 / P-17:** Always showing category counts (even ×1) was replaced by "suppress ×1, show ×N only when N > 1." The broader pattern (P-17) is the current guidance.
