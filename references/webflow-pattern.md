# Iverksetter Webflow Pattern

How Iverksetter builds Webflow sites within the Client-First v2 universe. This is the house style — the opinionated layer on top of Finsweet Client-First v2. Use it as the source of truth for every new build.

## Core ideas

1. **Style Guide page is the source of truth.** Every class is created and edited on `/style-guide`. Every other page references those classes. Never create or edit global classes from a target page context.
2. **Body-level defaults first.** Background color, text color, font family, and line height live on the body tag (on Style Guide page). Every section inherits. Sections only override when they are genuinely different.
3. **CF v2 4-layer section structure.** Every section is wrapped in four layers: `section_{name}` → `padding-global + padding-section-*` (combo) → `container-*` → `{name}_layout`. Custom class sits at the section level; CF v2 utilities handle padding, max-width, and responsiveness; a custom layout class handles the flex/grid engine.
4. **Typography variables with breakpoint modes.** Base, Tablet, Mobile Landscape, Mobile Portrait. Responsiveness lives in the variable, not in per-breakpoint Designer overrides.
5. **`heading-style-*` as visual-override combo classes.** Applied on top of semantic HTML tags. Keep the right tag for SEO and a11y, change the visual size with the combo.
6. **Scoped color groups for components.** Brand colors live in the global palette. Component-specific colors (Terminal/red, Alert/bg, Card/border) live in their own groups alongside, either holding raw values or aliasing to brand.
7. **Variable-first workflow.** Variables before styles, always. Styles reference variables from the start via native Webflow variable bindings — never raw CSS `var()` strings, never hex values that get rebound later.
8. **Semantic HTML first.** The tag carries meaning, the class carries appearance. Use H2 for eyebrow labels above H1 when they hold SEO-valuable keywords. Use `<p>` for descriptive paragraph copy.

## Starting point — always the clonable

Every new Iverksetter Webflow project starts from the Finsweet Client-First v2 clonable:

https://finsweet.info/client-first-cloneable

Never start from a blank project. The clonable ships with:
- Style guide page (`/style-guide`, draft)
- `page-wrapper`, `main-wrapper`
- `container-small | medium | large` (max-width utilities)
- `padding-global` (horizontal gutters, responsive)
- `padding-section-small | medium | large | xlarge` (vertical rhythm, responsive)
- `heading-style-h1` through `heading-style-h6` (base sizes, often not variable-bound yet)
- `text-size-*`, `text-color-*`, `text-weight-*`, `text-style-*`, `text-align-*`
- `margin-*`, `padding-*`, `max-width-*`, `background-color-*`
- Visibility utilities (`hide`, `hide-tablet`, `hide-mobile-*`)
- `fs-styleguide_*` internal classes for the Style Guide page itself

We customize the variables and rebind these classes. We never recreate or modify the CF v2 utility definitions.

## Body styles — the first thing you do on Style Guide

Before creating any custom classes, open the Style Guide page, select the body (or use the "All Pages → Body" tag selector), and bind these four properties:

| Property | Bind to |
|----------|---------|
| `background-color` | `Background Color/background primary` |
| `color` | `Text Color/text primary` |
| `font-family` | `Font Family/sans` (or whichever is body default) |
| `line-height` | `Line Height/normal` (or the body baseline) |

These four bindings cascade to every page, every section, every text element unless explicitly overridden. This is how you avoid setting `background-color: black` on every section and `color: alabaster` on every heading.

**Do this from Style Guide page context.** Webflow tracks which page was active when a style was edited, and you want the edit history to show "Style Guide" as the source for global defaults.

## Variable collections

Two collections per site. Sometimes a third for Spacing if the project needs a custom scale.

### Color collection

Modes: **Base** (always). Optional Dark mode per project.

#### Brand group (named colors)

Project-specific. Match Figma tokens exactly. **Always name them semantically** — never `brand-1`, `color-2`. Use the Figma token names or invent descriptive names.

