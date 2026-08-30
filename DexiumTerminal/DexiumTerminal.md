# Dexium Terminal Design System

Version: 1.0
Status: Active
Scope: Any product surface built with this system — marketing pages, data/comparison tables, product or spec detail pages, filters, forms, dashboards, admin views. Originally developed for a comparison-platform product, but the system itself is product-agnostic and portable to any application that benefits from a precise, data-dense, developer-tool-grade visual language.
Audience: AI coding agents and human developers implementing UI with Dexium Terminal

---

## 0. How to use this document

This document is the single source of truth for visual and interaction design in any project adopting **Dexium Terminal**. It is written so an AI agent can implement any screen without guessing, regardless of which specific product or domain it is applied to.

Rules for using this spec:

1. If a rule exists in this document, follow it exactly. Do not substitute a "close enough" value.
2. If a component you need is not defined here, do not invent a new visual language. Instead, compose it from existing primitives in **Section 5 (Primitives)** and follow the **Section 12 (Decision Rules for Undefined Cases)** procedure.
3. Never introduce a new color, font, radius, or shadow value that is not listed in Section 2 (Design Tokens). If a new semantic need appears (e.g. a new status), map it to the closest existing semantic token per Section 12.
4. This system has no dark/light mode toggle. Theme is resolved automatically from the user's OS/browser preference (`prefers-color-scheme`). Never render a manual theme switcher control anywhere in the product. See Section 1.3.
5. All numeric, technical, and pricing data must use the monospace font family. This is not optional styling — it is a semantic rule (Section 3.4).
6. When in doubt between "more minimal" and "more decorated," choose more minimal. This system's failure mode is over-decoration, not under-decoration.

---

## 1. Design Philosophy

### 1.1 Concept

**Dexium Terminal** is a developer-tool-grade interface language for products that are dense with data, specs, or comparable facts and are used by a technically literate audience (developers, sysadmins, indie hackers, technical founders, analysts). It was first developed for a services-comparison platform, but nothing about it is specific to that domain — it applies equally to dashboards, admin tools, developer platforms, documentation products, analytics tools, or any interface where clarity and precision matter more than persuasion.

The visual model is closer to **Linear, Vercel dashboard, GitHub, and a terminal/IDE** than to a typical SaaS marketing site. It is not playful, not rounded-and-soft, not gradient-heavy. It is precise, dense-but-legible, and data-first.

Wherever this document uses a comparison-platform example (a pricing table, a VPS spec card, an npm package list) to illustrate a rule, treat it as one illustration of the underlying principle, not a constraint on where the system can be used. Substitute your own project's real content into the same components and rules.

### 1.2 Design principles (in priority order)

1. **Data legibility beats decoration.** Every visual choice must make comparison data (numbers, specs, prices, statuses) easier to scan, not prettier to look at.
2. **Structure encodes meaning.** Borders, dividers, badges, and color exist to communicate real distinctions (best value, deprecated, price changed) — never as pure ornament.
3. **Restraint in color.** One accent color is used deliberately and sparingly. Color is a signal, not a mood.
4. **Flat over dimensional.** Prefer 1px borders over shadows. Prefer solid fills over gradients. Prefer sharp state changes over elaborate animation.
5. **Monospace for data, humanist sans for reading.** The pairing itself is a signature: it should be visually obvious which parts of the UI are "facts" (mono) and which are "narrative" (sans).
6. **No unnecessary chrome.** No decorative icons, no illustrations, no stock imagery, no gradient blobs, no glassmorphism, no skeuomorphism.

### 1.3 Theming policy (mandatory)

- Dexium Terminal supports **two themes: dark and light**. Both are fully specified in Section 2.
- Theme selection is **fully automatic**, driven by the operating system / browser setting via the CSS media feature `prefers-color-scheme`.
- **No manual toggle UI is permitted anywhere in the product**, unless a specific project explicitly overrides this rule for itself. Do not implement a theme switch button, dropdown, settings toggle, or any other manual override control, even if a design brief for a specific page does not mention theming at all. This rule overrides any absence of instruction elsewhere.
- If a future requirement explicitly demands a manual override, that would require an explicit amendment to this document; absent that, treat automatic-only as a hard constraint.
- Implementation default (web): use `@media (prefers-color-scheme: dark)` and `@media (prefers-color-scheme: light)` to swap CSS custom property values at the `:root` level. Do not gate theme on JavaScript-read `localStorage` preferences and do not persist a manually chosen theme, since no manual choice exists.

---

## 2. Design Tokens

All values below are the complete and exclusive palette, type scale, spacing scale, radius scale, and shadow scale for the product. Do not use any color, size, or radius outside these tables.

### 2.1 Color tokens — Dark theme (default when `prefers-color-scheme: dark` or no preference detected)

