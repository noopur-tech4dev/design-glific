# Glific HSM Templates — Design Decisions

A living record of decisions made during the HSM Templates redesign. Updated as decisions are taken.

---

## D-01 · Title field removed
**Date:** May 2026  
**Decision:** Remove the "Title" field from the create/edit form.  
**Rationale:** Title was a Glific-internal label with no function — it doesn't get submitted to Meta, doesn't appear in WhatsApp, and creates confusion because it implied you could rename a template. The element name is the only identifier that matters.  
**Alternative rejected:** Keeping Title as an optional internal label. Rejected because it adds cognitive load without value, and PMs would fill it with the same thing as the element name anyway.

---

## D-02 · Element name renamed to "Template ID" in UI
**Date:** May 2026  
**Decision:** Display the element name field as "Template ID" in the UI, with element name shown as the technical identifier below.  
**Rationale:** "Element name" is opaque to non-technical users (Programme Managers). "Template ID" is more intuitive. The permanence and format rules are shown inline on the field.  
**Note:** The actual value sent to Meta/WhatsApp is still the element name — this is only a UI label change.

---

## D-03 · Template ID (element name) is permanent — shown as a lock
**Date:** May 2026  
**Decision:** Show a locked-state callout on the Template ID field after submission. Before submission, show a prominent inline warning: "Cannot be changed after submission."  
**Rationale:** NGOs have caused confusion by assuming they can rename templates. The redesign makes permanence impossible to miss.  
**Implementation:** Amber info callout below the field, not an error state. Error state only triggers if the format is wrong or the name is already taken.

---

## D-04 · All optional fields visible — not hidden or collapsed
**Date:** May 2026  
**Decision:** All optional fields (footer, buttons, attachment, tags) are always visible. They are differentiated by an "(Optional)" label in gray, not hidden behind a toggle or collapse.  
**Rationale:** Optional fields like footer and buttons meaningfully improve template quality. Hiding them means most PMs never use them, resulting in lower approval rates and worse user experience for recipients.  
**Alternative rejected:** Approach C (accordion/collapse for optional fields). Rejected explicitly — user said "optional fields add to a good experience for designing an HSM. We need to keep them visible."

---

## D-05 · Multi-lingual is first-class, not a switch
**Date:** May 2026  
**Decision:** Language is the second field in the form (after Template ID). Language versions are shown as chips on the listing page and detail page. Adding a language version is a top-level action, not buried in a settings panel.  
**Rationale:** Multi-lingual templates are a major platform gap. Treating language as an afterthought (the old "Multi-lingual message switch" toggle) means most NGOs never create multi-language templates.  
**Implementation:** Language chips (EN ✓, HI ✓, MR ⏳) on listing rows. "Add language" is a primary CTA on the detail page.

---

## D-06 · Approval is Meta-controlled — no fake progress indicators
**Date:** May 2026  
**Decision:** Never show a progress bar or real-time status during Meta review. Show a clear "Pending review" state with an explanation that this is an external process (24–48 hrs) and Glific will notify when complete.  
**Rationale:** Glific has no visibility into Meta's review pipeline. Fake progress would create false expectations. Transparency = tell the user exactly what we know and don't know.  
**Implementation:** Amber banner on the pending template card: "Submitted to Meta for review. We'll notify you when this changes — usually 24–48 hours."

---

## D-07 · Notifications are the channel for Meta-originated events
**Date:** May 2026  
**Decision:** Category reclassifications, approval status changes, and rejections that originate from Meta (not triggered by PM action) surface in the Notifications section — not as inline alerts on the listing page.  
**Rationale:** The Notifications section already exists in Glific's nav. Creating a parallel notification system in HSM would be confusing.  
**Implementation:** Rich notification cards (red/amber/green) with Was/Now comparison for category changes. Listing page shows coloured border on affected cards as a secondary indicator.

---

## D-08 · Unapproved = broken flows — shown by name, not count
**Date:** May 2026  
**Decision:** When a template is rejected, the detail view and notification card list the specific flow names that are broken — not just a count ("2 flows affected").  
**Rationale:** "2 flows affected" requires the PM to go find which flows are broken. Showing the names (Onboarding flow, Workshop reminder) lets them act immediately.

---

