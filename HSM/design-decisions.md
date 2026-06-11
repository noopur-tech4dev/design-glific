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

## D-29 · AI Assist always visible in message body toolbar; hidden only in read-only view
**Date:** 9 June 2026  
**Decision:** The AI Assist button in the message body toolbar is always visible — it shows as soon as the form is open, not only after a language has been selected or added. It is hidden only when the form enters read-only/view mode (viewing an approved or pending language).  
**Rationale:** Previously the button only appeared after `setupAddLangForm()` ran, which meant a PM looking at a blank form had no indication that AI assistance was available. Showing it upfront sets the right expectation and reduces abandonment on the body field.  
**Implementation:** CSS changed from `display:none` to `display:inline-flex` on `.ai-assist-wrap`. `setFormLocked(true)` now also hides the AI Assist wrap. `clearViewMode()` no longer explicitly hides it — the form card itself is hidden on reset, so the button state doesn't matter.  
**Applied to:** Both `add-hsm-language.html` and `hsm-create-hifi.html`.

---

## D-30 · AI Assist custom prompt — context-switch within the same dropdown
**Date:** 9 June 2026  
**Decision:** Clicking "Custom prompt" in the AI Assist dropdown does not expand the input inline beneath the option. Instead it switches the dropdown into a custom-mode state: the two preset options (Make it professional / Make it utility) and the divider hide, a "← Back" button appears at the top, and the textarea + Apply CTA fill the panel.  
**Rationale:** The old expand-in-place behaviour made the dropdown feel like an accordion — the PM could see both the preset options and the custom input at the same time, which created visual noise and implied the options were still selectable while typing. Hiding the presets focuses attention on the task (writing a prompt) and makes the Back button the clear path to switching mode.  
**Back behaviour:** Clicking "← Back" removes `custom-mode` from the menu. The textarea content is preserved so the PM can return to it if they change their mind.  
**Implementation:** `.ai-assist-menu.custom-mode` CSS class toggles visibility of `.ai-main-options` (hidden) and `.ai-assist-back` (shown). `closeAiAssistMenu()` strips both `open` and `custom-mode` classes so the menu always resets fully on close.

---

## D-31 · HSM list — message body always 1-line clamped
**Date:** 9 June 2026  
**Decision:** The message body column in the HSM list always clamps to 1 line (`-webkit-line-clamp:1`), regardless of density setting. On hover, the full message text appears in a dark tooltip that follows the cursor.  
**Rationale:** Three options were evaluated: (A) always 1 line, (B) body moved under the template ID as a subtitle with the body column removed, (C) 1 line + "show more" expand. Option A was chosen for simplicity — it keeps the column, preserves scannability, and the hover tooltip satisfies the occasional need to read the full body without adding expand/collapse complexity to the table.  
**Implementation:** CSS `webkit-line-clamp:1` on `.body-text`. Tooltip is a `position:fixed` dark panel (`.body-tooltip`) positioned via `mousemove` listener, with smart flip logic when near screen edges. `escHtml()` sanitises body text before injecting into the tooltip.

---

## D-32 · 3-dot menu on HSM list — Delete removed
**Date:** 9 June 2026  
**Decision:** Remove the Delete option from the 3-dot overflow menu on each list row. The menu now contains only "Add new language" and "Send broadcast."  
**Rationale:** Template deletion is a high-stakes, irreversible action. A generic delete in a row-level overflow menu has no confirmation affordance and is too easy to trigger accidentally. If deletion is needed it should be a deliberate flow accessed from the template detail page, not a one-click option in the list.

---

