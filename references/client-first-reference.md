# Client-First Reference Guide (Compressed)

Source: Finsweet Client-First v2 Documentation
Compiled for actionable Webflow development use.

---

## 1. CLASS NAMING RULES

### The Underscore Rule
- `_` (underscore) in class name = **Custom class** (specific element/component)
- No underscore = **Utility class** (global CSS property)

### Custom Class Format
`folder_element-name` or `folder_subfolder_element-name`

Examples:
- `about-team_component`
- `footer_column`
- `nav_link`
- `home-header_texture`
- `form_component`, `form_wrapper`, `form_input`, `form_submit`
- `team-list_headshot-wrapper`

### Utility Class Format
`keyword-keyword-identifier` (dashes only, no underscores)

Examples:
- `padding-global`
- `text-color-primary`
- `margin-large`
- `background-color-dark`
- `display-none`

### Naming Principles
- General to specific: `text-size-large` not `large-size-text`
- No abbreviations, no shorthand
- Class name should answer: "What is this doing in the project?"
- A non-technical person should understand the class purpose
- Keywords enable search in Styles panel and clean Folders organization

### Combo Class Rules (is- prefix)
- Combo class = variant on top of a base class
- Always use `is-` prefix: `button is-brand`, `section_header is-home`
- `is-` class does NOT work alone, only combined with base class
- Must have clear benefit for inheriting base class styles
- If no inheritance benefit, use a single custom class instead

---

## 2. CORE STRUCTURE PATTERN

Every page follows this nesting structure:

```
page-wrapper                    (Div, wraps entire page)
  [nav component]               (header tag)
  main-wrapper                  (main tag, wraps main content, NOT nav/footer)
    section_[identifier]        (section tag, e.g. section_header, section_about)
      padding-global + padding-section-[size]  (SAME div block, both classes)
        container-[size]        (Div, max-width container)
          [your content]        (custom classes from here)
    section_[identifier]
      padding-global + padding-section-[size]
        container-[size]
          [your content]
  [footer component]            (footer tag)
```

### Core Structure Classes

| Class | Purpose | Styles |
|-------|---------|--------|
| `page-wrapper` | Wraps entire page | Optional (e.g. overflow:hidden) |
| `main-wrapper` | Main content area (a11y) | `<main>` tag, optional styles |
| `section_[name]` | Navigator organization | `<section>` tag, minimal styles |
| `padding-global` | Site-wide left/right padding | padding-left + padding-right only |
| `padding-section-small` | Section vertical spacing | padding-top + padding-bottom |
| `padding-section-medium` | Section vertical spacing | padding-top + padding-bottom |
| `padding-section-large` | Section vertical spacing | padding-top + padding-bottom |
| `container-large` | Content max-width | margin:0 auto, width:100%, max-width |
| `container-medium` | Content max-width | margin:0 auto, width:100%, max-width |
| `container-small` | Content max-width | margin:0 auto, width:100%, max-width |

Key: `padding-section-[size]` goes on the SAME div as `padding-global` (reduces nesting).

### Section Styling
- Avoid styling `section_[name]` directly
- For dark sections, add global combo: `section_header` + `section-style-dark`

---

## 3. REM SYSTEM

### Conversion: 1rem = 16px (browser default)
- px to rem: divide by 16 (`64px / 16 = 4rem`)
- rem to px: multiply by 16 (`2rem * 16 = 32px`)
- In Webflow Designer: type `100/16rem` in input field

### Approved rem Values

| px | rem | | px | rem |
|----|-----|-|----|-----|
| 2 | 0.125 | | 64 | 4 |
| 4 | 0.25 | | 80 | 5 |
| 8 | 0.5 | | 96 | 6 |
| 16 | 1 | | 128 | 8 |
| 20 | 1.25 | | 160 | 10 |
| 24 | 1.5 | | 192 | 12 |
| 32 | 2 | | 256 | 16 |
| 40 | 2.5 | | 320 | 20 |
| 48 | 3 | | 640 | 40 |
| 56 | 3.5 | | 1280 | 80 |

### Exceptions
1. **14px** (0.875rem) for typography when 16px is too large
2. **2px** (0.125rem) for tiny spacing
3. **1px stays 1px** for borders (no rem conversion, retina scaling)

---

## 4. TYPOGRAPHY SYSTEM

### Priority Order
1. Style HTML tags first (body, H1-H6) with defaults
2. Only add classes when there is a variation from default
3. Use utility classes for variations
4. Use custom class only for unique/specific text

### Typography Utility Classes

**Heading style switch** (apply to any heading to change its visual style):
- `heading-style-h1` through `heading-style-h6`
- Does NOT change the HTML tag, only the CSS styles

