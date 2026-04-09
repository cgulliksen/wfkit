# Example — Building a hero section end-to-end

This walkthrough shows what a real `/wfkit build` session looks like, using the Iverksetter 2026 homepage hero as the example. Use it as a reference for what the skill actually does under the hood.

## Context

- **Goal**: Build the homepage hero section on a fresh Webflow site
- **Design reference**: Figma file with logo, eyebrow label, H1 with highlight, description, terminal-style interactive component, CTA button
- **Stack**: Fresh Webflow site cloned from Client-First v2, Instrument Serif + Instrument Sans fonts installed via Google Fonts
- **Tools**: Claude Code + Webflow MCP + Figma MCP

## Session transcript (abbreviated)

### Session startup

```
/wfkit build home page hero section
```

The skill walks through startup:

1. `data_sites_tool list_sites` → confirms site ID and workspace
2. `de_page_tool get_current_page` → verifies active page (Style Guide)
3. `variable_tool get_variable_collections` → smoke-tests the MCP bridge and inventories existing collections
4. Verifies the Client-First v2 clonable is present by checking for `fs-styleguide_*` classes

### Phase 1 — Figma extraction

The user provides three Figma frame URLs. The skill uses Figma MCP to extract:

- **Fonts**: Instrument Serif (display/headings), Instrument Sans (body/UI), Courier New (terminal)
- **Brand colors**: Alabaster `#f2f0e4`, Blanched Almond `#fbefc9`, Natural Linen `#c2beb0`, Burnt Sienna `#e76e50`, Cornsilk `#fef2dc`
- **Neutral scale**: 500-950 (shadcn zinc)
- **Typography scale**: Display 1 96px, H1 56px, H2-H6 derived
- **Spacing tokens**: 4px / 12px / 16px / 24px / 32px / 48px / 64px

Tells the user: "These fonts need to be manually installed in Webflow project settings before we proceed."

### Phase 2 — Variables (on Style Guide page)

```
de_page_tool switch_page → Style Guide
```

All variable creation happens from Style Guide page context:

1. Create Typography collection + 4 modes (Base, Tablet, Mobile Landscape, Mobile Portrait)
2. Add brand colors to the default collection (Alabaster, Blanched Almond, Natural Linen, Burnt Sienna, Cornsilk)
3. Add neutral zinc scale (500-950)
4. Add scoped Terminal color group (Terminal/red, Terminal/yellow, Terminal/green)
5. Create Font Family variables (serif, sans, mono)
6. Create Font Weight variables (regular, medium, semibold, bold, extrabold)
7. Create Line Height variables (tight 1, normal 1.5)
8. Create per-level size variables (Display 1, H1-H6, Text Large/Medium/Regular/Small/Tiny) with mode-aware values

Responsive heading scale lives in the variable modes, not in per-breakpoint Designer overrides.

### Phase 3 — Body defaults + style guide bindings

Still on Style Guide page:

1. **Body tag bindings (FIRST)**:
   - `background-color` → `Background Color/background primary` variable
   - `color` → `Text Color/text primary` variable
   - `font-family` → `Font Family/sans` variable
   - `line-height` → `Line Height/normal` variable