## D-33 · Language view mode — Submit CTA replaced by Delete; disabled when pending
**Date:** 9 June 2026  
**Decision:** When a PM views an existing language (approved or rejected), the Submit for Approval button is hidden and replaced by a "Delete this language" button. When viewing a pending/in-process language, the Delete button is visible but disabled (with a tooltip explaining the reason).  
**Rationale:** "Submit for Approval" makes no sense in view mode — there is nothing to submit (the content is already submitted or approved). Showing it at all creates confusion about what clicking it would do. Delete is the only destructive action available in view context; making it visible-but-disabled for pending languages communicates that deletion will be possible once the review cycle completes.  
**Rejected option:** Showing both Submit and Delete together in rejected/fix mode. Adds ambiguity — which action is primary? The fix-and-resubmit path (for rejected languages) will be a separate dedicated flow if needed in future.

---

## D-34 · Category counts always shown on HSM list — even single-category templates
**Date:** 9 June 2026  
**Decision:** Every template on the list shows a category count badge (e.g., "Utility ×1", "Marketing ×2") regardless of whether all languages share the same category. The earlier logic that showed a plain badge (no count) when all languages matched the same category is removed.  
**Rationale:** The count conveys how many language versions exist in each category, which is directly relevant to billing (WhatsApp charges per-language per-category). A PM with one Utility language vs six should see different data. A plain "Utility" badge with no count hides information that is always meaningful.

---

## D-35 · Meta reclassification badge — two distinct patterns by case
**Date:** 9 June 2026  
**Decision:** Two distinct display patterns for Meta category changes:  
1. **Different category** (e.g., Marketing → Utility): Show the original category as a plain badge + an amber "Utility by Meta" badge alongside. No count on the original badge. No strikethrough or arrow.  
2. **Same category reclassification** (e.g., Meta confirmed Utility when template was already Utility): Show normal count badges only. No Meta tag. The reclassification is surfaced via the notification system (D-07) rather than inline on the list.  
**Rationale:** The different-category case changes billing and is immediately actionable — the PM needs to see it at a glance. The same-category case is informational only (billing cycle may reset, but the category is not changing) — surfacing it inline with the same prominence as a real category change would create noise and false urgency. Notifications are the right channel for that nuance.  
**Unified badge style:** Both same-category and different-category cases use the same amber badge when a Meta tag is shown, avoiding two competing visual treatments for what is conceptually one event type.

---

## D-36 · HSM list — collapsible row expansion with per-language detail
**Date:** 11 June 2026  
**Decision:** Each row in the HSM list is expandable via a chevron (left of title) or a click anywhere on the row. The expanded view shows flat sub-rows — one per language version — with columns aligned exactly to the outer table: message body (col 1 / Title), language tag (col 2 / Languages), category badge (col 3 / Category), last updated (col 4), and an empty col 5. A WhatsApp-format hover tooltip appears on collapsed rows (English preview only) and on each expanded sub-row (language-specific preview).  
**Rationale:** PMs often need to see per-language status and category at a glance without opening a separate page. Expanding in-place avoids a navigation round-trip. Aligning the expand sub-rows to the outer columns makes the expansion feel like a drill-in of the existing row rather than a separate widget.  
**Status column:** Removed entirely. Status is communicated via the coloured dot on each language chip.

---

## D-40 · HSM list expand rows — CSS Grid alignment (CRITICAL, do not regress)
**Date:** 11 June 2026  
**Decision:** Expanded sub-rows are rendered as CSS Grid divs (`.expand-sub-row`), NOT as a nested `<table>`. Column widths are set by reading the outer table's actual rendered `<th>` widths via `getBoundingClientRect()` in a `requestAnimationFrame` callback immediately after the row is shown.  
**Rationale:** A nested `<table>` inside a `<td>` has its own independent layout engine. Even with `table-layout:fixed` and matching `<colgroup>` values on both tables, the browser renders inner and outer column widths differently — they are two separate layout contexts. Every approach to sync them statically (hardcoded pixel widths, colgroup, table-layout:fixed) produced misalignment because the outer table uses `auto` layout and sizes columns by content. CSS Grid with runtime-measured column widths is the only approach that guarantees alignment.  
**Implementation rules — retain at all costs:**
1. Expand sub-rows are `<div class="expand-sub-row">` with `display:grid`
2. `gridTemplateColumns` is set via JavaScript using `th.getBoundingClientRect().width` on each outer `<th>`, run inside `requestAnimationFrame` after `row.style.display='table-row'`
3. The last column always uses `1fr` (not a pixel value) to fill remaining space
4. Do NOT replace with a nested `<table>` — this has been tried and failed
5. Do NOT use static pixel values for `grid-template-columns` — outer table column widths vary by viewport and content