| Token | Hex | Usage |
|---|---|---|
| `--bg` | `#0B0D0F` | Page background (base surface) |
| `--bg-raised` | `#101317` | Cards, panels, table container, modal surface |
| `--bg-inset` | `#08090B` | Table header row, input fields, code blocks, recessed areas |
| `--bg-overlay` | `rgba(0,0,0,0.6)` | Modal/dialog backdrop |
| `--border` | `#212630` | Default 1px borders, dividers, table row separators |
| `--border-strong` | `#2E3440` | Hover borders, focused card borders, emphasized dividers |
| `--text-primary` | `#EDEFF2` | Headings, primary body text, primary data values |
| `--text-secondary` | `#9BA3AF` | Supporting text, descriptions, secondary labels |
| `--text-tertiary` | `#5C6470` | Placeholder text, metadata, timestamps, disabled text |
| `--accent` | `#00E5A0` | Primary actions, links, active states, focus rings, "best value" highlight |
| `--accent-dim` | `#0A3B2C` | Accent background tint (badges, focus ring glow, selected chip fill) |
| `--accent-text` | `#00E5A0` | Text rendered on top of `--bg`/`--bg-raised` using accent hue (links, highlighted numbers) |
| `--good` | `#00E5A0` | Positive semantic signal (pass, included, winner, improvement) |
| `--good-bg` | `#0A2A20` | Background fill for positive badges/alerts |
| `--bad` | `#FF5C5C` | Negative semantic signal (fail, not included, deprecated, regression) |
| `--bad-bg` | `#2E1414` | Background fill for negative badges/alerts |
| `--warn` | `#FFB454` | Caution semantic signal (price change, partial support, outdated data) |
| `--warn-bg` | `#2E2210` | Background fill for caution badges/alerts |
| `--shadow` | `none` | Dark theme uses no drop shadows by default (see Section 2.4) |

### 2.2 Color tokens — Light theme (when `prefers-color-scheme: light`)

| Token | Hex | Usage |
|---|---|---|
| `--bg` | `#FAFAF9` | Page background (base surface) |
| `--bg-raised` | `#FFFFFF` | Cards, panels, table container, modal surface |
| `--bg-inset` | `#F1F1EF` | Table header row, input fields, code blocks, recessed areas |
| `--bg-overlay` | `rgba(20,21,26,0.4)` | Modal/dialog backdrop |
| `--border` | `#E4E4E1` | Default 1px borders, dividers, table row separators |
| `--border-strong` | `#D3D3CE` | Hover borders, focused card borders, emphasized dividers |
| `--text-primary` | `#14151A` | Headings, primary body text, primary data values |
| `--text-secondary` | `#55585F` | Supporting text, descriptions, secondary labels |
| `--text-tertiary` | `#8A8D93` | Placeholder text, metadata, timestamps, disabled text |
| `--accent` | `#00A876` | Primary actions, links, active states, focus rings, "best value" highlight |
| `--accent-dim` | `#E1F5EC` | Accent background tint (badges, focus ring glow, selected chip fill) |
| `--accent-text` | `#00875C` | Text rendered on top of `--bg`/`--bg-raised` using accent hue |
| `--good` | `#00875C` | Positive semantic signal |
| `--good-bg` | `#E7F6EE` | Background fill for positive badges/alerts |
| `--bad` | `#D3402D` | Negative semantic signal |
| `--bad-bg` | `#FBEAE7` | Background fill for negative badges/alerts |
| `--warn` | `#B4700A` | Caution semantic signal |
| `--warn-bg` | `#FBF0DD` | Background fill for caution badges/alerts |
| `--shadow` | `0 1px 2px rgba(20,21,26,0.04)` | Light theme uses one hairline shadow value only (see Section 2.4) |

### 2.3 Semantic color usage rules

- `--accent` is reserved for: primary CTA buttons, active nav/tab indicators, links, focus outlines, checked checkbox/radio fill, "best value" row indicators, progress indicators. Never use `--accent` purely decoratively (e.g. never as a random card background).
- `--good` / `--bad` / `--warn` are reserved exclusively for **comparison and status semantics**: pass/fail checks, price-change alerts, deprecation notices, "winner" designations, SLA/uptime thresholds. Do not reuse these hues for anything unrelated to a judgment about data.
- `--good` and `--accent` intentionally share the same hex value in both themes. This is deliberate: "the accent color" and "the positive/winning signal" are the same concept in this system. Do not diverge them.
- Never use pure red/green/orange outside the specified `--bad` / `--good` / `--warn` hex values. Do not use browser-default `red`, `green`, `orange` keywords.
- Text on colored backgrounds: when text sits on `--good-bg`, `--bad-bg`, or `--warn-bg`, use the corresponding `--good`, `--bad`, `--warn` token as the text color (not `--text-primary`).

### 2.4 Elevation / shadow policy

This system is **flat-first**. Elevation is communicated primarily through background layering (`--bg` → `--bg-raised` → `--bg-inset`) and 1px borders, not through shadows.

- Dark theme: `--shadow` is `none`. Do not add drop shadows to cards, dropdowns, or modals in dark theme. Separation comes entirely from background contrast and border color.
- Light theme: exactly one shadow value is permitted, `--shadow` (`0 1px 2px rgba(20,21,26,0.04)`), used only on: raised cards on hover, open dropdown/popover panels, and modal surfaces. Do not create additional shadow depths, do not use multi-layer shadows, do not use colored shadows.
- Modals/dialogs in both themes are distinguished from the page primarily by the `--bg-overlay` backdrop plus a `--border` outline on the modal surface, not by heavy shadow.

---

## 3. Typography

### 3.1 Font families

| Role | Family | Fallback stack |
|---|---|---|
| UI / body / headings | Inter | `-apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif` |
| Data / numeric / code | JetBrains Mono | `"SF Mono", Consolas, "Liberation Mono", monospace` |