2. Rebind `heading-style-display-1` (create if missing), `heading-style-h1` through `heading-style-h6` — all properties bound to Typography variables via `variable_as_value`
3. Remove legacy hardcoded breakpoint overrides on heading-style classes
4. Rebind semantic layer (Background Color/*, Text Color/*, Border Color/*, Link Color/*) to Iverksetter brand primitives

Verification: body bg cascades site-wide, headings render at correct sizes across all 4 breakpoints.

### Phase 5 — Build the hero section

Still on Style Guide page, create custom classes with proper variable bindings:

```
section_home-header          → (usually no properties)
home-header_layout           → flex column, align center, gap 7rem
home-header_logo             → font-family serif var, font-size 2rem, text-decoration none
home-header_content          → flex column, align center, gap 1.5rem, max-width 42rem, text-align center
home-header_label            → font-family sans var, font-size text-regular var, color natural-linen var, text-transform uppercase, letter-spacing 0.04em
home-header_h1               → (combo class on heading-style-display-1, no color override — inherits text-primary)
home-header_h1-highlight     → color blanched-almond var
home-header_description      → font-family sans var, font-size text-medium var, color natural-linen var, line-height normal var
home-header_cta              → border 1px solid #ffeaa6, border-radius 0.625rem, padding 0.5625rem 1.0625rem, font-family sans var, font-size text-small var, color blanched-almond var
```

Internal terminal component classes:

```
terminal_component           → flex column, align center, gap 1.5rem
terminal_window              → background-color neutral-950 var, border 1px solid neutral-800 var, border-radius 0.75rem, box-shadow, width 36.25rem, overflow hidden
terminal_top                 → flex row, padding 0.625rem 1rem, background-color neutral-900 var, border-bottom 1px solid neutral-800 var, gap 0.5rem
terminal_top-dots            → flex row, gap 0.5rem, flex-shrink 0
terminal_top-dot             → width 0.6875rem, height 0.6875rem, border-radius 9999px, flex-shrink 0
terminal_top-dot.is-red      → (combo) background-color Terminal/red var
terminal_top-dot.is-yellow   → (combo) background-color Terminal/yellow var
terminal_top-dot.is-green    → (combo) background-color Terminal/green var
terminal_top-title           → font-family mono var, font-size text-tiny var, color natural-linen var, flex 1, text-align center
terminal_top-spacer          → width 3.0625rem, flex-shrink 0
terminal_bottom              → flex column, gap 1rem, padding 1.5rem
terminal_bottom-intro        → font-family mono var, font-size text-tiny var, color natural-linen var
terminal_bottom-prompt       → font-family mono var, font-size text-small var, color burnt-sienna var
terminal_bottom-options      → flex column, gap 0.25rem
terminal_bottom-option       → font-family mono var, font-size text-small var, color natural-linen var, padding 0.375rem 0
```

All colors, fonts, sizes, line heights bound via `variable_as_value`. Structural sizes in rem (11px = 0.6875rem, 580px = 36.25rem). Only 1px borders stay as px.

### Switch to Home page and build the element tree

```
de_page_tool switch_page → Home
```

Then `whtml_builder` with HTML-only payload (no inline CSS) — the HTML references the pre-created class names:

```html
<section class="section_home-header">
  <div class="padding-global padding-section-large">
    <div class="container-large">
      <div class="home-header_layout">
        <a href="/" class="home-header_logo">Iverksetter</a>

        <div class="home-header_content">
          <h2 class="home-header_label">Growth Marketing · Tech · AI</h2>
          <h1 class="heading-style-display-1 home-header_h1">From MVP to <span class="home-header_h1-highlight">Growth</span></h1>
          <h3 class="home-header_description">Most startups can't afford a full team, and agencies are too slow and too expensive. Iverksetter is something in between. One partner, one subscription, full-stack growth.</h3>
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
              <p class="terminal_bottom-intro">Click any line below to jump to that service</p>
              <p class="terminal_bottom-prompt">Hello! What do you need help with?</p>
              <div class="terminal_bottom-options">
                <p class="terminal_bottom-option">› 🚀 I'm building a startup</p>
                <p class="terminal_bottom-option">› 📣 I need marketing</p>
                <p class="terminal_bottom-option">› 🧠 I want to learn to do it myself</p>
                <p class="terminal_bottom-option">› 💻 I need a web app</p>
              </div>
            </div>
          </div>
          <a href="#" class="home-header_cta">View case studies</a>
        </div>
      </div>
    </div>
  </div>
</section>
```

Verification: `element_snapshot_tool` on the section to confirm the render looks correct.

## Key learnings from this build

1. **Body-level defaults first**: Setting `background-color`, `color`, `font-family`, `line-height` on the body tag from Style Guide page means every section inherits them. No more redundant `background-color: black` on every section.
2. **4-layer wrapper structure**: Every section is `section_{name}` → `padding-global + padding-section-*` → `container-*` → `{name}_layout`. Four layers, one pattern.
3. **Internal components**: `terminal_component` / `terminal_top` / `terminal_bottom` suffixes are for elements INSIDE a section, never for the section itself. The section is `section_home-header`.
4. **Variable bindings via `variable_as_value`**: Not raw CSS `var(--...)` strings. Raw strings don't render correctly in Webflow Designer because they're not recognized as proper variable bindings.
5. **Scoped color groups**: Terminal-specific colors live in a `Terminal/` group (Terminal/red, Terminal/yellow, Terminal/green) instead of polluting the brand palette.
6. **Semantic headings**: H1 for the main heading, H2 for the SEO-valuable eyebrow label above H1, H3 for the description (even though it's paragraph-like) to give SEO weight to keyword-rich copy. Trade-off with screen-reader UX — document the choice.
7. **Style Guide is source of truth**: All class definitions edited from Style Guide page context. Home page (and every other page) only references classes, never defines them.
8. **Pixels to rem**: 11px → 0.6875rem, 580px → 36.25rem, 49px → 3.0625rem. Only 1px borders stay as px.
9. **Pre-create styles via `style_tool`, then build elements via `whtml_builder` with HTML only**. Passing CSS to `whtml_builder` creates styles with raw `var()` strings that don't render properly.
