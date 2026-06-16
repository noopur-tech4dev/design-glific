# Design Approach

## How this project works

Design for Glific happens through **iterative high-fidelity HTML prototypes** — not Figma, not wireframes. Every design decision is built directly as a working prototype in HTML/CSS/JS, then refined through iteration with the PM (Noopur). This means:

- The prototype IS the spec — it shows exact layout, interaction, edge cases, and copy
- Changes are made live in the file, reviewed in the browser, and iterated immediately
- Design decisions are documented in a separate markdown file as they are made
- The goal is always: something a developer can implement directly from the prototype

---

## File structure

All design work lives in: `/Users/noopurvarma/Glific/Glific-design/design/`

**Always read/write from the `design/` subfolder, not the root.**

| File | What it is |
|---|---|
| `hsm-list.html` | HSM Templates listing page — table, filters, expand rows, hover tooltips, template library modal |
| `hsm-create-hifi.html` | Create new template form — all fields, validation states, AI Assist, live preview panel, template picker modal |
| `add-hsm-language.html` | Add / view / fix a language version — two-card layout, tabbed language table, reapply flow |
| `design-decisions.md` | Every design decision with date, rationale, alternatives rejected. Updated at end of each session. |
| `hsm-redesign.md` | Full interaction reference: all flows, component behaviours, state transitions, data model |

**GitHub repo:** `github.com/noopurvarma/design-glific`  
**Live previews:** `https://noopurvarma.github.io/design-glific/design/[filename].html`

---

## Design principles (established through this project)

These principles emerged from working on the HSM redesign and apply broadly to Glific product design:

**1. Transparency over fake progress**
Glific has no visibility into Meta's review pipeline. Never show a progress bar or real-time status for external processes. Instead: tell the user exactly what we know, what we don't, and what to expect (e.g., "usually 24–48 hours; we'll notify you").

**2. Permanent decisions need visible friction**
The Template ID cannot be changed after submission. Design must make this impossible to miss — not just a tooltip, but a prominent callout before the first submission, and a locked read-only state afterwards.

**3. Multi-lingual is first-class**
Language is the second field in the create form (not buried in settings). Language chips appear on the list page. Adding a language version is a primary CTA, not an advanced feature. Most templates will have 1 language, but the design should make 5+ feel natural too.

**4. Error states, not silent failures**
Never strip invalid characters from inputs silently — show an error state and leave the input intact. Never suppress a field error — show it visibly. PMs assume "nothing happened" means "it worked".

**5. Separate inspect from act**
Clicking a rejected language row opens a locked read-only view (inspect). "Edit & Re-apply" opens the editable form (act). These are different intents and must be different affordances. Do not pre-emptively open an edit form when the user might just be reading.

**6. Secondary actions belong in overflow menus**
Low-frequency utility actions (Copy UUID, Duplicate template) live in the 3-dot overflow menu. High-frequency actions (Add language, View) live as visible icon buttons. Delete is never in a casual overflow menu — it requires a deliberate flow.

**7. WhatsApp preview = no metadata**
WA-style preview bubbles show message content only: body, footer, buttons, media placeholder. Never show timestamps ("9:41 AM") or read-ticks (✓✓) in a template preview — those belong to a sent message, not a template definition.

---

## Documentation rhythm

**During a session:**
- Build and iterate in the HTML prototypes
- Note decisions as they happen (can be captured informally in conversation)

**At the end of each session:**
- Update `design-decisions.md` with new decisions (D-NN format, date, rationale, alternatives rejected)
- Update `hsm-redesign.md` if any interaction, flow, or component behaviour changed

This keeps the documentation in sync with the prototype and means any new session can load the docs and have full context without re-reading code.

---

## How to work on a prototype file

1. **Always `Read` the file before editing** — never assume the current state
2. **Use `Edit` for targeted changes** — not full rewrites unless the change is sweeping
3. **Check for ripple effects** — e.g., removing a column from a table requires updating the colgroup, thead, tbody row render, expand sub-rows, and colspan values
4. **Test in browser** — after each change, open the file to verify visually
5. **Update docs** — design-decisions.md and hsm-redesign.md at end of session

## CSS conventions in the prototypes

- Colours: green `#119656` (primary), amber `#d97706` (warning), red `#dc2626` (error), `#e4f7b0` (WA bubble)
- Font: Heebo (Google Fonts, loaded via CDN)
- Tooltips: CSS `data-tip` pseudo-elements (not native `title` attributes)
- Dropdowns that need to overflow card boundaries: parent card must have `overflow:visible`, not `overflow:hidden`
- WA preview panels: dark green gradient background, `#e4f7b0` bubble — no timestamp, no read-ticks