**Pixel alignment specification:**
- Col 1 message text starts at: `16px` (cell pad) + `28px` (chevron spacer) + `8px` (flex gap) = **52px** from col 1 left edge
- This matches the template ID text in collapsed rows: `16px` (cell pad) + `28px` (chevron btn) + `8px` (flex gap) = **52px**
- This matches the "Title" header: `padding-left:52px` on `thead th:first-child`
- All other columns: `16px` left padding — same as outer `tbody td` standard padding
- First column uses standard `16px` padding (no special first-child override) — **do not add extra left padding to the first column**

---

## D-37 · HSM list expand — message body uses language-specific text
**Date:** 11 June 2026  
**Decision:** When a row is expanded, each language sub-row shows the translated body for that language (from the `langBodies` field on the template object), not the English fallback. Where no translated body is stored, the English body is used as a fallback.  
**Rationale:** Showing the English body for every language row defeats the purpose of expansion — the PM can't assess whether the Hindi or Marathi translation is correct if they're looking at the English text. Language-specific bodies make each row actionable (spot a typo, check length, verify variables are in place).  
**Fallback rule:** `t.langBodies?.[l.code] || t.body`. Applied in both `renderExpandRow` (expand sub-rows) and the WA hover tooltip mousemove handler.

---

## D-38 · HSM list hover tooltip — media attachment preview
**Date:** 11 June 2026  
**Decision:** When a template has a media attachment (`t.media` field with `type` and `label`), the WA-format hover tooltip shows a media placeholder block above the message bubble. Image templates show a landscape-orientation grey rectangle with a photo icon + file label. Document templates show a document-style icon + label. The placeholder communicates "this message comes with an attachment" without needing the actual file.  
**Rationale:** PMs reviewing templates from the list need to know which templates have media so they can assess whether the attachment is appropriate for a given broadcast campaign. A pure text preview would make media templates indistinguishable from text-only ones.  
**Media types supported:** `image` and `document`. The media block is hidden (display:none) when the template has no `t.media` field. Tooltip height adjusts automatically when media is present (290px vs 200px) to prevent clipping.

---

## D-39 · "Add new language" icon — translate-style Aa+ icon
**Date:** 11 June 2026  
**Decision:** The "Add new language" icon button on each list row uses a custom SVG combining a Latin "A" letterform with a "+" mark — signifying "add translation." The old globe icon was replaced because a globe is too generic (used for "international", "website", "language settings" interchangeably) and doesn't communicate the specific action of adding a translated version.  
**Rationale:** Icon affordance matters: the action is specifically "create a new language version of this template." The Aa+ motif (used in Google Translate and similar tools) is the established visual shorthand for translation and new-language creation. PMs familiar with translation tools will recognise it immediately; for those who aren't, the tooltip text "Add new language" provides the fallback.

---

## D-41 · Rejected template — two distinct paths from the language list
**Date:** 11 June 2026  
**Decision:** Clicking a rejected language row and clicking "Edit & Re-apply" are two different interactions with two different outcomes. Row click → locked read-only view (Card 2 in `mode-view`, Delete button only, no Submit). "Edit & Re-apply" inline CTA → editable pre-filled form (Card 2 in `mode-add`, all fields editable, rejection reason strip visible, Submit reads "Reapply for Approval").  
**Rationale:** A PM clicking the row wants to understand what was rejected before deciding whether to fix it. Opening the form immediately on row click risks accidental edits or premature re-submission. Separating the intent (inspect vs. act) into two distinct affordances eliminates ambiguity about what a click will do.  
**Implementation:** `showLangView(code)` — always locked, no Submit. `openForReapply(code)` — mirrors `setupAddLangForm()` with pre-filled body/footer from saved `langContent`, then calls `hideButtonStructureControls()` after `setFormLocked(false)` to prevent button deletion.