Load weights: Inter 400, 500, 600, 700, 800. JetBrains Mono 400, 500, 600, 700. Do not substitute other geometric or grotesque sans fonts (e.g. do not use Helvetica, Arial, Roboto, or Poppins as a replacement for Inter) and do not substitute other monospace fonts (e.g. do not use Fira Code, Roboto Mono, or Courier) for JetBrains Mono. If Inter or JetBrains Mono cannot be loaded, fall back only to the stacks listed above, in order.

### 3.2 Type scale

| Token | Size | Weight | Letter-spacing | Line-height | Usage |
|---|---|---|---|---|---|
| `display` | 52px | 800 | -0.03em | 1.05 | Hero headline (H1), one per page maximum |
| `h1` | 44px | 800 | -0.03em | 1.1 | Page-level heading when no hero is present |
| `h2` | 30px | 700 | -0.02em | 1.2 | Major section headings |
| `h3` | 21px | 600 | -0.01em | 1.3 | Card titles, subsection headings, product names in spec cards |
| `h4` | 16px | 700 | -0.01em | 1.4 | Minor headings, filter group titles (uppercase, see 3.3) |
| `body-lg` | 17px | 400 | -0.01em | 1.6 | Lead paragraphs under hero/page headings |
| `body` | 15px | 400 | normal | 1.6 | Default body text, table cell text, descriptions |
| `body-sm` | 13.5px | 400 | normal | 1.5 | Secondary descriptions, card metadata, dense table cells |
| `caption` | 12px | 500 | 0.02em | 1.4 | Timestamps, helper text, form field hints |
| `eyebrow` | 12px | 700 | 0.08em | 1.4 | Uppercase labels above headings, section kickers |
| `data-lg` | 26px | 700 | normal | 1.2 | Mono. Large price/number display (pricing cards) |
| `data` | 13.5–14px | 500–600 | normal | 1.5 | Mono. Table numeric cells, spec values, inline stats |
| `data-sm` | 11–12px | 600 | 0.02em | 1.4 | Mono. Badges, tags, small technical labels |

### 3.3 Typography usage rules

- **Headings** (`display`, `h1`, `h2`, `h3`, `h4`) always use Inter, weight per table, `--text-primary` color, negative letter-spacing as specified. Never render a heading in the monospace font.
- **Eyebrow labels** (small uppercase kickers above a heading, filter group titles, table column headers) use Inter at the `eyebrow` or `h4` size, `text-transform: uppercase`, `letter-spacing: 0.06em–0.08em`, color `--text-tertiary` or `--text-secondary`. Table column headers specifically use `--text-tertiary`.
- **Any of the following must be rendered in JetBrains Mono, regardless of surrounding context**: prices/currency values, percentages, byte/storage/bandwidth quantities (GB, TB, Mbps), counts (requests/sec, users, downloads), version numbers (semver strings, package versions), SLA/uptime numbers, benchmark results, dates rendered as data (e.g. in changelogs or "last verified" stamps), API rate limits, and any table cell whose column represents a quantitative spec. This is a semantic rule, not a stylistic suggestion — an AI implementing a new table or card must apply monospace to every such value even if this document does not explicitly name that exact field.
- Body copy, marketing/narrative sentences, button labels, navigation labels, and badge/status **words** (e.g. the word "Deprecated") use Inter, not monospace. Monospace is for **values**, not for status labels — see Section 6.5 for the one exception (mono used for the badge icon/glyph only).
- Line length for body text should not exceed ~70ch in prose contexts (hero lead paragraphs, descriptions). Not a hard render constraint, but should guide max-width choices on text containers.
- Never use justified text. Left-align all text except numeric table cells, which may be right-aligned or left-aligned consistently per table (see 6.3).

### 3.4 Numeric/data typography — non-negotiable rule

Any UI element whose primary content is a comparable, quantitative fact about a product/service (price, spec, rating expressed as a number, percentage, version) must use the `data`, `data-lg`, or `data-sm` scale in JetBrains Mono and, where the value represents a "winning" or best-in-class figure in a comparison context, must additionally use `--good` as its text color and `font-weight: 600` (see Section 6.3, "best value" cell rule). This dual signal (font + color) is the core visual mechanic of the entire comparison product and must be applied consistently across every new page or component.

---

## 4. Layout & Spacing

### 4.1 Spacing scale

Use an 4px-based scale. Only use values from this list for padding, margin, and gap:

`4, 8, 12, 14, 16, 18, 20, 24, 28, 32, 40, 48, 56, 64, 72, 88, 120`

(in px). Do not use arbitrary spacing values (e.g. `15px`, `22px`, `50px`) outside this list. When unsure which to pick, prefer the nearest value on the list that is equal to or slightly larger than what feels visually correct — this system errs toward slightly generous whitespace between major sections and tight whitespace within a component.

### 4.2 Grid & containers

- **Max content width**: `1180px`, centered, with `28px` horizontal padding on the container (`16px` on mobile, see 4.4).
- **Page vertical rhythm**: top padding under sticky header `56px`; bottom page padding `120px`; spacing between major `<section>` blocks `72px`.
- **Section internal header**: every major section has a `section-head` consisting of an uppercase `eyebrow`/`h4`-styled label on the left and an optional mono metadata string on the right, separated from content below by `20px` margin and sitting on a `1px solid --border` bottom rule with `12px` padding-bottom before the rule.
- **Two-column filter+content layout** (used for comparison browsing pages): left filter column fixed at `240px`, right content column `1fr`, gap `24px`. Filter column is `position: sticky` with `top` equal to the sticky top-bar height (`~76px`; recompute if top bar height changes). Collapses to single column (filter panel above content, no longer sticky) below `860px` viewport width.
- **Card grids**: default to a 3-column grid (`repeat(3, 1fr)`, gap `16px`) for spec/product cards on desktop. Reduce to 2 columns below `~1024px`, 1 column below `~640px`.

