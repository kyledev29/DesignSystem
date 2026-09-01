# Dexium Terminal Design System

Version: 1.0
Status: Active
Scope: Any product surface built with this system — marketing pages, data/comparison tables, product or spec detail pages, filters, forms, dashboards, admin views. Originally developed for a comparison-platform product, but the system itself is product-agnostic and portable to any application that benefits from a precise, data-dense, developer-tool-grade visual language.
Audience: AI coding agents and human developers implementing UI with Dexium Terminal

---

## 0. How to use this document

This document is the single source of truth for visual and interaction design in any project adopting **Dexium Terminal**. It is written so an AI agent can implement any screen without guessing, regardless of which specific product or domain it is applied to.

This document contains two layers, and both are binding, but they answer different questions:

- **Visual intent** (Sections 1–6, 8–12): what the interface should look and feel like, and why — the design philosophy, tokens, typography, layout values, primitives, component descriptions, voice, states, accessibility, and anti-patterns.
- **Implementation contract** (Section 7, plus the specific rendering-technique language embedded in Section 6's component rules): exactly *how* to render that intent when more than one technique could plausibly produce something that matches the visual description — which CSS layout mechanism to use, how columns are sized, what happens when content overflows, what gets sacrificed first on a small screen, which component owns a shared piece of chrome, and how to resolve it when two rules seem to disagree.

Two independent implementations that both satisfy the visual-intent layer can still look and behave differently from each other if the implementation-contract layer is left to individual judgment — that gap is what Section 7 exists to close. Read Section 7 before implementing any layout, table, grid, or responsive behavior; treat "it looks right" as necessary but not sufficient — it must also be built the specified way, using the specified default technique, so that another agent building the same screen from this same document converges on the same markup and the same computed values, not merely a similar impression.

Rules for using this spec:

1. If a rule exists in this document, follow it exactly. Do not substitute a "close enough" value. This includes implementation-contract rules (Section 7) as much as visual-intent rules — a technically-different implementation that "looks the same" is still a spec violation if Section 7 named a specific required technique.
2. If a component you need is not defined here, do not invent a new visual language. Instead, compose it from existing primitives in **Section 5 (Primitives)**, apply the general rendering defaults in **Section 7 (Implementation Contracts)**, and follow the **Section 13 (Decision Rules for Undefined Cases)** procedure.
3. Never introduce a new color, font, radius, or shadow value that is not listed in Section 2 (Design Tokens). If a new semantic need appears (e.g. a new status), map it to the closest existing semantic token per Section 13.
4. This system has no dark/light mode toggle. Theme is resolved automatically from the user's OS/browser preference (`prefers-color-scheme`). Never render a manual theme switcher control anywhere in the product. See Section 1.3.
5. All numeric, technical, and pricing data must use the monospace font family. This is not optional styling — it is a semantic rule (Section 3.4).
6. When in doubt between "more minimal" and "more decorated," choose more minimal. This system's failure mode is over-decoration, not under-decoration.
7. When in doubt between two ways of *building* something that would look identical, prefer whichever technique Section 7 names explicitly; if neither is named, prefer whichever technique produces the same computed output regardless of container-measurement timing, font-loading order, or browser quirks (fixed values over measured/inferred ones) — determinism beats elegance when the two trade off.

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
- **Two-column filter+content layout** (used for comparison browsing pages): left filter column fixed at `240px`, right content column `1fr`, gap `24px`. Filter column is `position: sticky` with `top: 76px`. This value is not approximate and is not "recomputed" per page: `76px` is derived from the top bar's fixed padding (`14px` × 2) plus its content line-height at the specified type size (Section 6.1), and every implementation must use this exact value rather than measuring the rendered top bar at runtime. If a future project genuinely changes the top bar's vertical padding, this document must be amended with the new computed value — implementers never derive their own. Collapses to single column (filter panel above content, no longer sticky) below `860px` viewport width; `860px` is an exact breakpoint, not an approximation.
- **Card grids**: default to a 3-column grid (`repeat(3, 1fr)`, gap `16px`) for spec/product cards on desktop. Reduce to 2 columns below `1024px`, 1 column below `640px`. These two breakpoints are exact and match the Desktop/Tablet/Mobile breakpoints in Section 4.4 — do not pick a different card-grid breakpoint than the global responsive breakpoints for any reason.

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

These are the atomic visual units every higher-level component is built from. When a new component is needed and not explicitly defined in Section 6, compose it from these primitives per Section 13.

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
- Do not animate `box-shadow` size/blur (shadows are near-static per Section 2.4), do not use parallax, do not use auto-playing decorative animations, do not use confetti/celebratory animation, do not use skeleton shimmer beyond a simple opacity pulse for loading states (Section 10.4).
- Respect `prefers-reduced-motion: reduce`: when set, disable all transform/translate hover effects and reduce all transition durations to `0ms` (state changes should be instant, not merely faster).

---

## 6. Core Components

### 6.1 Top bar

- Fixed/sticky at `top: 0`, `z-index: 50`.
- Height: exactly `76px` total (this is the fixed value referenced throughout this document — Section 4.2's sticky filter offset, Section 6.3's sticky table header offset). Achieved via `14px` vertical padding, `28px` horizontal padding (matches container padding), around content whose line-height brings the total to `76px`; implementations must set `height: 76px` explicitly on the top bar container rather than letting it float to whatever the padding-plus-content total happens to compute to, so the value cannot drift if a font, weight, or padding value is later adjusted on this element without a corresponding spec change.
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
- `<thead>` cells: background `--bg-inset`, text `eyebrow`-style (uppercase, `11px`, `700` weight, `0.06em` letter-spacing, `--text-tertiary`), `14px 16px` padding, bottom border `1px solid --border`, `position: sticky` with `top: 76px` (the exact top bar height per Section 6.1) so headers remain visible while scrolling a long table. If the table is rendered inside the two-column filter+content layout (Section 4.2), this is the same `76px` value, not an additive offset — the filter column and the table header stick to the same horizontal line.
- `<tbody>` cells: `16px` padding, `body-sm` (13.5px) size, bottom border `1px solid --border`, last row has no bottom border.
- Row hover: background shifts to `--bg-inset`, transition `120ms`.
- **First column convention**: the leading/identity column (provider, package, service name) renders as a `service-cell`: a `28px × 28px` logo/initials square (`--bg-inset` background, `1px solid --border`, `--radius-sm`, centered bold initials at `12px` in `--text-secondary`) plus a stacked name block (`service-name`: `600` weight, `13.5px`, `--text-primary`; optional `service-sub`: `11.5px`, `--text-tertiary`, e.g. plan/tier name).
- **Numeric/spec columns**: always rendered in mono (`data` scale, Section 3.4). All numeric columns in a given table are **right-aligned**. This is a fixed rule, not a per-table choice: right-alignment is what makes digit stacks and decimal points scan as a column, and giving implementers a left/right choice is exactly the kind of open decision that produces divergent output from an identical spec. The identity column (Section 6.3) and any plain-text/label columns remain left-aligned.
- **Column sizing algorithm (deterministic, do not substitute another strategy)**: table layout is `table-layout: fixed`. Column widths are assigned in this fixed order and never any other way:
  1. Identity column: `280px` on desktop (≥1024px), `220px` on tablet (640–1023px). This width does not vary with content.
  2. Any boolean/badge column (Section 6.3 "Boolean/inclusion columns"): `120px` fixed.
  3. All remaining numeric/spec columns split the leftover width equally (`1fr` each), with a `96px` minimum (`min-width: 96px`) per column enforced via the table's `<colgroup>`, not via cell-level CSS.
  4. If the sum of minimums exceeds the container width, the table does not shrink columns further or wrap cell content to multiple lines — it triggers the horizontal-scroll behavior in this section's "Mobile behavior" bullet, at whatever breakpoint the overflow first occurs (this can happen above 640px on tables with many columns; the 640px breakpoint is the *guaranteed* scroll point, not the only one).
  - Do not use `auto` table layout, do not use JavaScript-measured "fit content" sizing, and do not use CSS Grid for this component — `table-layout: fixed` with the widths above is the only permitted implementation, because it is the only one of the three that produces byte-identical column widths across independent implementations of the same dataset.
- **Cell content overflow**: every table cell (identity, numeric, badge) is single-line. Apply `white-space: nowrap; overflow: hidden; text-overflow: ellipsis` to the cell's text content. Do not wrap cell text to multiple lines and do not increase row height to fit long values — truncate instead. Exception: the `service-sub` line in the identity cell (Section 6.3) may wrap only if it would otherwise force the primary `service-name` line to truncate; `service-name` itself never wraps or truncates before `service-sub` does (i.e. `service-sub` absorbs the overflow first). For truncated numeric or identity values, add a native `title` attribute with the full untruncated value so it is recoverable on hover/long-press; do not build a custom tooltip component for this.
- **"Best value" row**: exactly one row per table may be marked as the standout/winning option using the `row-best` treatment: a `3px` solid `--accent` bar on the left inner edge of the first cell (implemented as a pseudo-element or inset box-shadow, not by adding a visible 4th table column), and the specific winning metric's cell(s) additionally styled per the "best value cell" rule below. Do not mark more than one row as best per table. **Tie-break rule**: the "best" designation is driven by exactly one column, chosen by the content author/data source (not the implementer) and passed to the component as an explicit `bestValueColumn` parameter — the component itself never infers which metric matters most. If the data source does not supply a `bestValueColumn`, the component renders the table with no `row-best` treatment at all (no best-row indicator, no per-cell "best value" styling anywhere in the table); it must not guess a metric on its own. If two or more rows are exactly equal on the designated `bestValueColumn`, omit the `row-best` treatment entirely rather than marking either row.
- **"Best value" cell** (independent of whether the row is the overall winner): the single best numeric value in a comparison column (e.g. lowest price, highest uptime) is styled with `color: --good` and `font-weight: 600`, keeping the same mono font/size as sibling cells in that column. Do not apply this to every "good-ish" value — only the single best cell per column, per table, so the signal stays meaningful. **Tie-break**: this rule applies per numeric column independently of the row-level designation above — if two or more values in one column are exactly equal for "best" (e.g. two providers both at the lowest price), no cell in that column gets the best-value treatment for that comparison. Ties suppress the signal; they never duplicate it.
- **Boolean/inclusion columns** (e.g. "Free tier", "IPv6 support"): render as a badge (Section 6.5) using `badge-good`/`check-good` for true and `badge-bad`/`check-bad` for false — never a bare "Yes"/"No" text string without the badge treatment, since the badge's color is what makes the table scannable at a glance.
- **Mobile behavior**: below `640px` (and at any wider viewport where columns overflow per the sizing algorithm above), do not reflow columns into stacked cards by default — instead make the `table-card` horizontally scrollable (`overflow-x: auto`) with the identity column set to `position: sticky; left: 0` with a solid `--bg-raised` background (not transparent, so scrolling numeric columns don't show through underneath it) and a `1px solid --border` right edge to visually separate it from the scrolling region. This sticky-identity-column behavior is a CSS-only requirement (`position: sticky` is supported by every browser this system targets) — it is never conditional on "framework support," and it is not optional. Only convert to a stacked-card layout per row if a specific page explicitly requires it; this is a deviation from default and should be a deliberate choice, not the automatic mobile fallback.

### 6.4 Spec cards

Used for single-product detail summaries (e.g. an email API's card, a VPS plan's card) in card-grid contexts.

- Container: `--bg-raised`, `1px solid --border`, `--radius-lg`, `20px` padding. Hover: border becomes `--border-strong`, `transform: translateY(-2px)`, transition `150ms`.
- Header row (`spec-card-head`): logo square (`36px × 36px`, same visual treatment as the table's service logo but larger, `14px` initials) on the left, an optional status/category badge (Section 6.5) on the right, `justify-content: space-between`, `16px` bottom margin.
- Title block: product name in `h3` (21px/600), immediately followed by a `tagline` in `12.5px`, `--text-tertiary`, `16px` bottom margin before the price.
- Price block (`price-tag`): mono `amount` at `data-lg` (26px/700) immediately followed by a `period` string (e.g. "/ 3k emails/mo") at `12.5px`, `--text-tertiary`, baseline-aligned, `16px` bottom margin.
- Spec list (`spec-list`): vertical stack of `spec-row` items, each a flex row with a `k` (key) label in `--text-tertiary` body-sm on the left and a `v` (value) in mono `data` weight `500` on the right, separated by a `1px dashed --border` bottom border (last item has none), `9px` vertical padding rhythm, `18px` bottom margin before the card's closing action.
- Closing action: a full-width Secondary small button ("View full spec" or similar), `width: 100%`, centered content. This is the default and only pattern for a spec card's closing action; a card either includes this button or omits the closing action entirely (no button) — do not substitute a text link, icon-only button, or any other control in this slot.
- Card grid spacing and breakpoints per Section 4.2.

### 6.5 Badges

- Base shape: `--radius-sm` (NOT pill-shaped — see Section 4.3), `3px 8px` padding, mono font, `11px` size, `600` weight, `uppercase`, `0.02em` letter-spacing, `display: inline-flex`, `gap: 5px` (for icon+label badges).
- Variants map directly to semantic tokens:
  - `badge-good`: background `--good-bg`, text `--good`. Use for: pass, included, winner, "best value," positive verification.
  - `badge-bad`: background `--bad-bg`, text `--bad`. Use for: fail, not included, deprecated, discontinued.
  - `badge-warn`: background `--warn-bg`, text `--warn`. Use for: price change, partial/limited support, stale/unverified data, beta status.
  - `badge-neutral`: background `--bg-inset`, text `--text-secondary`, border `1px solid --border`. Use for: informational, non-judgmental tags (e.g. "Free tier" as a category label distinct from a pass/fail check, "Popular").
  - `badge-accent`: background `--accent-dim`, text `--accent-text`. Use for: editorial/curatorial calls that are not pass/fail judgments — "Editor's pick," "New," "Recommended."
- Never invent a new badge color combination outside these five variants. If a new status concept appears, map it to the nearest of good/bad/warn/neutral/accent per Section 13 rather than introducing a sixth color.
- Optional leading glyph: literal `✓` or `✕` characters (Section 5.3) may prefix the badge label text for boolean-style badges; omit the glyph for non-boolean badges (neutral/accent variants normally have no glyph).

### 6.6 Filter panel

- Container: `--bg-raised`, `1px solid --border`, `--radius-lg`, `18px` padding, `position: sticky` (desktop only, see 4.2/4.4).
- Structure: a vertical stack of `filter-group` blocks, each with a `22px` bottom margin (last group has none).
- Group heading: `11px`, uppercase, `0.06em` letter-spacing, `700` weight, `--text-tertiary`, `10px` bottom margin.
- Checkbox filter option: a flex row, `space-between`, `6px` vertical padding, `13.5px` label in `--text-secondary` (hovers to `--text-primary`), with a right-aligned mono `count` in `11px`/`--text-tertiary` showing the number of matching results. Checkbox input uses `accent-color: var(--accent)` rather than a custom-built checkbox graphic, unless a specific page's framework requires a custom control — if so, the custom control must still resolve to an `--accent`-filled check state visually equivalent to the native `accent-color` rendering.
- Range filter: a native or custom range/slider input with `accent-color: var(--accent)` (or accent-colored track/thumb if custom-built), paired with a min/max label row underneath: `12px`, mono, `--text-tertiary`, uppercase, `0.08em` letter-spacing. This label styling is fully specified here; do not look to the reference HTML (Section 14) for any detail not stated in this bullet.
- The filter panel never uses `--bg-inset`; it is a raised surface, not a recessed one, because it is an interactive control panel, not passive/dimmed content.

### 6.7 Form fields

- Field wrapper: vertical flex, `6px` gap between label and input.
- Label: `12px`, mono font, `--text-tertiary`. **Default casing is uppercase** (`text-transform: uppercase`, `0.04em` letter-spacing), matching the eyebrow/filter-group-heading convention used everywhere else labels appear in this system (Sections 3.3, 6.6). Sentence case is permitted only if a specific page already has an established, documented reason to deviate (e.g. integrating into a pre-existing non-Dexium form on the same page) — absent such a documented reason, every implementation uses uppercase, full stop. This removes the previous per-page choice, which was a source of divergence between otherwise-identical forms.
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

## 7. Implementation Contracts (read this before implementing anything)

Section 6 and the rest of this document describe **visual intent**: what a component should look like and communicate. This section describes **implementation contracts**: the specific rendering strategy an agent or developer must use whenever more than one technique could plausibly satisfy that intent. Sections 1–6 tell you what "correct" looks like; this section tells you which of several correct-looking techniques to actually write, so that two independent implementations of the same screen produce the same markup, the same computed widths, and the same behavior at every viewport — not just a similar impression.

If anything in Section 6 states a rule and this section states a more specific technique for implementing that same rule, this section's technique wins for *how* to build it; Section 6 still wins for *what it should look like and communicate*. If the two ever appear to conflict outright (not just differ in detail level), that is a documentation defect — implement Section 6's visual intent using this section's default techniques, and flag the conflict per Section 13.7 rather than silently picking one side.

### 7.1 Sizing strategy — default technique per layout situation

Every layout in this system falls into exactly one of the following situations. Do not choose a sizing strategy by aesthetic preference; choose it by which situation applies.

| Situation | Required technique | Never use |
|---|---|---|
| A row of columns where each column's content is a comparable, bounded fact across rows (tables) | `table-layout: fixed` with explicit pixel/`fr` widths assigned per Section 6.3's column-sizing algorithm | `table-layout: auto`, JS-measured "fit content," CSS Grid standing in for a table |
| A fixed-count grid of self-contained units (card grids) | CSS Grid, `repeat(N, 1fr)`, with `N` and the gap fixed by Section 4.2 — never content-driven column count | `auto-fit`/`auto-fill` with `minmax()` (these produce a different column count depending on container width in a way this system does not define — see 7.1.1) |
| A single fixed-width sidebar beside fluid content (filter+content layout) | CSS Grid or Flexbox with the sidebar at an explicit pixel width (`240px`) and content at `1fr` — per Section 4.2 | `flex-basis` percentages, `minmax(240px, 1fr)` on the sidebar track (this allows the sidebar to grow past 240px, which this layout never permits) |
| A horizontal run of same-role controls that should share space evenly (form fields on one row, per Section 6.7) | Flexbox, `flex: 1` per item, fixed `min-width` per Section 6.7 | CSS Grid (adds a dimension of complexity this pattern doesn't need) |
| A vertical stack of items whose count varies with data (spec-list rows, filter options, alert stacks) | Flexbox column, fixed `gap` from Section 4.1's spacing scale | Margin-based spacing (`margin-bottom` on every child except the last) — use `gap`, since it does not require a `:last-child` exception and cannot be doubled by accident |

**7.1.1 Why `auto-fit`/`auto-fill` is disallowed for card grids:** these keywords make the resulting column count a function of the exact container width at render time, which means the same viewport width can legitimately produce a different column count depending on rounding, scrollbar width, or a few pixels of difference between two implementations' container padding. Section 4.2's fixed breakpoint-driven column counts (3 / 2 / 1) are deliberately chosen so that "how many columns at this viewport" has exactly one correct answer, checkable by reading a breakpoint table rather than by rendering and measuring.

### 7.2 Alignment defaults

- **Text**: left-aligned by default everywhere (Section 3.3). The complete list of exceptions is: numeric table columns (right-aligned, Section 6.3); the `price/period` pair and the `section-head`'s label-and-metadata pair (both baseline-aligned to each other, Sections 6.4 and 6.9 — "baseline-aligned" here means the two text runs share a text baseline, not that either is centered); a button's icon-and-label content (horizontally centered as a unit within the button's own bounds, Section 6.2 — this centers the control's content within itself, it does not center text within a page or section); and centered *empty-state message blocks* (Section 10.6). No other centered or baseline-aligned text exists in this system — any new component defaults to left-aligned text unless it matches one of these five named cases exactly.
- **Flex/grid alignment**: rows that pair a label with a value (spec-list rows, filter checkbox options, key/value pairs generally) use `justify-content: space-between` with `align-items: center`, never `space-around` or `space-evenly`. Rows that pair an icon with text (alerts, badges with glyphs) use `align-items: flex-start` if the text can wrap to multiple lines, `align-items: center` if the text is guaranteed single-line — badges (always single-line, Section 6.5) use `center`; alerts (Section 6.8, text may wrap) use `flex-start` with the icon aligned to the first line as already specified.
- **Vertical rhythm inside a component**: every component in Section 6 specifies its own internal margins between sub-parts (e.g. spec card's `16px` header margin, `16px` price margin). These are not additive suggestions — implement exactly the margin value given between exactly those two sub-parts, and do not also add default browser margin on the underlying elements (`h1`–`h4`, `p`) that would stack with the specified value. Zero out default heading/paragraph margins globally (a CSS reset or `margin: 0` on typographic elements) and apply only the spacing values this document specifies.

### 7.3 Overflow and truncation — global default

Unless a component's section states a different rule (the comparison table's cell truncation in Section 6.3 is the only override), the global default for any text content that might exceed its container is:

1. First choice: let the container grow to fit the content (most UI text in this system — headings, body copy, card taglines — is not artificially height-constrained).
2. If a fixed height or single-line constraint is explicitly specified for that element (badges, table cells, the `service-name` line, button labels), apply `white-space: nowrap; overflow: hidden; text-overflow: ellipsis` rather than allowing visual overflow (text spilling outside its container) or silently clipping without an ellipsis.
3. Never use `-webkit-line-clamp` multi-line truncation anywhere in this system unless a future amendment explicitly introduces it for a named component — its absence from Section 6 is deliberate, not an oversight, since multi-line clamping tends to reflow unpredictably across fonts/zoom levels in a system that otherwise avoids relying on the browser's line-breaking to determine layout height.
4. Long, unbreakable strings (URLs, hashes, long identifiers rendered as data per Section 3.4) get `word-break: break-all` only inside body text contexts, never inside single-line-constrained contexts (which truncate per point 2 instead).

### 7.4 Responsive adaptation — priority order when space runs out

When a component or layout must shed content to fit a smaller viewport, and Section 6 does not already give that component a specific mobile rule, resolve it in this fixed priority order (highest priority = last thing to be sacrificed):

1. **Core data/content never disappears.** Prices, specs, statuses, and the identity of what's being compared are never hidden at any breakpoint — they may reflow, scroll, or shrink in font size within the type scale (Section 3.2), but not be removed from the DOM or `display: none`'d for space reasons.
2. **Secondary navigation and chrome degrade before primary content.** Per Section 4.4's top bar rule, deprioritize/hide secondary nav items before shrinking the logo or search affordance — this same ordering (chrome before content) generalizes to any component: a card's optional status badge (Section 6.4) may be dropped at extreme widths before its price or spec list would ever be touched.
3. **Layout reflows before content is removed.** Multi-column becomes single-column, sidebars stack above content, tables scroll horizontally — all before any content is hidden outright (this is why the comparison table scrolls rather than reflowing into cards by default, per Section 6.3).
4. **Only as a last resort, and only if a specific page explicitly opts in, is content removed** (e.g. converting a table to per-row stacked cards on mobile, Section 6.3's explicitly-flagged deviation). This is never the automatic default for a new component — an agent implementing a new component under space pressure must exhaust priorities 1–3 before considering this one, and must treat reaching for it as a decision worth flagging (per Section 13.7) rather than a silent default.

### 7.5 Component composition priorities

When a screen needs to combine multiple Section 6 components and it's not obvious which one "owns" a shared piece of chrome (e.g. does the table or the page provide the section head above it?), resolve using these ownership rules:

- **`section-head` (Section 6.9) always belongs to the page/screen composition, never to the component beneath it.** A comparison table, card grid, or filter panel never renders its own section head internally — the page places a `section-head` above the component and passes it the result count/metadata string. This keeps every component reusable in a context that doesn't want a section head (e.g. a table embedded inside a modal).
- **The filter panel and its paired content component are siblings in a two-column grid (Section 4.2), never parent/child.** Do not implement the filter panel as a child of the comparison table or vice versa; both are independent components placed into the two-column layout primitive.
- **Badges (Section 6.5) are always a leaf component rendered inside another component's cell/slot** — a badge never contains another component, and nothing renders "inside" a badge except its optional glyph and label text.
- **Alerts (Section 6.8) are page/section-level, not row-level.** Do not render an alert inside a single table row or a single spec-card's spec-list; if a single row/item needs an inline warning, use a badge (Section 6.5) instead — alerts are reserved for messages about an entire component or the page, per the "do not nest alerts inside cards" rule already stated in 6.8.

### 7.6 Conflict-resolution hierarchy

When two rules in this document appear to genuinely conflict for a specific case (not merely under-specify it), resolve in this fixed order, and stop at the first rule that resolves the conflict:

1. A more specific component rule (Section 6.x, 7.x) overrides a more general primitive or philosophy statement (Section 1, 5).
2. A numeric/exact value anywhere in this document overrides a qualitative description anywhere else that seems to suggest a different value.
3. Section 1.2's principle order (data legibility > structure-as-meaning > color restraint > flatness > mono/sans pairing > no unnecessary chrome) breaks ties between two visual treatments that are otherwise equally well-specified.
4. Section 0, Rule 6 ("when in doubt between more minimal and more decorated, choose more minimal") breaks any remaining tie.
5. If the conflict still isn't resolved after steps 1–4, it is a genuine gap in the spec: follow Section 13's Decision Rules for Undefined Cases, and note the conflict (per 13.7) rather than resolving it silently — a documented, flagged assumption is recoverable; a silent one is what causes divergent implementations.

---

## 8. Iconless Logo / Service Identity Convention

In products that list many third-party entities (services, packages, vendors, companies) without reliably available brand assets, the default identity treatment is a **monogram square**, not a fetched brand logo, unless real logo assets are explicitly integrated:

- Square background `--bg-inset`, `1px solid --border`, radius `--radius-sm`.
- Centered 1–3 letter uppercase initials, `700` weight, Inter (not mono — initials are a label, not data), color `--text-secondary`.
- Sizes: `28px` in table rows, `36px` in spec cards, `44px` if used in a larger featured/hero product callout.
- If real logo image assets are available and approved for use, they replace the monogram inside the same square container (same size/border/radius), `object-fit: contain`, with internal padding equal to `4px` at the `28px` table size, `5px` at the `36px` card size, and `6px` at the `44px` hero size (each proportionally close to, but a fixed pixel value rather than a percentage, so padding does not require runtime calculation). Do not stretch or crop logos edge-to-edge.

---

## 9. Voice & Content Rules (applies to all UI copy)

Even though this is primarily a visual spec, copy is part of the design system because mismatched tone breaks the "dev tool" character as much as wrong colors would.

- Write from the user's point of view, describing what they control: "Compare pricing," not "Pricing comparison engine."
- Use active voice and plain verbs. Buttons say what they do: "Compare now," "Add to list," "View full spec" — not "Submit" or "Learn more" as generic filler.
- No marketing superlatives in UI chrome ("blazing fast," "revolutionary," "best-in-class" as unearned claims). Superlatives are only acceptable when they are the literal output of a data comparison (e.g. a badge that says "Best value" because the data actually shows it).
- Numbers and technical facts are stated plainly, with units, never rounded misleadingly (state "99.95% SLA," not "~100% uptime").
- Empty states describe what's missing and what to do next in the interface's own voice: "No providers match these filters. Try widening your price range." — not an apologetic tone ("Oops! Nothing here 😅").
- Error messages state what happened and how to fix it, without blame or unnecessary apology: "Couldn't load pricing for this provider. Refresh to try again."
- Status labels inside badges are short nouns/adjectives, sentence case is not required since badges are uppercase by style (Section 6.5), but the underlying copy should still be a plain, specific word: "Deprecated," "Free tier," "Price increase" — not vague terms like "Warning" or "Notice" alone.

---

## 10. States (must be defined for every interactive component)

Every interactive component built in this system must explicitly account for the following states. When implementing any new component, do not stop at the default/rest state.

### 10.1 Hover
- Buttons: per Section 6.2 per-variant rules.
- Table rows: background → `--bg-inset`.
- Cards: border → `--border-strong`; optionally `translateY(-2px)` for spec cards specifically (not for table rows or filter options).
- Links/inline text actions: color shifts from `--text-secondary`/`--text-primary` to `--accent-text`; underline optional but recommended for inline links within body copy (not for nav links).

### 10.2 Focus (keyboard)
- Every focusable element must show a visible focus ring: `1px solid --accent` border/outline plus `0 0 0 3px --accent-dim` box-shadow, per Section 5.2.
- Never remove focus outlines (`outline: none`) without supplying this equivalent replacement.
- Focus order must follow visual/DOM order; do not reorder tab sequence to "fix" a layout without also fixing the underlying DOM order.

### 10.3 Active / pressed
- Buttons: on `:active`, remove the hover `translateY` lift (return to `translateY(0)`) and reduce `filter: brightness` slightly below the hover value (e.g. `brightness(0.96)` on Primary) to give tactile press feedback.

### 10.4 Loading
- Inline loading (e.g. a table refreshing filtered results): reduce content opacity to `0.5` and disable pointer events on the affected region; do not replace with a full-page spinner for partial updates.
- Skeleton loading (initial page/table load): flat rectangular placeholders using `--bg-inset` background with a subtle opacity pulse animation (`0.6` ↔ `1`, `1.2s` ease-in-out infinite) — same radius as the content they replace (e.g. `--radius-sm` for table cells, `--radius-lg` for cards). Do not use shimmer/gradient-sweep skeletons.
- Button loading state: replace label with a small inline spinner (simple rotating arc, `--accent` on Primary buttons, current text color on Secondary/Ghost) and disable the button; keep button width stable (do not let it shrink to fit just the spinner).

### 10.5 Disabled
- `opacity: 0.45`, `cursor: not-allowed`, all hover/focus/active visual changes suppressed.
- Disabled form fields additionally get `background: --bg` (not `--bg-inset`) to visually flatten them further against the inset norm.

### 10.6 Empty
- Empty states (no results, no data yet) render inside the same container type the content would have used (e.g. an empty comparison table still renders the `table-card` wrapper) with a centered message block: a short bold statement (`h4` scale) plus a `body-sm` supporting line and, where actionable, a Secondary button to resolve it (e.g. "Clear filters"). Follow the voice rules in Section 9.

### 10.7 Error
- Inline field errors: border becomes `--bad`, a `caption`-scale message in `--bad` appears below the field with `4px` top margin.
- Component/section-level errors (e.g. a table failed to load data): use the `alert-bad` treatment (Section 6.8) in place of the content, with a retry action (Secondary button) if applicable.

---

## 11. Accessibility Requirements

- Color is never the sole carrier of meaning: every `good`/`bad`/`warn` semantic signal must be paired with a text label or icon glyph (badges already satisfy this by combining color with a word and/or ✓/✕ glyph — never ship a bare colored dot with no label as a status indicator).
- Minimum contrast: body text against its background must meet WCAG AA (4.5:1) in both themes; the token values in Section 2 are chosen to satisfy this on their intended background pairings (e.g. `--text-secondary` on `--bg`/`--bg-raised`, not on `--bg-inset` if that combination fails contrast — verify before reusing a text token on a non-standard background).
- All interactive elements must be reachable and operable via keyboard, with visible focus states per Section 10.2.
- Respect `prefers-reduced-motion` per Section 5.4.
- Use semantic HTML elements (`<table>`, `<thead>`, `<button>`, `<label for>`, etc.) as the underlying structure regardless of visual styling; the visual system in this document must be implemented as a styling layer on top of correct semantics, not as a replacement for them.

---

## 12. Explicit Non-Goals / Anti-Patterns

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
- No introducing a new hue for a new "status" without mapping it to Section 13's decision rule first.
- No `table-layout: auto`, JS-measured "fit content" column sizing, or CSS Grid standing in for the Comparison Table's column layout (Section 7.1) — `table-layout: fixed` with Section 6.3's algorithm is the only permitted technique.
- No `auto-fit`/`auto-fill`/`minmax()` responsive card grids (Section 7.1.1) — card-grid column counts come only from Section 4.2's fixed breakpoints.
- No multi-line `-webkit-line-clamp` truncation anywhere (Section 7.3) — single-line ellipsis truncation or unconstrained height only.
- No hiding or `display: none`-ing core comparison data (prices, specs, statuses) at any breakpoint to solve a space problem (Section 7.4) — reflow or scroll instead.

---

## 13. Decision Rules for Undefined Cases

When a screen or component is requested that this document does not explicitly cover, resolve it in this order:

1. **Identify the closest existing component** in Section 6 by function (not by visual similarity) — e.g. a new "compare npm packages by weekly downloads" table is still the Comparison Table component (6.3), even though its content differs from the VPS example.
2. **Reuse tokens, not values.** Pull colors, type, spacing, and radius exclusively from Sections 2–4. Never hand-pick a new hex/pixel value "because it looks right" for one screen.
3. **Map new semantic states to existing semantics.** If a new status concept appears (e.g. "actively maintained" vs "unmaintained" for an npm package), decide which existing semantic bucket it belongs to (`good`/`bad`/`warn`/`neutral`/`accent`) based on whether it represents a positive, negative, cautionary, informational, or editorial signal — do not create a sixth color.
4. **Apply the data-typography rule automatically.** Any newly introduced field that is a comparable quantitative fact must use mono typography and, if a "best in column" designation makes sense for it, the best-value color/weight treatment from Section 6.3 — even if that specific field (e.g. "GitHub stars," "bundle size in KB," "last commit date") is not named anywhere in this document.
5. **Default to the plainest composition.** If two valid layouts could satisfy a new request (e.g. cards vs. table for a given dataset), prefer whichever is denser and more scannable, consistent with Section 1.2's priority on data legibility — tabular/list-like data defaults to the Comparison Table; single-entity deep-dives default to Spec Cards or a full detail page built from the same primitives (raised surface, section heads, spec-list rows).
6. **If genuinely novel** (no analogous component exists even functionally), construct it strictly from Section 5 primitives (surfaces, borders, spacing, radius scale, motion rules) and document the new pattern's rationale in a comment or accompanying note so it can be formally added to this spec later — do not silently introduce a one-off visual style that other pages won't be able to replicate.
7. **Flag it, don't just decide it.** Whenever an implementation reaches this section at all — meaning no existing rule directly covered the case — leave a short, explicit note at the point of implementation (a code comment is sufficient; it does not need to be user-facing) stating what was undefined and which of rules 1–6 was used to resolve it. This applies equally to genuinely novel components (rule 6) and to any case elsewhere in this document that says to "flag" or "note" a conflict or gap (e.g. Section 7.6's conflict-resolution hierarchy, step 5). The note is what turns a one-off interpretation into something the next implementer — human or AI — can find, agree with or challenge, and eventually fold back into this document, rather than an invisible decision that only that implementation knows was ever made.

---

## 14. Reference Implementation

A working HTML reference demonstrating every component in this document (hero, type scale, color tokens, buttons, badges, a comparison table with filter sidebar, spec cards, form fields, alerts) exists as `dexium-terminal-design-system.html`. Its content uses a services-comparison example purely as one illustration of the system — the same components apply directly to any other domain (dashboards, admin panels, developer tools, etc.) by swapping in that project's real content. That file is illustrative; **this markdown document is authoritative**. Where the two ever disagree due to future edits of one but not the other, this document governs, and the reference HTML should be updated to match — not the reverse. Note that the reference HTML predates the no-manual-toggle rule in Section 1.3 and included a manual dark/light switch for demonstration purposes only; any new implementation must omit that control per Section 1.3.