---

## D-42 · Button structure is read-only in all existing-template contexts
**Date:** 11 June 2026  
**Decision:** When viewing or editing a language on an existing template (view mode, fix/reapply mode, or add-new-language mode), the delete (×) icons on QR buttons and CTA buttons, and all "Add button" triggers, are hidden. Button labels are editable; button structure (count, type) is not.  
**Rationale:** Button structure (number and type of buttons) is submitted to Meta as part of the original template definition. Changing it on a language version would create a structural mismatch — Meta expects language versions to share the same button slots. Deleting a button on one language while it exists on another is not a valid submission state. Hiding the delete controls prevents this class of error entirely.  
**Implementation:** `hideButtonStructureControls()` hides `.remove-btn-abs`, `.var-remove`, add-button trigger IDs, `.clear-btn-link`, and the phone-button add pill. Called from `setupAddLangForm()` (add mode) and `openForReapply()` (after `setFormLocked(false)` re-shows controls). `setFormLocked(true)` already hides these via CSS selector.

---

## D-43 · Auto-translate button styled to match AI Assist
**Date:** 11 June 2026  
**Decision:** The auto-translate trigger button (`.auto-translate-pill`) uses the same visual language as the AI Assist button: white background, green border (`#b9e4cc`), green text (`#119656`), 6px border-radius, Heebo font, same padding. It is not a pill-shaped chip or a plain text link.  
**Rationale:** Both buttons initiate AI-assisted content generation. Visual consistency between them signals to the PM that they are in the same category of action — automation assist, not form control. The original auto-translate styling (plain text, no border) looked like a disabled state and was frequently overlooked.

---

## D-44 · Inline rejected-language CTA labelled "Edit & Re-apply"
**Date:** 11 June 2026  
**Decision:** The inline action link on rejected language rows in the language table reads "Edit & Re-apply" (not "Reapply", "Fix", or "Edit"). The Submit button in the reapply form reads "Reapply for Approval."  
**Rationale:** "Reapply" alone implied submitting without editing — unclear whether you'd be re-sending the original rejected content. "Edit" alone implied free editing without a submission step. "Edit & Re-apply" makes both intentions explicit in sequence. "Reapply for Approval" on the submit button reinforces that the action sends to Meta, not just saves locally.

---

## D-45 · WA hover tooltip header shows templateId · LANG
**Date:** 11 June 2026  
**Decision:** The header line inside the WhatsApp-format hover tooltip reads `{templateId} · {LANG}` (e.g., `welcome_message · HI`). Previously it read `EN · UTILITY` (hardcoded language and category).  
**Rationale:** The tooltip is triggered per-language from the expanded sub-rows. Showing "EN" regardless of which language row the PM is hovering on was simply wrong data. The category label was moved out of the header — it's already visible in the Category column. The templateId grounds the PM in which template they're previewing when multiple rows are visible.  
**Implementation:** Both tooltip trigger paths (chip hover and main row hover) now set `document.getElementById('wat-lang').textContent = tid + ' · ' + lang`.

---