### 4.3 Radius scale

| Token | Value | Usage |
|---|---|---|
| `--radius-sm` | 4px | Badges, small chips, input fields, table logo squares |
| `--radius-md` | 6px | Buttons, filter panel, form fields, dropdown menus |
| `--radius-lg` | 10px | Cards, table containers, modals, panels |

Never use radius values outside these three. Never use fully circular (`border-radius: 50%` / pill-shaped `9999px`) elements **except** for: status dots (Section 6.1), and avatar/user profile images if introduced later. Buttons, inputs, badges, and cards must always use the sharp/sm/md/lg scale above — never pill-shaped buttons or pill-shaped badges. This is a deliberate departure from typical SaaS design and is core to the "dev tool" character of the system.

### 4.4 Responsive breakpoints

| Breakpoint | Width | Behavior |
|---|---|---|
| Desktop | ≥ 1024px | Full layout as specified: 3-col cards, 240px sticky filter sidebar |
| Tablet | 640–1023px | 2-col card grids; filter sidebar becomes non-sticky, stacked above table/content; horizontal container padding reduces to `20px` |
| Mobile | < 640px | 1-col card grids; hero `display`/`h1` size reduces to `36px`; horizontal container padding reduces to `16px`; comparison tables scroll horizontally within their `table-card` container rather than reflowing columns (see 6.3) |

At all breakpoints, the top bar remains sticky and single-row. If top bar content would overflow on very narrow screens, deprioritize/hide secondary nav items before shrinking the logo or search affordance.

---

## 5. Primitives

These are the atomic visual units every higher-level component is built from. When a new component is needed and not explicitly defined in Section 6, compose it from these primitives per Section 12.

### 5.1 Surfaces

- **Base** (`--bg`): the page canvas.
- **Raised** (`--bg-raised`): one level up — cards, panels, table containers, modal bodies, popovers. Always paired with a `1px solid --border` outline. Radius `--radius-lg` unless the raised surface is a small control (then `--radius-md`).
- **Inset** (`--bg-inset`): one level down / recessed — table header rows, input field backgrounds, code blocks, the sticky top bar is NOT inset (it uses `--bg` to blend with the page, separated only by a bottom border).

### 5.2 Borders

- Default border: `1px solid var(--border)`.
- Emphasized/hover border: `1px solid var(--border-strong)`.
- Focus border: `1px solid var(--accent)` plus a `0 0 0 3px var(--accent-dim)` box-shadow ring (applies in both themes; this is the one shadow permitted in dark theme, reserved exclusively for focus rings).
- Dashed border (`1px dashed var(--border)`) is permitted only for internal separators inside a card's spec list (Section 6.4), never for outer card/container edges.

### 5.3 Iconography

- No decorative icon set is bundled by default. Where icons are needed for functional purposes (chevrons, close buttons, external-link indicators, checkmarks/crosses in badges), use simple geometric line icons at `16px` or `20px` box size, `1.5px` stroke, color inherited from surrounding text color token.
- Checkmark (✓) and cross (✕) glyphs used in badges (Section 6.5) may be rendered as literal Unicode characters in the mono font at `data-sm` size rather than SVG icons — both are acceptable; be consistent within a single page.
- Do not use filled/solid icon styles, do not use multi-color icons, do not use illustrative or 3D icons, do not use emoji as functional UI icons (emoji are acceptable only inside user-generated or informal content areas, never in chrome/controls).

### 5.4 Motion

- Standard transition timing: `150ms` for micro-interactions (hover, focus, button press), `200ms` for layout-affecting transitions (panel open/close, dropdown reveal).
- Easing: `cubic-bezier(0.4, 0, 0.2, 1)` for all transitions (standard "ease" curve). Do not use bounce, elastic, or spring easings anywhere in the system.
- Permitted animated properties: `background-color`, `border-color`, `color`, `opacity`, `transform` (limited to `translateY` of 1–2px on hover for cards/buttons, and rotation of chevrons on expand/collapse).
- Do not animate `box-shadow` size/blur (shadows are near-static per Section 2.4), do not use parallax, do not use auto-playing decorative animations, do not use confetti/celebratory animation, do not use skeleton shimmer beyond a simple opacity pulse for loading states (Section 9.4).
- Respect `prefers-reduced-motion: reduce`: when set, disable all transform/translate hover effects and reduce all transition durations to `0ms` (state changes should be instant, not merely faster).

---

## 6. Core Components

### 6.1 Top bar

- Fixed/sticky at `top: 0`, `z-index: 50`.
- Height driven by content: `14px` vertical padding, `28px` horizontal padding (matches container padding).
- Background: `--bg` (flush with page). Bottom border: `1px solid --border`. No shadow.
- Left: brand lockup — a small `8px` square status-dot in `--accent` (this is the one exception to "no circular/pill shapes" being about buttons/badges; the dot itself is a small square with `--radius-sm`-equivalent 2px corner, not circular — keep it square to match the "sharp" system, do not round it into a circle) followed by the wordmark in `h4`-equivalent weight (700) at `15px`, tight letter-spacing (`-0.01em`), plus an optional small mono tag chip (`11px` mono, `1px solid --border`, `2px 6px` padding, `--radius-sm`) for contextual labels like a section name.
- Right: primary navigation and/or search entry point and/or primary account action. **Do not place a theme toggle here or anywhere else** (Section 1.3).
- On scroll, the top bar does not change background or gain a shadow — it remains visually flat and only the bottom border separates it from scrolled content.

