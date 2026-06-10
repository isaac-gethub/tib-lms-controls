# Design Rationale
## SAP Controls & Compliance Training Programme — LMS Design System
### KABO-TRN-CTL-WPB-001 | WP-01 | TIB Systems Inc.

---

## 1. Design Philosophy

The core tension in eLearning design is between **authority** and **approachability**. Platforms that lean too hard into corporate authority feel cold and demotivating. Platforms that chase consumer-app friendliness lose the credibility that professional certification audiences demand.

The TIB Systems LMS resolves this tension by treating the learner as a **practitioner in formation** — someone who already operates in or near the SAP GRC space and wants to be treated as an intelligent adult. The visual language is consequently drawn from two reference worlds:

- **Premium financial/legal publishing** — confident use of serif display type, navy as a dominant tone, restrained whitespace, data tables that look authoritative rather than playful.
- **Modern developer tooling** — monospace accents, precise token-driven spacing, a systematic rather than decorative approach to colour.

The result is a design that says: *this is serious material, and we know how to present it.*

---

## 2. Colour Strategy

### Brand Palette Elevation
The brief specified three brand anchors: Navy `#1F3864`, Teal `#1F7A8C`, Amber `#C65911`. These were elevated into a full 5-tint system per hue (deep → mid → base → light → faint) rather than used as flat swatches.

| Role | Rationale |
|---|---|
| **Navy** — dominant structural colour | Carries authority. Used for the header, sidebar, quiz header, and completion banner. Dark enough to work as near-black without feeling cold. |
| **Teal** — primary interactive colour | All interactive affordances (buttons, focus rings, active states, AI tutor header, progress bars) use teal. Warm enough not to read as generic corporate blue, distinctive enough to own the interaction layer. |
| **Amber** — accent / CTA / warning | Amber is used sparingly: warning callouts, the Mark Complete CTA variant, and accent stripes on concept cards. Its rarity makes it an attention signal rather than wallpaper. |

### Semantic Layering
Colours are never used raw — they are always accessed via semantic aliases (`--text-primary`, `--bg-sidebar`, `--color-success`). This means a future re-theme (e.g. a dark-mode variant) requires changes only to the token layer, not to component code.