## D-09 · "Use in flow" is not part of the creation form
**Date:** May 2026  
**Decision:** The template creation form does not include a step for assigning the template to a flow. Flow assignment happens post-approval in the Flow Builder.  
**Rationale:** Templates must be approved before they can be used. Adding flow assignment to the creation form implies the template is immediately usable, which is wrong. The flow builder is the right context for template assignment.  
**Implementation:** After approval, the notification card surfaces a "Use in flow" CTA as the primary action, routing the PM to Flow Builder.

---

## D-10 · Files are independent — no cross-linking between design docs
**Date:** May 2026  
**Decision:** `glific-design-system.html`, `hsm-design-brief.html`, and `hsm-flows.html` are standalone files. They do not link to each other.  
**Rationale:** The design system is product-agnostic. Linking it to feature-specific files would mean every feature's brief gets referenced from the system doc, which is unsustainable. Each file stands alone.

---

## D-11 · "Add Variable" button lives inside the message body field, not in the section header
**Date:** 27 May 2026  
**Decision:** Move the "Add Variable" button from the section header into a fixed toolbar bar docked at the bottom of the message body textarea. The toolbar also carries a hint: "Use {{1}}, {{2}}… for dynamic values."  
**Rationale:** When the button was in the header, it was visually disconnected from the textarea it acted on. PMs were unsure what "Add Variable" would do. Placing it inside the field makes the relationship obvious, and the hint text removes the need for separate documentation.  
**Implementation:** `.textarea-toolbar` bar is a flex row pinned below the textarea, always visible regardless of scroll position in the field.

---

## D-12 · Media size limits shown directly on upload cards
**Date:** 27 May 2026  
**Decision:** Each media type card (Image, Document, Video) shows the maximum allowed file size as a sub-label beneath the type name (Image: 5 MB, Document: 16 MB, Video: 16 MB). A dynamic hint under the URL field reflects the selected type's limit.  
**Rationale:** Meta enforces strict size limits that differ by media type. PMs were uploading oversized files and only learning the limit after rejection. Surfacing the spec upfront prevents wasted submission cycles.  
**Note:** Limits are standard Meta/WhatsApp Business API values and will need to be kept in sync if Meta updates them.

---

## D-13 · "Add Language" is a dedicated page, not an in-page banner or modal
**Date:** 27 May 2026  
**Decision:** Adding a new language to an existing template opens a dedicated page (`add-hsm-language.html`) rather than a slide-in panel or banner atop the create form.  
**Rationale:** The add-language flow requires the same form complexity as creating a new template (body, footer, buttons, media — all specific to the new language). A panel or modal would be too cramped and would give the wrong mental model (it's a submission to Meta, not a quick edit).  
**Alternative rejected:** An "exit message banner" overlaid on the create page that let the PM cancel mid-add. Removed because it created confusion about which form was the "real" one and added an unnecessary dismissal step.  
**Implementation:** `add-hsm-language.html` is built from the same source as `hsm-create-hifi.html`, pre-loaded in existing-template state. Template ID is shown read-only as the page heading. Submit CTA reads "Submit for Approval."

---

## D-14 · Back navigation is always present on create and add-language pages
**Date:** 27 May 2026  
**Decision:** A "← Back to templates" text button sits above the page `<h1>` on both `hsm-create-hifi.html` and `add-hsm-language.html`. It routes to `hsm-list.html`.  
**Rationale:** PMs regularly land on these pages from the list and need a clear escape hatch without relying on the browser back button (which may have unpredictable history state).  
**Implementation:** Ghost button (no border, no background), 13px/600 weight, gray `#6b7280`, chevron icon. Sits outside the form card so it doesn't compete with form labels.

---

## D-15 · Eye icon on list routes to read-only language view, not edit
**Date:** 27 May 2026  
**Decision:** The eye icon in the HSM list actions column routes to `add-hsm-language.html?template={id}&action=view`. In view mode: the page heading shows only the template ID, the "Add language" chip is hidden, and the submit row is hidden. All fields are read-only.  
**Rationale:** PMs need to inspect a submitted template's content and languages without accidentally triggering an edit or re-submission. The eye icon's affordance (view, not edit) sets the right expectation.  
**Alternative rejected:** A modal preview on the list page. Rejected because the full form context (category, buttons, footer, all language versions) can't fit cleanly in a modal.

---

## D-16 · Organisational tag uses a custom dropdown — no pill after selection, supports create-new
**Date:** 28 May 2026  
**Decision:** The tag field is a custom-built dropdown (not a native `<select>`). It filters options as the PM types, shows existing tags with green dots, and offers a "Create tag '…'" row when the input doesn't match any existing tag. After selection the chosen tag stays in the input field as plain text — no coloured pill is added below the field.  
**Rationale:** Native `<select>` can't support create-new. Pill tags under the field implied multiple tags were allowed (they aren't — one tag per template). Keeping the value in the input itself is simpler and avoids the "how do I remove this?" confusion that pills create.  
**Help text:** "Pick a suggestion or type your own — tags help you filter and organise templates later."