### 6.2 Buttons

Three variants, two sizes. All buttons share: `border-radius: --radius-md`, `font-weight: 600`, `display: inline-flex`, `align-items: center`, `gap: 8px`, transition `150ms` per Section 5.4.

| Variant | Background | Text | Border | Hover behavior |
|---|---|---|---|---|
| Primary | `--accent` | `#04140F` (dark theme) / `#FFFFFF` (light theme) | none | `filter: brightness(1.08)` + `translateY(-1px)` |
| Secondary | `--bg-raised` | `--text-primary` | `1px solid --border-strong` | border becomes `--text-tertiary`-equivalent contrast (darken/lighten border only, no fill change) |
| Ghost | transparent | `--text-secondary` | none | background becomes `--bg-raised`, text becomes `--text-primary` |

Sizes:
- Default: `11px 20px` padding, `14px` font size.
- Small (`btn-sm`): `7px 12px` padding, `12.5px` font size — used inside cards and dense table rows.

Rules:
- Exactly one Primary button should be visually dominant per screen section/hero; do not place two Primary buttons directly adjacent to each other. Pair Primary with Secondary or Ghost for secondary actions.
- Disabled state: `opacity: 0.45`, `cursor: not-allowed`, no hover transform, pointer-events disabled.
- Destructive actions (delete, remove comparison, etc.) use the Secondary visual shape but swap text/border color to `--bad`; do not introduce a fourth "danger" filled-background button variant — keep destructive actions visually quiet (outline-only) so they are never the most prominent element on screen.

### 6.3 Comparison table (core component)

This is the single most important component in the product. Structure:

- Outer wrapper `table-card`: `--bg-raised` background, `1px solid --border`, `--radius-lg`, `overflow: hidden`.
- `<thead>` cells: background `--bg-inset`, text `eyebrow`-style (uppercase, `11px`, `700` weight, `0.06em` letter-spacing, `--text-tertiary`), `14px 16px` padding, bottom border `1px solid --border`, `position: sticky` with `top` matching the top bar height so headers remain visible while scrolling a long table.
- `<tbody>` cells: `16px` padding, `body-sm` (13.5px) size, bottom border `1px solid --border`, last row has no bottom border.
- Row hover: background shifts to `--bg-inset`, transition `120ms`.
- **First column convention**: the leading/identity column (provider, package, service name) renders as a `service-cell`: a `28px × 28px` logo/initials square (`--bg-inset` background, `1px solid --border`, `--radius-sm`, centered bold initials at `12px` in `--text-secondary`) plus a stacked name block (`service-name`: `600` weight, `13.5px`, `--text-primary`; optional `service-sub`: `11.5px`, `--text-tertiary`, e.g. plan/tier name).
- **Numeric/spec columns**: always rendered in mono (`data` scale, Section 3.4). Choose one alignment (left or right) per table and apply consistently to all numeric columns in that table; do not mix alignment within a single table.
- **"Best value" row**: exactly one row per table may be marked as the standout/winning option using the `row-best` treatment: a `3px` solid `--accent` bar on the left inner edge of the first cell (implemented as a pseudo-element or inset box-shadow, not by adding a visible 4th table column), and the specific winning metric's cell(s) additionally styled per the "best value cell" rule below. Do not mark more than one row as best per table — if several rows tie, pick the single most relevant metric to break the tie, or omit the best-row treatment entirely rather than doubling it.
- **"Best value" cell** (independent of whether the row is the overall winner): the single best numeric value in a comparison column (e.g. lowest price, highest uptime) is styled with `color: --good` and `font-weight: 600`, keeping the same mono font/size as sibling cells in that column. Do not apply this to every "good-ish" value — only the single best cell per column, per table, so the signal stays meaningful.
- **Boolean/inclusion columns** (e.g. "Free tier", "IPv6 support"): render as a badge (Section 6.5) using `badge-good`/`check-good` for true and `badge-bad`/`check-bad` for false — never a bare "Yes"/"No" text string without the badge treatment, since the badge's color is what makes the table scannable at a glance.
- **Mobile behavior**: below `640px`, do not reflow columns into stacked cards by default — instead make the `table-card` horizontally scrollable (`overflow-x: auto`) with the first (identity) column optionally sticky to the left edge if the table library/framework in use supports sticky columns; if it does not, a plain horizontal scroll is acceptable. Only convert to a stacked-card layout per row if a specific page explicitly requires it; this is a deviation from default and should be a deliberate choice, not the automatic mobile fallback.

### 6.4 Spec cards

Used for single-product detail summaries (e.g. an email API's card, a VPS plan's card) in card-grid contexts.