## D-46 · Template ID field — show error state for invalid characters, do not strip silently
**Date:** 11 June 2026  
**Decision:** When a PM types a character not allowed in a Template ID (anything other than `a–z`, `0–9`, `_`), the field shows a red error state immediately: red border + red glow, an error message below ("Only lowercase letters, numbers, and underscores allowed — e.g., order_confirmation"), and the hint text is hidden. The invalid character is NOT removed from the field. On blur, availability check is skipped if invalid characters are present.  
**Rationale:** Silently stripping characters as the PM types (the old behaviour) felt like keys were broken or the field was laggy. PMs reported thinking their keyboard wasn't working. An explicit error message respects user agency — they can see what they typed and understand the rule — rather than having the system secretly modify their input.  
**Root cause fixed:** The previous implementation crashed on a `null` reference (`document.getElementById('existing-dd')` returned `null` — the element doesn't exist) before any validation logic ran, silently swallowing all errors.

---

## D-47 · Tag dropdown — overflow visible, 5-item scroll, Create CTA always shown
**Date:** 11 June 2026  
**Decision:** Three fixes to the tag dropdown in `hsm-create-hifi.html`: (1) `.form-card` changed from `overflow:hidden` to `overflow:visible` so the absolutely-positioned dropdown is not clipped by the card boundary. (2) The scrollable options list (`.tag-dropdown-options`) has `max-height:180px` (≈5 items at 36px each) with `overflow-y:auto`. (3) The "Create" row is always visible — it shows "Create a new tag…" when the input is empty or has an exact match, and "Create '{query}'" when there is a partial non-matching query.  
**Rationale:** The clipping was caused by `overflow:hidden` on the parent card — a common CSS trap with absolute dropdowns. Splitting max-height onto the options child (not the whole dropdown list) keeps the Create CTA pinned below the scroll area at all times. Always showing the Create CTA removes the confusion where it only appeared mid-query and vanished when the user cleared the field.

---

## D-48 · Tags column added to HSM list table with filter
**Date:** 11 June 2026  
**Decision:** A "Tags" column is added to the HSM template list table (between Category and Last updated). Each cell shows the template's tag as a single green pill. A tag filter dropdown is added to the filters bar (next to All Categories). Filtering by tag uses exact match against the template's single `tag` field.  
**Rationale:** Tags are the primary organisational mechanism for NGOs that manage large template libraries. Without a column and filter, tags are write-only — PMs can assign them but have no way to use them for browsing or finding related templates. The column makes the tag visible at a glance; the filter makes it actionable.

---

## D-49 · One tag per template — string, not array
**Date:** 11 June 2026  
**Decision:** Each template has at most one organisational tag, stored as a string (`tag: 'welcome'`), not an array. The tag filter uses exact string equality (`t.tag === tf`). The tag pill in the list and expanded rows renders a single chip or nothing.  
**Rationale:** Multiple tags per template were explored in an earlier pass (arrays like `tags: ['welcome','onboarding']`). Rejected because: (a) multi-tag filtering logic is significantly more complex (AND vs OR, UI for compound filters); (b) NGO PMs have consistently said tags are used for top-level categorisation, not cross-cutting metadata — one category per template is the mental model they use; (c) multiple tags make it harder to build a clean tag taxonomy over time.  
**Migration:** All templates in the prototype data converted from `tags: [...]` arrays to `tag: '...'` strings, keeping the first/most specific value.

---

## D-50 · Expanded sub-rows include Tags column and per-language Last updated
**Date:** 11 June 2026  
**Decision:** Each expanded language sub-row now renders a Tags cell (same tag pill as the parent row — tags apply to the whole template, not individual languages) and a Last updated cell showing the per-language timestamp from `langMeta[code].lastUpdated`. Previously the Tags column was missing from expanded rows (causing column misalignment) and Last updated showed a single value for all languages.  
**Rationale:** Column alignment between the collapsed row and its expanded sub-rows is required for the grid to feel like a coherent drill-in rather than a separate widget (see D-40). The per-language timestamp is the most actionable data in the expanded view — it tells the PM which language was submitted most recently, so they can prioritise follow-up.

---

## D-51 · Category count badge — suppress ×1, show ×N only when count > 1
**Date:** 11 June 2026  
**Decision:** Category badges on the HSM list show a count suffix only when more than one language version shares that category (e.g., "Utility ×3"). When exactly one language is in a category, the badge shows the plain label ("Utility", no count). This supersedes D-34.  
**Rationale:** D-34 showed ×1 on every badge. In practice, most templates in a single-language NGO's library have exactly one Utility language — every row showed "Utility ×1", which added visual noise without adding information. The count is meaningful only when the reader needs to know how many; one is already the expected default. Suppressing ×1 makes the count signal meaningful when it does appear.

---

## D-52 · WhatsApp message preview — timestamps and read-ticks removed everywhere
**Date:** 11 June 2026  
**Decision:** The "9:41 AM ✓✓" and "12:30 ✓✓" timestamp/read-tick lines are removed from all WhatsApp-format preview bubbles across the three prototype files: the inline preview panel in `hsm-create-hifi.html`, the JS-rendered template picker bubbles in both `hsm-create-hifi.html` and `hsm-list.html`, the WA hover tooltip in `hsm-list.html`, and the preview panel in `add-hsm-language.html`.  
**Rationale:** The previews are design-time representations of a template's content, not screenshots of a sent message. Timestamps implied the template had been sent, which is incorrect. Read ticks are specific to individual message delivery status — they have no meaning in a template preview context. Their presence was a carry-over from styling the bubble to look "realistic" but they mislead PMs about what they're looking at.

---

## Resolved decisions

- ✅ **Should the form live on a separate page or in a right drawer/modal?** → Separate full page (see D-13).  
  *Reason: The add-language form has the same field complexity as the create form. A drawer or modal would be too cramped, and the mental model is wrong — the PM is making a Meta submission, not a quick inline edit. A dedicated page also makes it bookmarkable and avoids state management issues when the PM navigates away mid-fill.*

- ✅ **How should button types be differentiated in the form?** → CTA buttons use a unified add-button with inline URL/Phone type toggle per row (D-21). Quick-reply buttons have a 20-character counter per label.  
  *Reason: The old layout had separate sub-sections for URL and Phone buttons, each with its own add action. This forced PMs to understand the distinction before they'd done anything. The inline toggle per row keeps the decision close to the content it affects and is consistent with how other multi-type field patterns work (e.g. button type pickers in flow builders). Quick-reply buttons don't need a type toggle since they are always text-only — the 20-char counter is sufficient to guide correct input.*

---

## Pending decisions

- [ ] **Sample message field: keep as a separate field, or replace variables inline in the body field with placeholder values?**  
  *Context: Meta requires a sample message showing variable placeholders filled with real example values (e.g., `Hi John` instead of `Hi {{1}}`). Currently this is a separate field below the body. An alternative is to let PMs type placeholder values directly inside the body editor that render inline (like a mail-merge preview). The inline approach is more intuitive but requires a richer editor component — beyond the current plain textarea. Deferred until the editor component decision is made.*

- [ ] **Should "Save as draft" be a V1 feature?**  
  *Context: Several PMs have asked to save a half-finished template and return to it later. This requires a backend draft state that is distinct from submitted/pending. Currently there is no draft concept in Glific's template API. Implementing it in the frontend without backend support would mean storing draft data locally (localStorage/IndexedDB), which is fragile across devices and sessions. Blocked on backend prioritisation.*

- [ ] **What happens when a PM tries to add a language that Meta has since paused or deprecated for that template category?**  
  *Context: Meta occasionally restricts certain language+category combinations (e.g., pausing marketing templates in certain locales). Glific currently has no signal from Meta about which combinations are currently permitted. The form would let a PM build and submit a template that Meta will silently reject. Needs a decision on whether to (a) surface a warning if we can detect the paused state via the API, (b) show a generic advisory note on the language picker, or (c) handle it post-rejection via the fix flow (D-25). Blocked on API capability check.*

- [ ] **For the 5+ language grid view — should clicking a language cell open the full language form inline, or route to the add-hsm-language page?**  
  *Context: In the compact language grid (activated when a template has more than 5 languages), clicking a cell needs to open the content form for that language. Two options: (a) open it inline within the same card, expanding below the grid — keeps context but causes vertical jump; (b) route to `add-hsm-language.html` pre-loaded for that language — consistent with the existing flow but loses grid context. Option (b) is the current default (consistent with D-13), but option (a) may be better UX for the rare power-user case. No implementation yet — grid view is not V1 scope.*
