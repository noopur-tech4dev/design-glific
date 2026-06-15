# Glific HSM Templates — Redesign Reference

A complete reference for the interactions, behaviours, and flows across the HSM template management redesign. Covers all three prototype files and their relationships.

**Prototype files**
- [`hsm-list.html`](https://noopurvarma.github.io/design-glific/design/hsm-list.html) — Template list with expand, filter, search, hover previews
- [`hsm-create-hifi.html`](https://noopurvarma.github.io/design-glific/design/hsm-create-hifi.html) — Create new template form
- [`add-hsm-language.html`](https://noopurvarma.github.io/design-glific/design/add-hsm-language.html) — Add / view / fix a language version on an existing template

---

## 1. System overview

HSM (Highly Structured Message) templates are pre-approved WhatsApp message formats. Every template must be submitted to Meta before it can be used in a Glific flow. The key constraints that shape the design:

- A template has one or more **language versions**. Each version is submitted to Meta independently.
- Meta assigns a **status** to each language version: approved, pending, or rejected.
- The **template ID** (element name) is permanent once submitted — it cannot be changed.
- **Buttons** (QR / CTA) are defined at the template level and shared across all language versions — structure cannot differ by language.
- **Category** (Utility / Marketing / Authentication) affects billing. Meta can reclassify a category after submission.
- **Tags** are organisational only (one per template) and do not go to Meta.

---

## 2. Page: HSM List (`hsm-list.html`)

### 2.1 Table layout

The main table has five columns:

| Column | Content |
|---|---|
| Title | Template ID, tag pill (if present) below it |
| Languages | Language chips (up to 4 shown, +N overflow), status summary line |
| Category | Category badge(s) with count when >1 per category |
| Last updated | Most recent submission date |
| Actions | Aa+ (add language), Eye (view), 3-dot menu |

**Title cell structure**
- Line 1: Template ID in bold (`tpl-id`)
- Line 2: Tag pill in green if the template has a tag — shows inline below the ID, no separate column

### 2.2 Row interactions

**Collapsed row**
- Click anywhere on the row → expands to show per-language sub-rows
- Hover on row body → dark tooltip appears with English message preview (plain text, no WhatsApp styling)
- Click chevron (↓) → same as row click

**Expanded row**
- One sub-row per language version, columns aligned to the outer table via CSS Grid with runtime-measured column widths
- Hover on a language sub-row → WhatsApp-format tooltip appears (styled bubble, language-specific body, media placeholder if applicable)
- Tooltip header: `{templateId} · {LANG}` — e.g. `welcome_message · HI`
- No timestamps or read-ticks in any preview bubble

**Language chips**
- Green dot = approved, amber dot = pending, red dot = rejected
- Up to 4 chips shown; beyond 4 a "+N more" button expands the rest
- Status summary line below chips: e.g. "2 rejected · 1 pending" (only shown when at least one language is not approved)

### 2.3 Category badges

- Single-category template, 1 language: plain badge ("Utility")
- Single-category template, multiple languages: badge with count ("Utility ×3")
- Mixed-category template: one badge per category, count only on those with >1 ("Utility ×2  Marketing")
- Meta reclassification (different category): original category plain badge + amber "Utility by Meta" badge
- Meta reclassification (same category confirmed): normal count badges only, no Meta badge

### 2.4 Filters and search

Three controls in the filters bar:
- **All Categories** dropdown — filters by template category (Utility / Marketing)
- **All Tags** dropdown — filters by single tag, exact match (tag column is removed but this filter remains fully functional)
- **Search input** — matches against template ID and message body text

All three filters compose (AND logic). Clearing any filter re-renders the full unfiltered list.

### 2.5 Actions column

All three buttons use CSS `data-tip` tooltips (appear immediately on hover, dark pill style with arrow) — not native browser `title` tooltips.

- **Aa+ icon** — "Add new language" tooltip → routes to `add-hsm-language.html?template={id}`
- **Eye icon** — "View template" tooltip → routes to `add-hsm-language.html?template={id}&action=view` (read-only language view)
- **3-dot menu** — "More options" tooltip → opens dropdown with three items:
  - **Copy UUID** — copies the template's UUID to clipboard; shows a brief toast confirmation
  - **Duplicate template** — creates a copy of the template (prototype: no-op placeholder)
  - **Send broadcast** — routes to broadcast flow (prototype: no-op placeholder)
  - Delete is intentionally absent — see D-32

### 2.6 Template library modal (on HSM list)

**Trigger:** "Template library" outline button in the top-right header bar (distinct from the green "Create" button). A hover tooltip on the button reads "Pre-built templates from Meta's WhatsApp library — approved and ready to use."

**Purpose:** Let PMs browse and select a pre-approved Meta template as a starting point for a new template submission — so they don't write from scratch and can leverage formats Meta has already approved.

**Modal layout:** Full-screen dimmed overlay → centred modal (`880px × 620px`).

**Left panel — Browse & Select**

- Title: "Select a Template"
- **Search input** — filters templates by title or body text as you type
- **All languages dropdown** — filters to templates available in a specific language (English, Hindi, Marathi, Telugu, Tamil, Bengali, Gujarati)
- **Category pills** — Utility (default active) / Authentication; toggles which category group is shown
- **Grouped list** — templates grouped by feature (e.g., Account updates, Onboarding, Order status). Each group is a collapsible accordion section:
  - Click group header → expands/collapses the template rows within
  - Each row shows the template title + a radio-style check circle
  - Selecting a row fills the right panel with a WA preview and enables the footer CTA
  - When language filter is active, groups with no templates in that language are shown dimmed and non-interactive with a "No [type] templates available in [language]" hint
- **Template count** — small line below the list: "Showing N of M templates"

**Right panel — Preview**

- Dark green background (matches WhatsApp chat backdrop)
- Default state: centred empty message "Select a template to see a preview"
- On selection: WhatsApp-style message bubble appears (light green `#e4f7b0`):
  - Template title in bold
  - Body text with variables `{{text}}` highlighted in green
  - Footer (italic, separator line) if present
  - Button row with icon + label if present
  - No timestamp, no read-ticks
- Info line below preview: "Using this template pre-fills the message body, footer, and button fields. All fields stay fully editable."

**Footer CTA**

- "Use this template" button — disabled until a template is selected; enabled on selection
- On click: closes the modal and routes to `hsm-create-hifi.html?template={id}` — the create form opens pre-filled with the selected template's body, footer, and buttons

**Dismiss:** Click the × button, click outside the modal, or press Escape

---

## 3. Page: Create Template (`hsm-create-hifi.html`)

### 3.1 Form structure

The form is a single-column page with a live WhatsApp preview panel on the right. Sections in order:

1. **Template ID** — lowercase, numbers, underscores only; permanent after submission; real-time availability check (600ms debounce); error state for invalid characters (does not strip silently)
2. **Language** — dropdown selection; chip row shows existing languages once selected
3. **Category** — Utility / Marketing / Authentication; inline explanation of billing impact
4. **Message body** — textarea with unified toolbar: Bold / Italic / Strikethrough | Add Variable | char counter (0/1024); amber warning at 900+, red at 1024+
5. **Footer** (optional) — short disclaimer text
6. **Buttons** (optional) — unified add-button, each row has URL/Phone type toggle; max 2 URL + 1 phone; 20-char counter on QR buttons
7. **Media attachment** (optional) — Image / Document / Video type selection with size limits shown inline
8. **Tags** (optional) — custom dropdown with create-new; one tag per template; `overflow:visible` on form card to prevent dropdown clipping

### 3.2 Template ID field behaviour

- Typing any character → `onerInput` fires
- Invalid character detected (`/[^a-z0-9_]/`) → red border + error message shown; availability check skipped
- Valid character string → 600ms debounce → `checkNameAvailability()` → shows "checking…" spinner, then either "taken" error or clears feedback (no "available" confirmation)
- On blur → re-runs availability check if valid and not already an existing template
- "Use existing template" path → Template ID becomes read-only chip; field collapses

### 3.3 Message body toolbar

Fixed toolbar docked below the body textarea:
- **B** — wraps selection in `*...*` (WhatsApp bold)
- **I** — wraps selection in `_..._` (WhatsApp italic)
- **S̶** — wraps selection in `~...~` (WhatsApp strikethrough)
- **Add Variable** — inserts `{{N}}` at cursor (N = next unused number)
- **AI Assist** — dropdown with: Make it professional, Make it utility tone, Custom prompt
- **Counter** — live 0/1024 display; thresholds: neutral → amber (900) → red (1024+)

### 3.4 AI Assist dropdown behaviour

- Opens as a dropdown above the toolbar
- **Preset options**: one-click → fills body textarea with AI-generated text, closes menu
- **Custom prompt**: switches dropdown to custom-mode (presets hidden, Back button + textarea + Apply shown)
- **Back button**: returns to preset view, preserves typed prompt content
- Closing the dropdown (click outside or Esc) resets to preset view

### 3.5 Buttons section

- **[+ Add Button]** → creates a new row with URL/Phone type toggle
- Selecting URL → reveals Label + URL + Static/Dynamic toggle (collapsed under "Advanced")
- Selecting Phone → reveals Label + Phone number fields
- **Delete (×)** on each row → removes that button row
- [+ Add Button] hides when both URL limit (2) and phone limit (1) are reached
- URL chip is greyed in the type toggle when 2 URL buttons already exist
- Phone chip is greyed when a phone button already exists

### 3.6 Template Library modal

- Triggered by "Use existing template" link below the Template ID field
- Left panel: categories (Utility / Authentication), search, grouped item list
- Right panel: dark green background, WhatsApp-style bubble with body + footer + button(s), no timestamp
- Selecting a template enables "Use this template" CTA
- Applying pre-fills: body, footer, buttons in the main form; Template ID is set to the selected template's ID (read-only); language chips are loaded

### 3.7 Live preview panel

- Sticky right-hand panel showing real-time WhatsApp bubble
- Updates on every keystroke in body, footer, button fields
- Variables `{{1}}`, `{{2}}` highlighted in green
- Media type shown as placeholder block above the bubble when selected
- No timestamp or read-ticks

### 3.8 Submit behaviour

- Submit button disabled until: Template ID valid + at least one variable or body text present + no field errors
- On submit: loading state (spinner on button, fields disabled), simulated 1.8s delay
- Success → success modal with template name and "View template" CTA
- Error → inline error banner below submit row

---

## 4. Page: Add / View Language (`add-hsm-language.html`)

### 4.1 Page layout

Two cards, stacked vertically:

**Card 1 — Template Identity + Language table** (always visible)
- Template name as page heading (read-only)
- Three tabs: Approved | In Progress | Rejected
- Each tab shows a table of language versions in that status
- Rejected tab is auto-selected on load if any rejections exist

**Card 2 — Language content form** (hidden by default, appears below Card 1 when a language action is triggered)
- Border colour signals mode: green (adding new), grey (viewing), red (rejected/reapply)
- Shared form fields: body, footer, buttons, media

### 4.2 Language table

Each row shows: language name + code, category badge, last updated, status, and inline action links.

Inline action links per status:
- **Approved / Pending** → row click = opens Card 2 in locked view mode
- **Rejected** → row click = opens Card 2 in locked view mode; "Edit & Re-apply" link = opens Card 2 in editable reapply mode

### 4.3 Card 2 modes

**mode-view (locked)**
- All fields read-only (`pointer-events:none`, greyed backgrounds)
- No AI Assist button
- No Submit button
- Delete button visible; disabled if status is pending (cannot delete while under review)
- Rejection reason strip visible for rejected languages (even in locked mode)
- Button delete (×) icons hidden

**mode-add (adding new language)**
- Method picker visible (Manual / AI Translate)
- All fields editable
- AI Assist and Auto-translate buttons visible
- Button labels editable; button delete (×) icons hidden (structure is fixed by existing template)
- Reference strip (collapsible green panel) shows English source body + buttons above the form
- Submit button reads "Submit for Approval"

**mode-reapply (fixing rejected language)**
- Same surface as mode-add, but pre-filled with the rejected language's saved body + footer
- Rejection reason strip visible at top of card (red)
- Reference strip visible (same as mode-add)
- Button labels editable; button structure controls hidden
- Submit button reads "Reapply for Approval"
- Delete button also visible (allows removing the rejected version entirely)

### 4.4 AI Translate button

- Visible in mode-add and mode-reapply, hidden in mode-view
- Styled identically to AI Assist (white background, green border, green text, Heebo font)
- One-click: fills body + footer with simulated translated content in ~1.4s
- Shows loading spinner while generating

### 4.5 Reference strip

- Collapsible green panel injected above the body field
- Shows approved English (or earliest approved language) source content
- Sections: Body, Footer (if present), Buttons (inline chips above button rows)
- Button reference chips injected directly above the QR / CTA input rows (not at section header level)
- Collapses to a single "Show English source" toggle line

### 4.6 Live preview panel

- Same as `hsm-create-hifi.html`: sticky right-hand WhatsApp bubble
- Updates on every keystroke
- No timestamp or read-ticks

---

## 5. Cross-cutting behaviours

### 5.1 Form locking

`setFormLocked(true)` applies to:
- All `<input>`, `<textarea>`, `<select>` fields (`pointer-events:none`, grey background)
- All `.remove-btn-abs`, `.var-remove`, `.clear-btn-link`, `.green-pill-btn` elements (hidden)
- AI Assist button (hidden)

`setFormLocked(false)` restores interactivity but does NOT restore button structure controls — those are separately controlled by `hideButtonStructureControls()`.

### 5.2 Button structure rule

In any mode involving an existing template (view, reapply, add-new-language), button structure controls are always hidden:
- Delete (×) icons on QR rows (`.remove-btn-abs`)
- Delete icons on CTA rows (`.var-remove`)
- Add-button triggers (`#add-url-btn-trigger`, `#add-qr-btn-trigger`, `#add-cta-btn-trigger`)
- Phone button add pill
- Clear-button links

Button label inputs remain editable in mode-add and mode-reapply.

### 5.3 WhatsApp preview rules (all files)

- Background: `#e4f7b0` (light green bubble)
- Variables `{{1}}`, `{{2}}` highlighted in green or teal
- Footer: grey italic text below a hairline separator
- Buttons: white rounded rows with teal/green label text and icon
- Media: grey placeholder block above the bubble (image = landscape rectangle + photo icon; document = doc icon + label)
- **No timestamps. No read-ticks.** Timestamps were removed as they imply a sent message, not a template definition.

### 5.4 Tag behaviour

- One tag per template (string), not multiple (array)
- Custom dropdown with: type-to-filter existing tags, create-new row always visible
- After selection: value shown in the input field (no coloured pill below)
- On existing templates: tag field is locked ("Tag is shared across all language versions and cannot be changed here")
- Filter on list page: exact match; selecting a tag shows only templates with that tag

### 5.5 Element name / Template ID validation

Rules (in priority order):
1. Always lowercase (auto-converted on input)
2. Characters outside `[a-z0-9_]` → red error state shown; invalid char stays in field; availability check skipped
3. Valid format but already taken → "This Template ID is already in use" error
4. Valid format, not taken → feedback clears silently (no "available" confirmation message)

### 5.6 Category count display

- Exactly 1 language in a category → plain badge: "Utility"
- More than 1 language in a category → badge with count: "Utility ×3"
- Count suffix only appears when it carries information (i.e., >1)

---

## 6. Key flows

### Flow 1 — Create a new template from scratch
1. `hsm-list.html` → click **Create** (top-right green button)
2. `hsm-create-hifi.html` opens blank
3. Fill Template ID → validate format + availability
4. Select language + category
5. Write body (use formatting toolbar, Add Variable, AI Assist as needed)
6. Optionally add footer, buttons, media attachment, tag
7. Submit → loading state → success modal → route back to list

### Flow 2 — Create from an existing Meta template
1. `hsm-create-hifi.html` → click **Use existing template**
2. Template Library modal opens
3. Browse / search / filter templates; click one to see WA preview
4. Click **Use this template**
5. Modal closes; form pre-filled with selected template's body, footer, buttons; Template ID set to selected template's ID (read-only)
6. Adjust content as needed → Submit

### Flow 3 — Add a new language to an existing template
1. `hsm-list.html` → Aa+ icon on a template row (or 3-dot → Add new language)
2. `add-hsm-language.html` loads with that template's data
3. Card 1 shows existing language versions in their tabs
4. Click **Add new language** chip → language picker appears
5. Select language → Card 2 appears in mode-add (green border)
6. Choose to translate manually or use AI Translate
7. Fill body + footer (reference strip shows English source for reference)
8. Submit → loading state → language added to In Progress tab

### Flow 4 — View an approved/pending language
1. `hsm-list.html` → Eye icon on a template row
2. `add-hsm-language.html` loads with Card 1 showing all languages
3. Click any approved/pending language row → Card 2 appears in mode-view (grey border)
4. All fields read-only; Delete button visible (disabled if pending)
5. No edits possible; PM can read content and close

### Flow 5 — Fix a rejected language (Edit & Re-apply)
1. `add-hsm-language.html` loads; Rejected tab auto-selected (red dot languages)
2. Click **Edit & Re-apply** inline link on the rejected row
3. Card 2 appears in mode-reapply (red border): rejection reason strip visible, fields pre-filled with original content
4. Edit body/footer as needed; AI Translate available; reference strip visible
5. Submit → "Reapply for Approval" → loading state → language moves to In Progress tab

### Flow 6 — Inspect a rejected language (read-only)
1. Same as Flow 5 step 1–2, but click the row itself (not "Edit & Re-apply")
2. Card 2 appears in mode-view: rejection reason visible, all fields read-only
3. PM reads the rejection reason and original content; decides whether to edit or delete
4. Delete button available (removes the language version)

### Flow 7 — Filter and find templates by tag
1. `hsm-list.html` → Tags filter dropdown → select a tag
2. Table re-renders showing only templates with that exact tag
3. Tag pills visible in the Tags column and in each expanded sub-row
4. Combine with Category filter and search for compound filtering

---

## 7. File and component inventory

### `hsm-list.html`
- `TEMPLATES[]` — in-memory template data (12 templates)
- `getFiltered()` — applies category, tag, search filters
- `renderTable()` — renders all rows from filtered data
- `renderExpandRow(t)` — renders CSS Grid sub-rows for a template's languages
- `toggleExpand(idx, tid, e)` — show/hide expand row; syncs grid column widths via rAF
- `catCostHTML(t)` — renders category badge(s) with count (suppresses ×1)
- `langCell(langs, i, t)` — renders chip row with +N overflow
- `copyUUID(uuid, e)` — clipboard copy with toast
- WA hover tooltip (`_waTip`) — WhatsApp-format bubble on expand sub-row hover
- Row body tooltip (`_rowTooltip`) — plain text preview on collapsed row hover

### `hsm-create-hifi.html`
- `TEMPLATES{}` — existing template data (for ID availability check + "use existing" picker)
- `onElementInput(el)` / `onElementBlur(el)` — Template ID validation and availability check
- `updateBodyCounter()` — live char count with threshold colouring
- `updatePreview()` — syncs right-panel WA bubble to current form state
- `handleSubmit()` — form submit with loading/success/error states
- `openTplPicker()` / `closeTplPicker()` — Template Library modal
- `selectMetaCard(id)` — updates WA preview in modal; no timestamp rendered
- `applyTplSelection()` — pre-fills form from selected Meta template
- `toggleAiAssistMenu()` / AI assist functions — AI Assist dropdown with custom-mode toggle
- Tag dropdown: `openTagDropdown()`, `renderTagOptions()`, `selectTag()`, `createTag()`

### `add-hsm-language.html`
- `TEMPLATES[]` — template data including `langBodies`, `langContent`, `rejectionReason` per language
- `showLangView(code)` — opens Card 2 in mode-view (always locked, Delete only)
- `openForReapply(code)` — opens Card 2 in mode-reapply (editable, pre-filled, rejection strip)
- `setupAddLangForm(code)` — builds button/variable structure for add/reapply mode; calls `hideButtonStructureControls()`
- `hideButtonStructureControls()` — hides all structural edit controls (×, add triggers)
- `setFormLocked(lock)` — locks/unlocks all form fields and secondary controls
- `showReferenceStrip()` — shows collapsible English source reference above body field
- `renderLangTable(t, selectedCode)` — renders language table with inline action links
- `openAddLangRow()` — shows language picker for adding a new language
- `updatePreview()` / `updateBodyCounter()` — live preview and counter sync

---

## 8. Data model (prototype)

```
Template {
  id: string                    // Template ID / element name (permanent)
  category: string              // 'utility' | 'marketing' | 'authentication'
  categories: string[]          // all categories used across lang versions
  langCats: { [code]: string }  // per-language category
  metaCategory?: string         // if Meta reclassified (triggers inline badge)
  langs: [{ code, status }]     // all language versions with status
  body: string                  // English (default) body text
  langBodies: { [code]: string }// translated body per language
  footer?: string               // footer text
  buttons?: string[]            // button labels
  media?: { type, label }       // 'image' | 'document', display label
  langMeta: { [code]: { lastUpdated } } // per-language timestamps
  tag?: string                  // single organisational tag
  langContent?: { [code]: { body, footer } } // saved content for rejected languages
  uuid: string                  // unique identifier
  created: string               // relative date string
  status: string                // aggregate status (approved/pending/rejected)
}
```

---

*Last updated: 11 June 2026*