- Container: `--bg-raised`, `1px solid --border`, `--radius-lg`, `20px` padding. Hover: border becomes `--border-strong`, `transform: translateY(-2px)`, transition `150ms`.
- Header row (`spec-card-head`): logo square (`36px × 36px`, same visual treatment as the table's service logo but larger, `14px` initials) on the left, an optional status/category badge (Section 6.5) on the right, `justify-content: space-between`, `16px` bottom margin.
- Title block: product name in `h3` (21px/600), immediately followed by a `tagline` in `12.5px`, `--text-tertiary`, `16px` bottom margin before the price.
- Price block (`price-tag`): mono `amount` at `data-lg` (26px/700) immediately followed by a `period` string (e.g. "/ 3k emails/mo") at `12.5px`, `--text-tertiary`, baseline-aligned, `16px` bottom margin.
- Spec list (`spec-list`): vertical stack of `spec-row` items, each a flex row with a `k` (key) label in `--text-tertiary` body-sm on the left and a `v` (value) in mono `data` weight `500` on the right, separated by a `1px dashed --border` bottom border (last item has none), `9px` vertical padding rhythm, `18px` bottom margin before the card's closing action.
- Closing action: typically a full-width Secondary small button ("View full spec" or similar), `width: 100%`, centered content.
- Card grid spacing and breakpoints per Section 4.2.

### 6.5 Badges

- Base shape: `--radius-sm` (NOT pill-shaped — see Section 4.3), `3px 8px` padding, mono font, `11px` size, `600` weight, `uppercase`, `0.02em` letter-spacing, `display: inline-flex`, `gap: 5px` (for icon+label badges).
- Variants map directly to semantic tokens:
  - `badge-good`: background `--good-bg`, text `--good`. Use for: pass, included, winner, "best value," positive verification.
  - `badge-bad`: background `--bad-bg`, text `--bad`. Use for: fail, not included, deprecated, discontinued.
  - `badge-warn`: background `--warn-bg`, text `--warn`. Use for: price change, partial/limited support, stale/unverified data, beta status.
  - `badge-neutral`: background `--bg-inset`, text `--text-secondary`, border `1px solid --border`. Use for: informational, non-judgmental tags (e.g. "Free tier" as a category label distinct from a pass/fail check, "Popular").
  - `badge-accent`: background `--accent-dim`, text `--accent-text`. Use for: editorial/curatorial calls that are not pass/fail judgments — "Editor's pick," "New," "Recommended."
- Never invent a new badge color combination outside these five variants. If a new status concept appears, map it to the nearest of good/bad/warn/neutral/accent per Section 12 rather than introducing a sixth color.
- Optional leading glyph: literal `✓` or `✕` characters (Section 5.3) may prefix the badge label text for boolean-style badges; omit the glyph for non-boolean badges (neutral/accent variants normally have no glyph).

### 6.6 Filter panel

- Container: `--bg-raised`, `1px solid --border`, `--radius-lg`, `18px` padding, `position: sticky` (desktop only, see 4.2/4.4).
- Structure: a vertical stack of `filter-group` blocks, each with a `22px` bottom margin (last group has none).
- Group heading: `11px`, uppercase, `0.06em` letter-spacing, `700` weight, `--text-tertiary`, `10px` bottom margin.
- Checkbox filter option: a flex row, `space-between`, `6px` vertical padding, `13.5px` label in `--text-secondary` (hovers to `--text-primary`), with a right-aligned mono `count` in `11px`/`--text-tertiary` showing the number of matching results. Checkbox input uses `accent-color: var(--accent)` rather than a custom-built checkbox graphic, unless a specific page's framework requires a custom control — if so, the custom control must still resolve to an `--accent`-filled check state visually equivalent to the native `accent-color` rendering.
- Range filter: a native or custom range/slider input with `accent-color: var(--accent)` (or accent-colored track/thumb if custom-built), paired with a min/max label row underneath in `caption`-equivalent mono/uppercase styling consistent with `type-sample-6` from the demo (12px, `--text-tertiary`, uppercase, `0.08em`).
- The filter panel never uses `--bg-inset`; it is a raised surface, not a recessed one, because it is an interactive control panel, not passive/dimmed content.

### 6.7 Form fields

- Field wrapper: vertical flex, `6px` gap between label and input.
- Label: `12px`, mono font, `--text-tertiary`, typically uppercase or sentence case consistent with nearby labels on the same page (pick one convention per page and apply it to every field label on that page).
- Input/select: `--bg-inset` background, `1px solid --border`, `--radius-sm`, `10px 12px` padding, `13.5px` Inter (not mono — user-entered/selected values in inputs use the UI font; only the *displayed, already-resolved* data elsewhere in the product uses mono, per Section 3.4's distinction between "data being read" and "data being entered").
- Focus state: border becomes `--accent`, plus `0 0 0 3px --accent-dim` box-shadow ring (Section 5.2).
- Placeholder text color: `--text-tertiary`.
- Multiple fields on one row: `flex` with `16px` gap, each field `flex: 1`, `min-width: 180px`, wrapping to new lines below that width.

### 6.8 Alerts / inline notices

- Container: flex row, `12px` gap between icon and text, `14px 16px` padding, `--radius-md`, `1px solid` border in the semantic color, `13.5px` text in `--text-primary` (not the semantic color — only the border, background, and icon carry the semantic color; body text of the alert stays neutral for readability).
- Background: the corresponding `-bg` semantic token (`--good-bg`, `--bad-bg`, `--warn-bg`).
- Icon: a bold mono glyph (`✓`, `!`, `✕`) in the semantic foreground color (`--good`, `--warn`, `--bad`), `flex-shrink: 0`, vertically aligned with the first line of text.
- Stack multiple alerts with `12px` gap between them; do not nest alerts inside cards that already carry semantic color (e.g. do not put a `badge-bad` and an `alert-bad` in the same card unless they describe genuinely distinct facts).
- There is no "info" alert variant with a distinct hue — for neutral/informational notices, use the `warn` styling only if genuine caution is warranted, or a plain `--bg-inset` panel with `--text-secondary` text and no colored border if the notice is purely informational and carries no judgment. Do not invent a blue "info" token.

### 6.9 Section headers (`section-head`)

- Flex row, `baseline` alignment, `space-between`, `20px` bottom margin, `12px` bottom padding, `1px solid --border` bottom border.
- Left: `h2`-adjacent eyebrow label — in practice rendered smaller than a true H2 (`13px`, `700`, uppercase, `0.08em`, `--text-secondary`) when used as a section kicker above dense UI (tables, card grids); use the full `h2` token (Section 3.2) only for narrative/marketing page sections, not dense app-like sections. Both are acceptable but must be chosen consistently: dense/app-like pages (comparison browser, dashboard) use the small eyebrow-style section head; narrative pages (homepage, about, methodology) use full `h2`.
- Right (optional): a small mono metadata string (`12px`, `--text-tertiary`) — e.g. a result count, a "last updated" stamp, or a category tag — right-aligned, baseline with the left label.

---

## 7. Iconless Logo / Service Identity Convention

In products that list many third-party entities (services, packages, vendors, companies) without reliably available brand assets, the default identity treatment is a **monogram square**, not a fetched brand logo, unless real logo assets are explicitly integrated:

- Square background `--bg-inset`, `1px solid --border`, radius `--radius-sm`.
- Centered 1–3 letter uppercase initials, `700` weight, Inter (not mono — initials are a label, not data), color `--text-secondary`.
- Sizes: `28px` in table rows, `36px` in spec cards, `44px` if used in a larger featured/hero product callout.
- If real logo image assets are available and approved for use, they replace the monogram inside the same square container (same size/border/radius), object-fit: contain, with a small internal padding (~15% of the square) so logos don't touch the edges. Do not stretch or crop logos edge-to-edge.

---

## 8. Voice & Content Rules (applies to all UI copy)

Even though this is primarily a visual spec, copy is part of the design system because mismatched tone breaks the "dev tool" character as much as wrong colors would.

- Write from the user's point of view, describing what they control: "Compare pricing," not "Pricing comparison engine."
- Use active voice and plain verbs. Buttons say what they do: "Compare now," "Add to list," "View full spec" — not "Submit" or "Learn more" as generic filler.
- No marketing superlatives in UI chrome ("blazing fast," "revolutionary," "best-in-class" as unearned claims). Superlatives are only acceptable when they are the literal output of a data comparison (e.g. a badge that says "Best value" because the data actually shows it).
- Numbers and technical facts are stated plainly, with units, never rounded misleadingly (state "99.95% SLA," not "~100% uptime").
- Empty states describe what's missing and what to do next in the interface's own voice: "No providers match these filters. Try widening your price range." — not an apologetic tone ("Oops! Nothing here 😅").
- Error messages state what happened and how to fix it, without blame or unnecessary apology: "Couldn't load pricing for this provider. Refresh to try again."
- Status labels inside badges are short nouns/adjectives, sentence case is not required since badges are uppercase by style (Section 6.5), but the underlying copy should still be a plain, specific word: "Deprecated," "Free tier," "Price increase" — not vague terms like "Warning" or "Notice" alone.

---

## 9. States (must be defined for every interactive component)

Every interactive component built in this system must explicitly account for the following states. When implementing any new component, do not stop at the default/rest state.

### 9.1 Hover
- Buttons: per Section 6.2 per-variant rules.
- Table rows: background → `--bg-inset`.
- Cards: border → `--border-strong`; optionally `translateY(-2px)` for spec cards specifically (not for table rows or filter options).
- Links/inline text actions: color shifts from `--text-secondary`/`--text-primary` to `--accent-text`; underline optional but recommended for inline links within body copy (not for nav links).

### 9.2 Focus (keyboard)
- Every focusable element must show a visible focus ring: `1px solid --accent` border/outline plus `0 0 0 3px --accent-dim` box-shadow, per Section 5.2.
- Never remove focus outlines (`outline: none`) without supplying this equivalent replacement.
- Focus order must follow visual/DOM order; do not reorder tab sequence to "fix" a layout without also fixing the underlying DOM order.

### 9.3 Active / pressed
- Buttons: on `:active`, remove the hover `translateY` lift (return to `translateY(0)`) and reduce `filter: brightness` slightly below the hover value (e.g. `brightness(0.96)` on Primary) to give tactile press feedback.

### 9.4 Loading
- Inline loading (e.g. a table refreshing filtered results): reduce content opacity to `0.5` and disable pointer events on the affected region; do not replace with a full-page spinner for partial updates.
- Skeleton loading (initial page/table load): flat rectangular placeholders using `--bg-inset` background with a subtle opacity pulse animation (`0.6` ↔ `1`, `1.2s` ease-in-out infinite) — same radius as the content they replace (e.g. `--radius-sm` for table cells, `--radius-lg` for cards). Do not use shimmer/gradient-sweep skeletons.
- Button loading state: replace label with a small inline spinner (simple rotating arc, `--accent` on Primary buttons, current text color on Secondary/Ghost) and disable the button; keep button width stable (do not let it shrink to fit just the spinner).

### 9.5 Disabled
- `opacity: 0.45`, `cursor: not-allowed`, all hover/focus/active visual changes suppressed.
- Disabled form fields additionally get `background: --bg` (not `--bg-inset`) to visually flatten them further against the inset norm.

### 9.6 Empty
- Empty states (no results, no data yet) render inside the same container type the content would have used (e.g. an empty comparison table still renders the `table-card` wrapper) with a centered message block: a short bold statement (`h4` scale) plus a `body-sm` supporting line and, where actionable, a Secondary button to resolve it (e.g. "Clear filters"). Follow the voice rules in Section 8.

### 9.7 Error
- Inline field errors: border becomes `--bad`, a `caption`-scale message in `--bad` appears below the field with `4px` top margin.
- Component/section-level errors (e.g. a table failed to load data): use the `alert-bad` treatment (Section 6.8) in place of the content, with a retry action (Secondary button) if applicable.

---

## 10. Accessibility Requirements

- Color is never the sole carrier of meaning: every `good`/`bad`/`warn` semantic signal must be paired with a text label or icon glyph (badges already satisfy this by combining color with a word and/or ✓/✕ glyph — never ship a bare colored dot with no label as a status indicator).
- Minimum contrast: body text against its background must meet WCAG AA (4.5:1) in both themes; the token values in Section 2 are chosen to satisfy this on their intended background pairings (e.g. `--text-secondary` on `--bg`/`--bg-raised`, not on `--bg-inset` if that combination fails contrast — verify before reusing a text token on a non-standard background).
- All interactive elements must be reachable and operable via keyboard, with visible focus states per Section 9.2.
- Respect `prefers-reduced-motion` per Section 5.4.
- Use semantic HTML elements (`<table>`, `<thead>`, `<button>`, `<label for>`, etc.) as the underlying structure regardless of visual styling; the visual system in this document must be implemented as a styling layer on top of correct semantics, not as a replacement for them.

---

## 11. Explicit Non-Goals / Anti-Patterns

To prevent drift toward generic "AI-generated SaaS" defaults, the following are explicitly forbidden anywhere in the product unless this document is formally amended:

- No warm cream backgrounds with terracotta/clay accents.
- No pill-shaped buttons or pill-shaped badges.
- No gradients on backgrounds, buttons, text, or borders (solid fills only).
- No glassmorphism (backdrop-blur translucent panels), no neumorphism (soft embossed shadows).
- No large multi-layer drop shadows or glow effects around cards/buttons.
- No stock photography, illustration sets, or decorative hero graphics; the hero is copy- and data-led (real comparison tables/snippets), not imagery-led.
- No bouncy/elastic animation curves.
- No manual dark/light toggle control (Section 1.3).
- No serif display typeface anywhere (this system is sans + mono only).
- No more than one Primary button visually competing for attention within the same view.
- No introducing a new hue for a new "status" without mapping it to Section 12's decision rule first.

---

## 12. Decision Rules for Undefined Cases

When a screen or component is requested that this document does not explicitly cover, resolve it in this order:

1. **Identify the closest existing component** in Section 6 by function (not by visual similarity) — e.g. a new "compare npm packages by weekly downloads" table is still the Comparison Table component (6.3), even though its content differs from the VPS example.
2. **Reuse tokens, not values.** Pull colors, type, spacing, and radius exclusively from Sections 2–4. Never hand-pick a new hex/pixel value "because it looks right" for one screen.
3. **Map new semantic states to existing semantics.** If a new status concept appears (e.g. "actively maintained" vs "unmaintained" for an npm package), decide which existing semantic bucket it belongs to (`good`/`bad`/`warn`/`neutral`/`accent`) based on whether it represents a positive, negative, cautionary, informational, or editorial signal — do not create a sixth color.
4. **Apply the data-typography rule automatically.** Any newly introduced field that is a comparable quantitative fact must use mono typography and, if a "best in column" designation makes sense for it, the best-value color/weight treatment from Section 6.3 — even if that specific field (e.g. "GitHub stars," "bundle size in KB," "last commit date") is not named anywhere in this document.
5. **Default to the plainest composition.** If two valid layouts could satisfy a new request (e.g. cards vs. table for a given dataset), prefer whichever is denser and more scannable, consistent with Section 1.2's priority on data legibility — tabular/list-like data defaults to the Comparison Table; single-entity deep-dives default to Spec Cards or a full detail page built from the same primitives (raised surface, section heads, spec-list rows).
6. **If genuinely novel** (no analogous component exists even functionally), construct it strictly from Section 5 primitives (surfaces, borders, spacing, radius scale, motion rules) and document the new pattern's rationale in a comment or accompanying note so it can be formally added to this spec later — do not silently introduce a one-off visual style that other pages won't be able to replicate.

---

## 13. Reference Implementation

A working HTML reference demonstrating every component in this document (hero, type scale, color tokens, buttons, badges, a comparison table with filter sidebar, spec cards, form fields, alerts) exists as `dexium-terminal-design-system.html`. Its content uses a services-comparison example purely as one illustration of the system — the same components apply directly to any other domain (dashboards, admin panels, developer tools, etc.) by swapping in that project's real content. That file is illustrative; **this markdown document is authoritative**. Where the two ever disagree due to future edits of one but not the other, this document governs, and the reference HTML should be updated to match — not the reverse. Note that the reference HTML predates the no-manual-toggle rule in Section 1.3 and included a manual dark/light switch for demonstration purposes only; any new implementation must omit that control per Section 1.3.