---

## D-17 · Organisational tag is locked when the template already has one
**Date:** 28 May 2026  
**Decision:** When a PM opens an existing template (either to view it or add a language), and that template already has an organisational tag, the tag field becomes read-only and a lock note appears: "Tag is shared across all language versions and cannot be changed here."  
**Rationale:** A tag applies to the entire template, not to a single language version. Allowing a PM to change the tag on the add-language page would silently change the tag for all existing approved languages — a high-impact side effect that's not obvious from context.  
**Implementation:** `lockTag(val)` / `unlockTag()` functions called from `loadExistingTemplate()`. The tag field gets `pointer-events:none`, `background:#f9fafb`, `color:#6b7280`. The lock note uses a lock SVG icon for instant recognition.

---

## D-18 · Existing template selection opens a two-panel picker modal with WhatsApp preview
**Date:** 28 May 2026  
**Decision:** When the PM clicks "Use existing template" on the create page, a modal opens with two panels: a searchable list of templates on the left, and a WhatsApp-style message preview on the right. The preview shows language tabs, the message bubble (body + footer + buttons), and the category. "Use this template" is disabled until a template is selected.  
**Rationale:** The previous design used an inline dropdown to select the template ID. This gave no preview of the template content — PMs had to memorise or guess what `workshop_reminder` contained. The modal lets them verify content before locking in the choice.  
**Alternative rejected:** Inline dropdown with a separate preview panel below it. Rejected because the preview needed enough space to be useful, and a modal is a cleaner context boundary (the PM is making a deliberate choice).  
**Implementation:** `.tpl-picker-overlay` (full-screen dimmed backdrop) → `.tpl-picker-modal` (640px wide, 480px tall). Left panel: search input + scrollable item list. Right panel: lang tabs + `.tpl-wa-bubble` styled to match WhatsApp message appearance (`#e4f7b0` background, Heebo font, rounded corners).

---