### Background System
Three background levels (`--bg-canvas` #F4F6FB, `--bg-surface` #FFFFFF, `--bg-surface-2` #F0F3FA) create a subtle elevation hierarchy without resorting to heavy shadows. The canvas is not pure white — the slight blue-grey cast reinforces the professional tone and reduces eye strain during long reading sessions.

---

## 3. Typography

### Font Selection
Three typefaces form a deliberate triad:

**DM Serif Display** — headings, module titles, score numbers, completion banner. A transitional serif with high contrast and elegance. It signals premium publishing, not generic corporate. The italic variant is used in hero headings to add expressivity without losing authority.

**DM Sans** — body copy, UI labels, navigation, quiz questions. DM Sans is the optical companion to DM Serif Display — they share the same design family, so they coexist without visual friction. Its variable-weight range (300–700) provides full tonal range within one typeface.

**JetBrains Mono** — module codes (B1, B2…), transaction code references (FB60, F110), quiz counters, progress percentages, score displays. Using a monospace font for codes and data signals precision and reinforces the SAP-technical context. It also prevents numeric jitter when scores or counters update.

### Type Scale
The scale follows a modular progression (approximately 1.25 ratio) from `--scale-2xs` (10px) to `--scale-5xl` (60px). All sizes are defined in `rem` to respect user font-size preferences. Line-height and letter-spacing are tuned per category — tight tracking on display headings, relaxed on body copy, generous caps spacing on labels.

### Prose Width Constraint
Body paragraphs are constrained to `max-width: 70ch`. This is a reading-ergonomics decision: beyond ~75 characters per line, the eye struggles to track back to the start of the next line. The constraint applies in CSS globally to `<p>` elements, ensuring every content author benefits without remembering to add it manually.

---

## 4. Spatial System

All spacing is derived from an 8-point base grid via named tokens (`--space-1` through `--space-24`). This provides:

- **Predictability** — components composed of token-spaced elements are always optically aligned, even when authors are not thinking about alignment consciously.
- **Rhythm** — consistent vertical spacing between sections creates a reading cadence. The module content area uses `--space-8` between major sections, `--space-6` between paragraphs and callouts, `--space-4` for internal component padding.
- **Scalability** — changing the base size of a single token cascades consistently across all usages.

---

## 5. Component Decisions

### Module Hero
The hero uses a three-stop CSS gradient (navy-deep → navy → teal-deep) rather than a flat colour or an image. This avoids image-loading dependencies (critical for offline/slow connections), maintains brand consistency, and provides a canvas for the two radial overlays that add depth without complexity. The `::after` pseudo-element adds a 1px gradient rule at the bottom — an almost invisible detail that sharpens the transition to the content area.

### Sidebar
The sidebar sits on `--navy-deep` (#152849) rather than pure navy — slightly darker to recede behind the main content plane. Active states use a 3px left border rule in teal-light (not a full background highlight) to mark position without overwhelming the narrow panel. The section labels are set in 10px all-caps with generous tracking — the conventional treatment for sidebar group dividers, but tinted to white at 35% opacity rather than a separate colour.

### Accordions
Panels animate via `max-height` transition rather than `height: auto` (which cannot be transitioned). The max-height is set to 600px — large enough for the deepest expected panel content, small enough to produce a fast, natural-feeling expand. The `cubic-bezier(0.4, 0, 0.2, 1)` easing mirrors Material Design's standard curve, chosen for its smooth deceleration which reads as responsive without being bouncy.

### Quiz Options
Each option is a `<button>` element (not `<div>`) for native keyboard accessibility. The three-state system (default → selected → revealed correct/incorrect) uses border-colour and background-colour changes rather than added icons alone, ensuring the state is perceptible to colour-blind users (the border-width change from 2px to 2px highlighted is a secondary signal; the background change is the primary one). The A/B/C/D marker circle transitions between states — white with grey border at rest, filled navy on selection, filled green/red on reveal.

### AI Tutor Panel
The tutor panel's header uses the teal gradient (not navy) to visually differentiate the AI space from the course navigation and quiz spaces. The status dot animation uses a `pulse` keyframe — a universally-understood "live" signal. The typing indicator uses three dots with staggered animation delays (0s, 0.2s, 0.4s) — the minimum viable treatment that clearly communicates "the system is thinking."

### Callout Blocks
Five callout variants (info/warning/success/danger/navy) share a left-border + faint background treatment. The left border is 4px — thick enough to be immediately scannable, thin enough not to dominate. The background faint tints are derived from the brand palette's `--faint` tokens, so the callouts feel like extensions of the brand rather than external UI elements.

### Completion Banner
The banner re-uses the module hero gradient but adds a central radial glow — a visual signal that this is a moment of arrival, not just another section. The confetti system uses pure CSS animations (`confettiFall` keyframe) on dynamically created `<div>` elements — no canvas API, no library, no additional load. Fifty-five pieces is the threshold at which the effect reads as celebration without becoming distracting.

---

## 6. Motion & Animation

All transitions are defined via token (`--transition-fast` 120ms, `--transition-base` 220ms, `--transition-slow` 380ms, `--transition-spring` with cubic-bezier overshoot). The hierarchy:

- **Fast (120ms)** — micro-interactions: hover colour changes, button active scale.
- **Base (220ms)** — state transitions: tab switching, accordion triggers, option selection.
- **Slow (380ms)** — layout transitions: sidebar open/close, accordion panel expansion.
- **Spring** — elements that benefit from slight overshoot: button-icon hover scale, connect-to-reality snapping feedback.

The `@keyframes` system defines seven named animations. No transition or animation is purely decorative — each one carries semantic meaning (fade-in = new content arriving; slide-down = contextual feedback appearing; typing-dot = system processing; confetti = achievement).

---

## 7. Accessibility Considerations

- All interactive elements are native `<button>` or `<a>` elements — keyboard-navigable by default.
- Focus rings are defined globally via `:focus-visible` using a 2px teal outline with 2px offset — visible against both light and dark surfaces.
- Colour is never the sole differentiator of state — shape, border, and icon changes accompany all colour shifts.
- Prose paragraphs are constrained to 70ch for readability.
- The sidebar uses `overflow-y: auto` with a custom thin scrollbar — accessible on both mouse and touch.
- Print styles strip navigation, the AI tutor, and all interactive chrome, leaving only content for printing or PDF export.

---

## 8. Performance Architecture

The design system is deliberately **zero-dependency** at runtime:

- One external resource: Google Fonts (2 families, 3 variants each) loaded via a single `@import` with `display=swap`.
- No JavaScript framework, no CSS preprocessor, no build step.
- All icons are inline SVG — zero HTTP requests, infinitely scalable, styleable via `currentColor`.
- Animations are CSS-only wherever possible. JavaScript animation is used only for confetti (DOM element creation) and the AI tutor streaming.
- `localStorage` is the only browser API used for persistence — no cookies, no IndexedDB, no server.

This architecture means the LMS opens by double-clicking `index.html` on any machine, with or without an internet connection (after the Google Fonts initial load is cached).

---

## 9. Naming Conventions

| Convention | Example | Rule |
|---|---|---|
| BEM-influenced classes | `.quiz-option__marker` | Block (`quiz-option`), element (`__marker`), modifier (`--correct`) |
| Design tokens | `--shadow-teal` | Always kebab-case, grouped by property prefix |
| Utility classes | `.t-muted`, `.fw-600`, `.mt-6` | Short prefixed utilities for common one-off needs |
| Component variants | `.card--navy`, `.btn-primary` | Modifier appended with `--` or fused for well-known variants |
| JS hooks | `id="quiz-options"`, `id="chat-messages"` | IDs are reserved for JS targeting; classes handle styling |

---

*Document prepared by TIB Systems Inc. | KABO-TRN-CTL-WPB-001 | WP-01*