**Text size:**
- `text-size-large`, `text-size-medium`, `text-size-regular`, `text-size-small`, `text-size-tiny`

**Text weight:**
- `text-weight-xbold`, `text-weight-bold`, `text-weight-semibold`, `text-weight-normal`, `text-weight-light`

**Text style:**
- `text-style-allcaps`, `text-style-italic`, `text-style-link`, `text-style-muted`, `text-style-nowrap`, `text-style-quote`, `text-style-strikethrough`, `text-style-2lines`, `text-style-3lines`

**Text color:**
- `text-color-primary`, `text-color-secondary`, `text-color-alternate`

**Text alignment:**
- `text-align-left`, `text-align-center`, `text-align-right`

### When to Use Custom Typography Class
- Unique/specific text (e.g. `footer_copyright-text`)
- Managing a group of recurring text (e.g. `footer_link`)
- Custom responsive behavior needed on mobile

---

## 5. SPACING SYSTEM

### Part 1: Utility Class Spacing

**Spacing Block Strategy (Option 1 - recommended):**
- `spacer-[size]` - single class on empty Div Block between siblings
- Supports responsive variants: `spacer-large is-mobile-small`

**Spacing Block Strategy (Option 2):**
- `padding-bottom` + `padding-[size]` - two classes on empty Div Block

**Spacing Wrapper Strategy:**
- `margin-[direction]` + `margin-[size]` on Div wrapping a child element
- `padding-[direction]` + `padding-[size]` on Div wrapping a child element

### Margin Direction Classes
`margin-top`, `margin-bottom`, `margin-left`, `margin-right`, `margin-horizontal`, `margin-vertical`

### Padding Direction Classes
`padding-top`, `padding-bottom`, `padding-left`, `padding-right`, `padding-horizontal`, `padding-vertical`

### Size Classes (same for margin- and padding-)

| Class | Value |
|-------|-------|
| `[m/p]-0` | 0rem |
| `[m/p]-tiny` | 0.125rem (2px) |
| `[m/p]-xxsmall` | 0.25rem (4px) |
| `[m/p]-xsmall` | 0.5rem (8px) |
| `[m/p]-small` | 1rem (16px) |
| `[m/p]-medium` | 2rem (32px) |
| `[m/p]-large` | 3rem (48px) |
| `[m/p]-xlarge` | 4rem (64px) |
| `[m/p]-xxlarge` | 5rem (80px) |
| `[m/p]-huge` | 6rem (96px) |
| `[m/p]-xhuge` | 8rem (128px) |
| `[m/p]-xxhuge` | 12rem (192px) |
| `[m/p]-custom1` | 1.5rem (24px) |
| `[m/p]-custom2` | 2.5rem (40px) |
| `[m/p]-custom3` | 3.5rem (56px) |

### Special Classes
- `spacing-clean` - resets all margin and padding to 0
- `spacer_[element]` - custom class folder for element-specific global spacing

### Part 2: Custom Class Spacing
- **Custom class on element**: Apply margin/padding directly (e.g. `faq_title` with margin-bottom)
- **CSS Grid parent**: Apply display:grid on parent, use row-gap/column-gap
- Use custom class when: globally managing a specific recurring element, unique mobile spacing, one-off values

### Spacing Tips
- Do NOT put margin/padding utility classes directly on text elements (causes deep stacking)
- Use flex/grid for button rows, not margin on button class
- Do NOT use utility padding classes for inner element sizing
- `padding-section-[size]` goes on SAME div as `padding-global`

---

## 6. COMPLETE UTILITY CLASS INVENTORY

### Structure
- `padding-global`
- `container-large`, `container-medium`, `container-small`
- `padding-section-small`, `padding-section-medium`, `padding-section-large`

### Typography
- `heading-style-h1` through `heading-style-h6`
- `text-size-large`, `text-size-medium`, `text-size-regular`, `text-size-small`, `text-size-tiny`
- `text-weight-xbold`, `text-weight-bold`, `text-weight-semibold`, `text-weight-normal`, `text-weight-light`
- `text-style-allcaps`, `text-style-italic`, `text-style-link`, `text-style-muted`, `text-style-nowrap`, `text-style-quote`, `text-style-strikethrough`, `text-style-2lines`, `text-style-3lines`
- `text-color-primary`, `text-color-secondary`, `text-color-alternate`
- `text-align-left`, `text-align-center`, `text-align-right`

### Buttons
- `button` (base), `button is-secondary`, `button is-text`

### Spacing (see Section 5 for full list)
- All margin-[direction], margin-[size] classes
- All padding-[direction], padding-[size] classes
- `spacing-clean`
- `spacer-[size]` classes