Examples from real builds:
- `Alabaster` (#f2f0e4) — off-white primary text
- `Blanched Almond` (#fbefc9) — cream highlight accent
- `Natural Linen` (#c2beb0) — beige secondary text
- `Burnt Sienna` (#e76e50) — orange accent
- `Cornsilk` (#fef2dc) — light cream CTA background

#### Neutral group (zinc or custom scale)

A greyscale ramp. The shadcn zinc scale is a solid default:

| Token | Hex |
|-------|-----|
| 500 | `#71717a` |
| 600 | `#52525b` |
| 700 | `#3f3f46` |
| 800 | `#222222` |
| 900 | `#171717` |
| 950 | `#09090b` |

Add `black` and `white` if the clonable's defaults don't already cover them.

#### System group

- `success green`, `success green dark`
- `warning yellow`, `warning yellow dark`
- `error red`, `error red dark`
- `focus state` (usually brand-blue or similar)

#### Semantic layer (Background, Border, Link, Text)

These are the classes that utility classes bind to. They point at the primitives above. Editing the semantic layer = site-wide rebrand.

Examples (from a dark-theme build):
- `Background Color/background primary` → Black
- `Background Color/background secondary` → Neutral 900
- `Background Color/background tertiary` → Neutral 950
- `Background Color/background alternate` → Cornsilk (light accent card bg)
- `Text Color/text primary` → Alabaster
- `Text Color/text secondary` → Natural Linen
- `Text Color/text alternate` → Blanched Almond
- `Border Color/border primary` → Neutral 800
- `Border Color/border alternate` → Blanched Almond

#### Scoped component groups (NEW)

For components with specific color tokens that don't belong in the brand palette, create a scoped group in the same collection:

```
Terminal/
  red    = #ff5f57
  yellow = #febc2e
  green  = #28c840
```

```
Alert/
  bg     = → Warning Yellow (aliased)
  border = → Warning Yellow Dark (aliased)
  text   = → Neutral 900 (aliased)
```

Scoped groups can hold raw values (one-off colors) OR alias to brand primitives. They keep component-specific tokens organized without polluting the main palette.

### Typography collection

Modes: **Base**, **Tablet**, **Mobile Landscape**, **Mobile Portrait** — four modes total. Base is the desktop default. Create the other three explicitly via `create_variable_mode`.

Variable naming convention: `Group Name/variable-name` using `/` as the group separator. CSS names auto-generate as `--_typography---group-name--variable-name`.

#### Font Family group (no mode differences)

- `Font Family/serif` — display / expressive (e.g., Instrument Serif)
- `Font Family/sans` — body / UI (e.g., Instrument Sans)
- `Font Family/mono` — terminal / code (e.g., Courier New)

Bind these via proper Webflow variable binding, not raw CSS strings. The variable value holds the font stack (`"Instrument Serif, serif"`).

#### Font Weight group (no mode differences)

- `Font Weight/regular` = 400
- `Font Weight/medium` = 500
- `Font Weight/semibold` = 600
- `Font Weight/bold` = 700
- `Font Weight/extrabold` = 800

#### Line Height group (no mode differences)

- `Line Height/tight` = 1 (headings)
- `Line Height/normal` = 1.5 (body text)

#### Font Size per-level (mode-aware — this is where responsiveness lives)

Each heading and text level is a size variable with different values per mode. Ship these as the baseline and let the Figma design override per project:

**Headings** (responsive — shrink on smaller screens):

| Level     | Base     | Tablet   | M-Land   | M-Port   |
|-----------|----------|----------|----------|----------|
| Display 1 | 6rem     | 5rem     | 3.5rem   | 3rem     |
| H1        | 3.5rem   | 3rem     | 2.5rem   | 2.25rem  |
| H2        | 3rem     | 2.5rem   | 2rem     | 1.875rem |
| H3        | 2.5rem   | 2rem     | 1.75rem  | 1.625rem |
| H4        | 2rem     | 1.75rem  | 1.5rem   | 1.5rem   |
| H5        | 1.5rem   | 1.375rem | 1.25rem  | 1.25rem  |
| H6        | 1.25rem  | 1.25rem  | 1.125rem | 1.125rem |

**Body text** (usually same across all modes):

| Level        | rem     | px  |
|--------------|---------|-----|
| Text Large   | 1.25rem | 20  |
| Text Medium  | 1.125rem| 18  |
| Text Regular | 1rem    | 16  |
| Text Small   | 0.875rem| 14  |
| Text Tiny    | 0.75rem | 12  |

## Style Guide class bindings

The `heading-style-*` utilities ship with the clonable. Rebind them to Typography variables.

`heading-style-display-1` (create if missing) and `heading-style-h1` through `heading-style-h6` each bind:

| Property | Bind to |
|----------|---------|
| `font-family` | `Font Family/serif` (or per-level override) |
| `font-size` | `Font Size/{level}` |
| `font-weight` | `Font Weight/regular` (or per-level override) |
| `line-height` | `Line Height/tight` |

All bindings happen at the `main` breakpoint only. The Typography variable modes handle responsiveness — no per-breakpoint Designer overrides on heading-style classes. If the clonable ships with hardcoded `small` breakpoint overrides on these classes, **remove them**.

`text-size-*` utilities bind `font-size` and `line-height` to their respective body text groups.

`text-color-*` utilities bind `color` to Color variables.

`text-weight-*` utilities bind `font-weight` to Font Weight variables.

## The CF v2 4-layer section pattern

Every section in the final page tree uses this exact 4-layer wrapper:

```html
<section class="section_home-header">                   <!-- L1: section tag + single custom class -->
  <div class="padding-global padding-section-large">    <!-- L2: CF v2 utilities (combo for vertical rhythm) -->
    <div class="container-large">                        <!-- L3: CF v2 max-width utility -->
      <div class="home-header_layout">                   <!-- L4: custom flex/grid wrapper -->
        <!-- content -->
      </div>
    </div>
  </div>
</section>
```

### Layer 1 — Section (`section_{name}`)

The section tag carries a single custom class with the `section_` prefix. This class usually holds NO properties (or just background-color if the section is different from body default).

Naming rules:
- **Generic contexts** get a context prefix: `section_home-header`, `section_team-header`, `section_about-header`, `section_home-pricing`, `section_team-roster`
- **Unique / reusable** sections use their own name: `section_terminal`, `section_pricing`, `section_testimonials`, `section_footer`

The section class NEVER holds padding, max-width, or layout properties — those are on Layers 2, 3, 4.

### Layer 2 — Padding (`padding-global` + `padding-section-*`)

A div wrapping the content with two CF v2 utility classes (stacked combo):
- `padding-global` — responsive horizontal gutters
- `padding-section-small | medium | large | xlarge` — responsive vertical rhythm

**Never edit these.** They ship from the clonable and are set on the Style Guide page. Page-level overrides on `padding-global` or `container-large` are a red flag.

### Layer 3 — Container (`container-*`)

A div with a single CF v2 container utility:
- `container-small` (40rem)
- `container-medium` (64rem)
- `container-large` (80rem)

Pick the one matching the section's max-width. Never override its values on a specific page.

### Layer 4 — Layout (`{name}_layout`)

The custom layout engine. This is the ONLY custom-defined class in the wrapper stack (besides Layer 1). Holds:
- `display: flex` or `display: grid`
- `flex-direction` / `grid-template-columns` / `grid-template-rows`
- `align-items` / `justify-content` / `justify-items`
- **`gap`** — ALL spacing between children lives here
- Never padding, never margin

### Internal components inside a section

`_component` / `_layout` / `_wrapper` / `_top` / `_bottom` / `_header` / `_body` are for **elements inside** a section, not for the section itself.

Example — a terminal window component living inside `section_home-header`:

```html
<div class="home-header_layout">
  <a class="home-header_logo">Iverksetter</a>

  <div class="home-header_content">
    <h2 class="home-header_label">Growth Marketing · Tech · AI</h2>
    <h1 class="heading-style-display-1 home-header_h1">From MVP to <span class="home-header_h1-highlight">Growth</span></h1>
    <h3 class="home-header_description">Descriptive copy...</h3>
  </div>

  <div class="terminal_component">
    <div class="terminal_window">
      <div class="terminal_top">
        <div class="terminal_top-dots">
          <div class="terminal_top-dot is-red"></div>
          <div class="terminal_top-dot is-yellow"></div>
          <div class="terminal_top-dot is-green"></div>
        </div>
        <p class="terminal_top-title">Use terminal to find your package</p>
        <div class="terminal_top-spacer"></div>
      </div>
      <div class="terminal_bottom">
        <p class="terminal_bottom-intro">Click any line to jump to that service</p>
        <p class="terminal_bottom-prompt">Hello! What do you need help with?</p>
        <div class="terminal_bottom-options">
          <p class="terminal_bottom-option">› 🚀 I'm building a startup</p>
          <!-- ... -->
        </div>
      </div>
    </div>
    <a class="home-header_cta">View case studies</a>
  </div>
</div>
```

Notice the naming:
- `home-header_*` classes belong to the home header section (label, H1, content, logo, cta)
- `terminal_*` classes belong to the reusable terminal component (window, top, bottom, dots, option)
- `is-red`, `is-yellow`, `is-green` are combo classes on `terminal_top-dot` (variants)

### Section-vs-component cheat sheet

| Naming | Usage | Example |
|--------|-------|---------|
| `section_{name}` | Section tag custom class, one per section | `section_home-header`, `section_pricing` |
| `{name}_layout` | The flex/grid engine inside a section | `home-header_layout`, `pricing_layout` |
| `{component}_component` | Outer wrapper of a reusable component inside a section | `terminal_component`, `card_component` |
| `{component}_layout` or `{component}_wrapper` | Internal layout engine of a reusable component | `terminal_layout`, `card_wrapper` |
| `{component}_top` / `_bottom` / `_header` / `_body` | Internal structural divisions of a component | `terminal_top`, `card_header` |
| `is-{variant}` | Combo class for a variant | `is-red`, `is-primary`, `is-featured` |

## Body-level defaults vs section overrides

Body handles the site-wide background, text color, and font. Sections only override when they genuinely differ.

**Dark-theme site example:**
- Body: `background-color = background-primary (black)`, `color = text-primary (alabaster)`, `font-family = sans`
- `section_home-header`: NO background-color (inherits body black) ✓
- `section_pricing`: `background-color = Cornsilk` (light card section — explicit override) ✓
- H1: NO color set (inherits body → text-primary = alabaster) ✓
- Logo text: NO color set (inherits body) ✓
- Eyebrow label: `color = Natural Linen` (explicitly different from text-primary) ✓
- Highlight span: `color = Blanched Almond` (explicit accent) ✓

**Rule of thumb:** if the value matches text-primary or background-primary, don't set it. Only set colors when they differ from the body default. Every redundant override is another thing to maintain during a rebrand.

## Variable-first MCP workflow

When bootstrapping via the Webflow MCP, execute in this exact order:

1. `data_sites_tool list_sites` → confirm site ID and workspace
2. `de_page_tool switch_page` → **switch to Style Guide page first** (source of truth)
3. `variable_tool create_variable_collection` → Typography (if needed)
4. `variable_tool create_variable_mode` × 3 → Tablet, Mobile Landscape, Mobile Portrait (Base is implicit)
5. `variable_tool create_color_variable` × N → brand, neutral, system, scoped groups
6. `variable_tool create_font_family_variable` × N → serif, sans, mono
7. `variable_tool create_number_variable` × N → font weights, line heights
8. `variable_tool create_size_variable` × N → per-level font sizes, then `update_size_variable` with `mode_id` to set per-mode values
9. `style_tool update_style` → Body tag: bind `background-color`, `color`, `font-family`, `line-height` to variables
10. `style_tool update_style` → `heading-style-display-1` through `heading-style-h6`: bind `font-family`, `font-size`, `font-weight`, `line-height` via `variable_as_value`. Remove any legacy breakpoint overrides on `small` / `medium`.
11. `style_tool update_style` → `text-size-*`, `text-color-*`, `text-weight-*` utilities: bind to variables
12. `style_tool create_style` or `update_style` → custom section + component classes (`section_*`, `*_layout`, `*_component`, etc.) with `variable_as_value` for color/font/size/weight/line-height
13. Verify the Style Guide page renders correctly at all 4 breakpoints
14. `de_page_tool switch_page` → switch to the target page (e.g., Home)
15. `element_builder` or `whtml_builder` → build the element tree referencing pre-created class names. **Do NOT pass inline CSS with `whtml_builder`** — reference existing styles by class name only.

## Critical MCP gotcha: raw CSS vs native variable bindings

**`whtml_builder` with inline CSS creates Webflow styles from the CSS rules, but font-family and other variable references become raw CSS `var(--_typography---font-family--sans)` strings**. These strings do not render correctly in the Designer canvas because Webflow doesn't recognize them as proper variable bindings.

**Always pre-create styles via `style_tool` with `variable_as_value` before building elements.** The correct workflow:

```
1. style_tool create_style or update_style
   └─ properties: [{property_name: "font-family", variable_as_value: "variable-xxx"}]

2. whtml_builder or element_builder
   └─ HTML only (class names reference pre-created styles), no CSS
```

If you accidentally create styles via whtml_builder's CSS, fix them with `style_tool update_style` passing `variable_as_value` — the update replaces the raw CSS value with a proper binding.

## Scoped component color groups (example workflow)

When building a component with colors that aren't part of the brand palette:

1. Open Style Guide page
2. Create new variables in a scoped group: `variable_tool create_color_variable` with name `Terminal/red`, value `#ff5f57`
3. Create or update the component's dot class + combos:
   ```
   terminal_top-dot (base): width, height, border-radius, flex-shrink
   terminal_top-dot.is-red (combo): background-color → Terminal/red
   terminal_top-dot.is-yellow (combo): background-color → Terminal/yellow
   terminal_top-dot.is-green (combo): background-color → Terminal/green
   ```
4. Apply in HTML: `<div class="terminal_top-dot is-red"></div>`

The scoped group lives in the same collection as brand, neutral, system. Webflow's variable panel groups them by the `/` prefix automatically.

## Things to avoid (anti-patterns to reject in code review)

1. **Raw CSS `var(--...)` strings** in style properties → use native Webflow variable bindings (`variable_as_value`)
2. **Background-color on a section that matches body default** → inherit from body, only override when different
3. **Text color on headings that should inherit text-primary** → inherit from body
4. **Editing global classes from a target page** → always edit from Style Guide page
5. **Modifying CF v2 utility classes** (`container-large`, `padding-global`, `padding-section-*`) → these ship with the clonable and stay untouched
6. **Hardcoded hex values on styles** → everything flows through variables
7. **px values anywhere except 1px borders** → rem everything (11px → 0.6875rem, 580px → 36.25rem, etc.)
8. **Margin or padding on text elements for layout** → spacing is gap on the layout class
9. **Custom classes on H1/H2/P for sizing or color** → use heading-style combos + utility classes
10. **Auto-named combo classes** (`heading-style-h2-copy-1`, `button-5`, `center-aligned-7`) → rename semantically or delete
11. **Changing heading HTML tag to match visual size** → keep semantic tag, use heading-style combo
12. **Per-breakpoint Designer overrides on heading-style classes** → override via variable mode
13. **Starting from a blank project** → always clone Client-First v2
14. **Creating styles first, then variables** → variables first, always
15. **Hardcoded font-family strings in Typography variables** → the variable value holds the font stack, styles bind to the variable
16. **Using `_component` / `_layout` on the section itself** → `section_{name}` for the section, `_component` / `_layout` / `_top` / `_bottom` for elements inside
17. **Skipping the 4-layer wrapper** (section → padding-global → container → layout) → always use all four layers; the padding and container layers are CF v2's contract