## D-19 · Meta category reclassification shown inline on the list row
**Date:** 29 May 2026  
**Decision:** When Meta has reclassified a template's category (e.g., Marketing → Utility), the list row shows the old category with a strikethrough, an arrow (→), and an amber warning badge reading "Utility by Meta." The badge includes a tooltip with the full explanation.  
**Rationale:** Category changes affect how billing is calculated on WhatsApp Business API. A PM who doesn't notice the change may budget incorrectly or be surprised by a cost difference. The inline indicator makes the change impossible to miss on the next visit to the list, without requiring a full notification panel read.  
**Colour choice:** Amber (#fffbeb border + #92400e text) signals a non-critical but attention-requiring state — distinct from the red used for rejections and the green used for approvals.  
**Implementation:** `metaCategory` field on the template data object. `renderTable()` compares `t.category` vs `t.metaCategory`; if different, renders the mismatch treatment instead of the standard badge.

---

## D-20 · Message body toolbar — formatting, variable, and counter unified in one bar
**Date:** 4 June 2026  
**Decision:** The message body textarea has a single unified toolbar docked at the bottom of the field. Left side: Bold (B), Italic (I), Strikethrough (S̶) formatting buttons. Centre: Add Variable button. Right side: live character counter (0 / 1024). The old standalone counter below the field is removed.  
**Rationale:** Formatting buttons, the Add Variable action, and the character counter are all directly related to composing the message body. Scattering them across different zones (header, below the field) made the form feel disconnected. A single bar keeps all body-editing tools in one predictable place.  
**WhatsApp formatting syntax:** Bold = `*text*`, Italic = `_text_`, Strikethrough = `~text~`. Clicking a format button wraps selected text; with no selection, inserts markers and places cursor between them. Clicking again on already-wrapped text unwraps.  
**Counter thresholds:** Neutral (< 900 chars) → amber warning (900–1023) → red over-limit (1024+).

---

## D-21 · Call to Action buttons — single "Add Button" with inline type toggle
**Date:** 4 June 2026  
**Decision:** Replace the old two-section CTA layout (separate "URL Buttons" sub-section + "Phone Button" sub-section, each with its own add button) with a single **[+ Add Button]** that creates a row. Each row starts with an inline type chip toggle: **[ 🔗 URL ] [ 📞 Phone number ]**. Selecting a type reveals the correct fields for that type. Static/Dynamic URL toggle moves into a collapsible "Advanced" disclosure within the URL row.  
**Rationale:** The old layout required PMs to understand the URL/Phone distinction before they had done anything, presented Static/Dynamic radio buttons as a required upfront choice (a backend concept most NGO PMs don't need to think about), and created two disconnected add buttons for what is conceptually one action — "add a button to my template."  
**Limits enforced silently:** URL chip in a new row is greyed out once 2 URL buttons exist; Phone chip is greyed out once a phone button exists. The [+ Add Button] itself disappears when both limits are hit.  
**Alternative rejected:** Keeping the two sub-sections but replacing the radio buttons with a segmented control. Rejected — it still forces the PM to decide URL vs Phone before seeing any fields, and maintains the cognitive split between two zones.

---

## D-22 · Language chip overflow — progressive treatment based on count
**Date:** 5 June 2026  
**Data context:** Of ~14k templates in production — 12k have 1 language (86%), ~1.5k have 2–4 languages across 5 orgs, ~100 have 5–7 languages, and fewer than 50 have 10+ languages. Design must work well for the 86% case and remain usable for the rare 10+ case without complicating the common path.  
**Decision (list table):** Show language chips up to a count of 4. Beyond that, collapse remaining chips into a "+N more" overflow badge on the same line. Clicking the badge expands to show all chips. Table rows stay at a consistent height regardless of language count.  
**Decision (create/add-language form):** When a template has 5 or fewer language chips, use the current pill row (no change). When it has more than 5, switch to: (1) a 4-column compact grid of language badges, each showing the language code and a status dot; (2) a standalone **[+ Add language]** button below the grid as a separate element.  
**Alternative rejected — horizontal scroll:** Hidden chips off-screen with no affordance that more exist. Unexpected on desktop and does not solve the discoverability problem.  
**Alternative rejected — Add button as a grid cell:** The button position shifts every time a language is added, making it hard to find. It also blends in visually with the language cells.  
**Threshold:** The grid treatment activates only above 5 chips. Templates with 1–4 languages (the overwhelming majority) see zero change.

---

## D-23 · Add Language button — fixed position below the language grid
**Date:** 5 June 2026  
**Decision:** In the 5+ language grid view, the "Add language" action is always a standalone button below the grid — never embedded as a cell inside the grid, never inside a combined status bar.  
**Rationale:** When the add button is a grid cell, its position changes every time a language is added or removed. PMs have to scan the whole grid to find it. A fixed position below the grid is the standard list + add-item pattern: data above, action below. It is unambiguous regardless of how many languages exist.  
**Supporting option considered:** A green summary-action bar combining "9 approved · 2 pending · 1 rejected" with the add button in one row. Decided against it because the green action button competes with the green summary bar visually. Status counts can be shown as a small text line above the grid without conflating them with the action.

---

## D-24 · Language pill row → tabbed list on add-language page
**Date:** 9 June 2026  
**Decision:** Replace the flat chip row on the Add Language page with a tabbed list inside a bordered container. Three tabs — Approved, In Progress, Rejected — each showing only languages in that bucket. The Rejected tab is auto-selected on load if any rejections exist.  
**Rationale:** Chip overflow was the trigger — a template with 5+ languages in one status would create an unmanageable pill row. The tabbed list keeps each bucket scannable, surfaces rejection reasons inline (truncated to 90 chars), and gives quick Fix / Delete action links per row without modal overhead. The bordered container groups the entire language-management surface visually.  
**Alternative rejected — single flat table with status column:** All languages mixed together, status conveyed only by a badge. Harder to triage rejections fast when you have to scan rows of different statuses.  
**Alternative rejected — status-grouped sections (no tabs):** All groups visible at once, takes too much vertical space. Tabs keep the section compact while letting each bucket have full focus.

---

## D-26 · Add language: reference strip + inline AI Translate (no manual/auto fork)
**Date:** 9 June 2026  
**Decision:** Remove the two-path method picker ("Manual" vs "Auto-generate") from the add-language flow. Replace with a single path: the content form opens immediately, with (a) a collapsible green reference strip showing the English (or earliest approved) source content above the body field, and (b) an "AI Translate" toolbar button on the body field that fills body + footer in ~1.4 seconds.  
**Rationale:** The fork created unnecessary friction. Manual translators needed to copy source text from somewhere — the reference strip solves this without a separate step. Users who want AI assistance can hit one button instead of choosing a method upfront. Both modes now coexist on the same surface; the user decides after seeing the form, not before.  
**Alternative rejected — keep the two-card method picker:** Requires an extra decision and an extra click before seeing any content. The picker also implied the two modes were mutually exclusive, which isn't true (a PM might AI-translate, then edit manually).

---

## D-27 · Button reference shown inline above button input rows
**Date:** 9 June 2026  
**Decision:** When in add-language mode on a template that has buttons, show the approved-language button labels as a compact chip strip injected directly above the QR/CTA input list (not at the top of the Buttons section).  
**Rationale:** If reference chips are at the top of the section header, the user has to scroll up to cross-reference while typing in the lower rows. Injecting them immediately above the input rows means the source text and the target field are adjacent — no scrolling or memory load.  
**Implementation note:** The strip is created dynamically via DOM injection (`insertBefore` the qr-btns-list or url-btns-list) and removed when leaving add mode. No static HTML needed.

---

## D-28 · HSM list — remove Status column, embed status summary in Languages column
**Date:** 9 June 2026  
**Decision:** Remove the dedicated "Status" column from the HSM template list. Instead, show a compact status summary line ("2 rejected · 1 pending") below the language chips inside the Languages column. For templates with more than 4 languages, show 4 chips + a "+N more" expand button.  
**Rationale:** The Status column showed an aggregate (e.g., "Approved") even for templates with rejected language versions — a misleading simplification. The real actionable signal is per-language status. Embedding the summary in the Languages column co-locates the information with the chips that give it meaning. Removing the column also reclaims horizontal space (from 160px to 220px for the Languages column) to show more chips before overflow.  
**Alternative rejected — keep Status column but make it multi-value:** Clutters the cell and duplicates information already visible from the chip colours. A single text summary below the chips is less noisy.

---

## D-25 · Two-card layout on add-language page
**Date:** 9 June 2026  
**Decision:** Split the add-language page into two distinct cards. Card 1 (always visible) shows Template Identity + the tabbed language list. Card 2 (hidden by default, appears below Card 1 when a language row is clicked or Add is triggered) shows the full content form for that language.  
**Rationale:** The single-card layout caused confusion: the language picker at the top and the form fields below felt unrelated because they serve different conceptual purposes — Card 1 is "which template, which languages exist?" (context) and Card 2 is "what does this language version say?" (task). Splitting them makes Card 1 a stable reference that stays visible while working in Card 2.  
**Mode states for Card 2:**
- `mode-view` (grey top border) — viewing an approved or pending language; all fields read-only
- `mode-fix` (red top border + red banner) — fixing a rejected language; fields editable, rejection reason shown in a prominent strip
- `mode-add` (green top border + green banner) — adding a new language; method picker shown first, then fields

**Alternative rejected — inline expansion within Card 1:** Content form expands in-place below the selected row. Causes vertical jump in the card and mixes list context with edit context on the same surface.

---

## Resolved decisions

- ✅ **Should the form live on a separate page or in a right drawer/modal?** → Separate full page (see D-13).
- ✅ **How should button types be differentiated in the form?** → CTA buttons use a unified add-button with inline URL/Phone type toggle per row (D-21). Quick-reply buttons have a 20-character counter per label.

## Pending decisions

- [ ] Sample message field: keep as a separate field, or replace variables inline in the body field with placeholder values?
- [ ] Should "Save as draft" be a V1 feature? (requires backend support for draft state)
- [ ] What happens when a PM tries to add a language that Meta has since paused or deprecated for that template category?
- [ ] For the 5+ language grid view — should clicking a language cell open the full language form inline, or route to the add-hsm-language page?