### Responsive Hide
- `hide` (all devices)
- `hide-tablet` (from tablet down)
- `hide-mobile-landscape` (from mobile landscape down)
- `hide-mobile-portrait` (mobile portrait only)

### Display
- `display-inlineflex` (inline-flex, useful for buttons)

### Max Width
- `max-width-xxlarge` (80rem), `max-width-xlarge` (64rem), `max-width-large` (48rem)
- `max-width-medium` (32rem), `max-width-small` (20rem), `max-width-xsmall` (16rem), `max-width-xxsmall` (12rem)
- `max-width-full` (none), `max-width-full-tablet`, `max-width-full-mobile-landscape`, `max-width-full-mobile-portrait`

### Icon Sizes
- `icon-height-small`, `icon-height-medium`, `icon-height-large`
- `icon-1x1-small`, `icon-1x1-medium`, `icon-1x1-large`

### Background Colors
- `background-color-primary`, `background-color-secondary`, `background-color-tertiary`, `background-color-alternate`

### Advanced Utilities
- `z-index-1`, `z-index-2`
- `align-center` (margin-left/right auto)
- `layer` (position:absolute, 0% all sides)
- `pointer-events-none`, `pointer-events-auto`
- `overflow-hidden`, `overflow-scroll`, `overflow-auto`
- `aspect-ratio-square` (1/1), `aspect-ratio-portrait` (2/3), `aspect-ratio-landscape` (3/2), `aspect-ratio-widescreen` (16/9)
- `inherit-color` (color:inherit on all children)

---

## 7. FOLDER SYSTEM

### Custom Class Folders (underscore-based)
- One `_` = one folder level: `nav_link` -> nav folder > link
- Two `_` = nested: `home_slider_arrow` -> home > slider > arrow
- No limit on nesting depth

### Utility Class Folders (automatic keyword matching)
- Classes without `_` go into Utility folder
- Grouped by matching first keyword: `text-color-primary` + `text-size-large` -> text folder
- Second keyword creates subfolder when 2+ classes share first two keywords

### Folder Strategy Rules
- Only nest folders when there is clear organizational benefit
- Small projects: one folder level is fine
- Large projects: nested folders help
- Use `_component` keyword for complete UI components: `client-slider_component`
- Page name in class = page-specific element (don't reuse on other pages)
- General keyword = reusable globally

### Folder Organization Options
1. **General folder**: `slider_arrow` (reusable anywhere)
2. **Specific folder**: `home-slider_arrow` (page-specific)
3. **Page folder**: `home_slider_arrow` (nested, page-first)
4. **Component folder**: `slider_1_component` (library style)

---

## 8. DEEP STACKING RULES

### The Rule: Don't Deep Stack
- 1-2 classes: Great, common practice
- 3 classes: Acceptable but question if necessary
- 4 classes: Absolute maximum
- 5+ classes: Too many, create a custom class instead

### Why No Deep Stacking
- Can't reorder classes in Webflow Styles panel
- Difficult edits on lower breakpoints
- Slower workflow
- Higher learning curve

### Solutions to Avoid Deep Stacking
1. Use one single custom class instead
2. Nest another Div Block (separate concerns into layers)
3. Create a combo class: `section_header is-home-header` instead of 4 stacked classes

### Stacking Rules
- Stack similar classes only (margin with margin, text with text)
- Never add new styles to stacked global utility classes (creates unwanted combo)
- Don't mix property types on one element

---

## 9. NO LAYOUT SYSTEM

Client-First does NOT include flex, grid, column, or layout utility classes.

- Create layouts with custom classes using flex/grid
- Optional: Create `grid_col-2`, `grid_col-3` as global layout classes with `is-` combos for responsive variants
- Never use abbreviated layout classes like `col-2-d`, `flex-a-l-j-c`

---

## 10. VARIABLES (Colors)

### Primitive Tokens
- Base color values, prefixed with `- Base Color:`
- Groups: Brand colors, Neutral colors, System colors
- Do NOT link primitives directly to classes (use semantic tokens)

### Semantic Tokens
- Named by purpose, not color: `background-color--background-primary`
- Naming: `[element]-[style]-[identifier]` (e.g. `text-color-primary`, `border-color-brand`)
- Do NOT use color name in semantic variable name
- Utility classes linked to semantic tokens:
  - `background-color-primary` -> var(background-color--background-primary)
  - `text-color-primary` -> var(text-color--text-primary)
- Avoid linking text tag directly to semantic variable (loses parent color inheritance)
- Use `inherit-color` utility class to override and inherit parent color

---

## 11. INTERACTIONS NAMING

### Format: `Element [Action + State]`
- Examples: `Nav Menu [Open]`, `Button Arrow [Move In]`, `Home Hero Lottie [Show]`
- Capitalize first letter of each word
- Keep names short (don't overflow Designer panel)
- Use square brackets to separate Action/State from Element

### Keywords
- **Action**: Show, Hide, Move, Rotate, Scale
- **State**: In/Out, Open/Close, Increase/Decrease, Expand/Collapse
- **Responsive** (optional): `[Mobile]`, `[Desktop]`, `[Tablet Mobile]`
- **Trigger** (use sparingly): Click, Hover, Scroll, Load

---

## 12. FLUID RESPONSIVE

### Method: Root-font scaling (NOT vw/vh)
- All sizes in rem, relative to HTML font-size
- Modify HTML font-size across breakpoints using CSS calc()
- Add generated CSS to global embed

### Default CSS snippet:
```css
html { font-size: calc(0.625rem + 0.41666vw); }
@media screen and (max-width:1920px) { html { font-size: calc(0.625rem + 0.41666vw); } }
@media screen and (max-width:1440px) { html { font-size: calc(0.8127rem + 0.2081vw); } }
@media screen and (max-width:479px) { html { font-size: calc(0.7495rem + 0.8368vw); } }
```

### Benefits over vw/vh
- No workflow changes needed
- Browser zoom works correctly
- Browser font-size settings respected
- Can be added at end of project

---

## 13. SEMANTIC HTML TAGS

### Required Tags
| Element | Tag | Client-First Class |
|---------|-----|--------------------|
| Nav component | `<header>` | nav_component |
| Main content | `<main>` | main-wrapper |
| Footer | `<footer>` | footer_component |
| Content sections | `<section>` | section_[name] |

### Heading Rules
- One `<h1>` per page (page title)
- Never skip heading levels (h1 -> h2 -> h3, not h1 -> h3)
- Use `heading-style-h#` to change visual style while keeping correct tag for SEO
- Don't use heading tags for non-heading content

### Other Semantic Tags
- `<article>` for self-contained content (blog posts, product cards, etc.)
- `<aside>` for sidebars, related content
- `<nav>` for navigation link groups
- `<figure>` for illustrations, diagrams, photos
- `<address>` for contact information

---

## 14. ACCESSIBILITY

### Key Practices
- Use rem units (browser zoom and font-size settings work correctly)
- Use semantic HTML tags
- Style focus-visible state on all focusable elements
- Add tabindex="0" to clickable non-link/button elements
- Use `role="button"` on divs acting as buttons
- Add aria-label to elements without descriptive content
- Use aria-hidden="true" on decorative elements
- Don't skip heading levels

### Global Embed Focus State
```css
*[tabindex]:focus-visible,
input[type="file"]:focus-visible {
  outline: 0.125rem solid var(--base-color-system--focus-state);
  outline-offset: 0.125rem;
}
```

---

## 15. GLOBAL STYLES EMBED

Required CSS in symbol embed block on every page:

1. **Text clarity**: `-webkit-font-smoothing: antialiased`
2. **Focus state**: Unified keyboard focus outline
3. **Rich Text margins**: Remove top margin from first element, bottom margin from last
4. **Container centering**: Force `margin: 0 auto !important` on all containers
5. **!important declarations**: Force hide classes, margin/padding direction classes to maintain intended behavior
6. **inherit-color**: `.inherit-color * { color: inherit; }`

---

## 16. DO'S AND DON'TS

### DO
- Use rem for all measurements
- Style HTML tags (body, H1-H6) before creating classes
- Use `_` for custom classes (creates folders)
- Use `-` only for utility classes
- Keep classes to 1-2 per element (max 4)
- Use `is-` prefix for all combo classes
- Name classes general-to-specific
- Use `padding-global` + `padding-section-[size]` on same div
- Use spacing blocks/wrappers to separate spacing from content classes
- Use CSS Grid for spacing lists of equal items
- Use custom classes for layouts (flex/grid)
- Add semantic HTML tags
- Use color variables (semantic tokens, not primitives)
- Put Global Styles embed on every page

### DON'T
- Deep stack 5+ classes
- Add new styles to stacked utility classes
- Use utility classes for layout (no flex/grid utilities)
- Put spacing classes on text elements
- Use vw/vh for sizing (use rem + fluid responsive)
- Skip heading levels
- Use px (except for 1px borders)
- Create abbreviated/shorthand class names
- Use page-name classes on other pages
- Apply inner padding with utility classes
- Link primitive color tokens directly to classes
- Name semantic variables with color names
- Use `position-absolute` as a utility class
