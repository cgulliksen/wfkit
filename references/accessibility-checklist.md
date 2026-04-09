# Accessibility Checklist for Webflow Sites

Baseline: WCAG 2.2 AA plus Iverksetter house conventions. This is the checklist the agent verifies during every build, not a pre-launch afterthought.

## The 25-item baseline

Every Webflow build must hit these 25 items:

### Structure & semantics

1. **Single `<h1>` per page** — no skipped heading levels (H1 → H2 → H3, never H1 → H3)
2. **Semantic tag + heading-style combo** for visual-only changes — keep H1 for SEO, style it as H2 visually: `<h1 class="heading-style-h2">`
3. **Skip link** as first focusable element, targeting `#main-content`
4. **`lang` attribute** on `<html>` (e.g., `no` for Norwegian bokmål, `en` for English)
5. **Landmark regions**: `<nav>`, `<main>`, `<section>`, `<footer>`, `<aside>` (Webflow Div Blocks let you set any tag)

### Interactive & keyboard

6. **`:focus-visible` keyboard focus**, ≥2px outline at 3:1 contrast (WCAG 2.4.13 Focus Appearance)
7. **Target size ≥24×24 CSS pixels**, prefer 44×44 (WCAG 2.5.8 Target Size Minimum)
8. **Tab order follows visual order** — use Webflow's default DOM order, avoid `tabindex > 0`
9. **`prefers-reduced-motion: reduce`** respected on ALL animations (IX2, Lottie, Rive, GSAP, CSS)
10. **Alt text on every image**; check CMS alt fields are populated, not `__wf_reserved_inherit`
11. **Decorative icons** get `aria-hidden="true"` + empty alt
12. **Semantic `<button>` for buttons** — Webflow Buttons render as `<a href="#">`; use HTML embed for true `<button>` elements when actions matter

### Forms

13. **Explicit `<label for="id">`** on every form field (Webflow's native Label element doesn't always associate — verify in DOM)
14. **`aria-invalid="true"`** on invalid fields
15. **Error messages** linked via `aria-describedby` to the field
16. **`fieldset`/`legend`** for radio and checkbox groups
17. **No redundant re-entry** across multi-step forms (WCAG 3.3.7)

### Components

18. **`aria-expanded` + `aria-controls`** on collapsible triggers (hamburger, accordion, dropdown)
19. **Modal focus trap** + ESC close + `role="dialog"` + `aria-modal="true"`
20. **Video captions** (human-written, not auto-generated)
21. **SVG**: `role="img"` + `aria-label` if meaningful, `aria-hidden="true"` if decorative

### Color & contrast

22. **WCAG AA color contrast**: 4.5:1 normal text, 3:1 large text (≥24px or ≥19px bold), 3:1 UI components and borders
23. **No state communicated by color alone** — pair with icons, text, or patterns
24. **Touch targets spaced ≥8px apart**

### Testing

25. **Tested with keyboard + screen reader** before launch (VoiceOver on Mac, NVDA on Windows)

---

## Language and document structure

- **Page language**: Set `lang` attribute on every page. Options:
  - Project settings → Custom code → head: `<script>document.documentElement.lang = 'no';</script>`
  - Or set a custom attribute on the `<html>` element via embed
- **Logical heading structure**: One H1 per page, followed by H2s, H3s, etc. Never skip levels.
- **Decouple visual from semantic**: An H2 can look like an H4 visually using `heading-style-h4` combo. The Iverksetter house style relies on this heavily.

## Skip link

Add as the first element inside `<body>`. Visually hidden until focused.

```html
<a href="#main-content" class="skip-link">Skip to main content</a>
```

Global Styles CSS embed:
```css
.skip-link {
  position: absolute;
  top: -100%;
  left: 0;
  padding: 0.5rem 1rem;
  background: var(--_color---bg-primary);
  color: var(--_color---text-primary);
  z-index: 9999;
  text-decoration: none;
  border-radius: 0.25rem;
}
.skip-link:focus {
  top: 0;
}
```

Give the `<main>` element `id="main-content"` to match the skip target.

## Images and media

- **Alt text on all images**: descriptive, conveys the image's purpose. Not "image of" or "photo of".
- **Decorative images**: empty alt attribute (not missing). In Webflow, leave the alt field blank but the field must exist.
- **CMS images**: Check the CMS alt field is populated. Webflow's fallback is `__wf_reserved_inherit` which is not descriptive.
- **No auto-playing media**: Video and audio must not play automatically. Provide play/pause controls.
- **Captions on video**: Human-written captions on all video content. YouTube auto-captions are not sufficient.
- **Respect reduced motion**: Wrap every animation.

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

Add to Global Styles CSS embed.

## Color and contrast

- **Normal text**: minimum 4.5:1 ratio against background (WCAG AA)
- **Large text** (≥24px regular or ≥19px bold): minimum 3:1 ratio
- **UI components** (borders, icons, interactive boundaries): minimum 3:1 ratio
- **Don't rely on color alone**: pair with icons, text, or patterns
- **Test in grayscale**: content should be understandable without color

Tools: WebAIM Contrast Checker, Chrome DevTools, axe DevTools extension.

## Keyboard navigation

- **All interactive elements keyboard-accessible**: links, buttons, form fields, dropdowns, modals, tabs
- **`:focus-visible` outline** for keyboard users — never set `outline: none` without providing an alternative

```css
:focus-visible {
  outline: 2px solid var(--_color---brand-primary);
  outline-offset: 2px;
  border-radius: 0.25rem;
}
```

- **Logical tab order**: follows visual layout. Avoid custom `tabindex > 0`.
- **No keyboard traps**: users must be able to Tab into AND out of every component (exception: open modals).

## Semantic buttons via HTML embed

Webflow "Button" elements render as `<a href="#">`. For actions that aren't navigation (modal triggers, form submits, toggles), use a true semantic button via HTML embed:

```html
<button type="button" class="button" aria-label="Open menu">
  Menu
</button>
```

Or wrap a Div Block with a custom attribute `tag="button"` via the Designer's "Custom Attributes" panel — but verify the rendered HTML is actually a `<button>`.

## ARIA and landmarks

### Landmarks
- `<nav>` for all navigation regions
- `<main>` for the primary content area (one per page)
- `<footer>` for the page footer
- `<header>` for the page header
- `<section>` for thematic sections with an accessible name
- `<aside>` for complementary content (sidebars, callouts)

### ARIA labels
- **`aria-label`** on icon-only buttons (hamburger, close, social icons)
- **`aria-expanded`** on toggle triggers (menu, dropdown, accordion)
- **`aria-controls`** pointing to the controlled element's ID
- **`aria-hidden="true"`** on decorative icons and anything invisible to screen readers
- **`aria-live`** on dynamic content regions that update without page reload (form success/error, search results count)

## Forms

### Labels
- Every form field has a visible `<label>` element paired with its input via `for` / `id`
- In Webflow: use the Label element and verify the `for` attribute matches the input `id` in the DOM
- Placeholder supplements, never replaces, a label

### Required fields
- Mark with visible text (not just an asterisk)
- Add `aria-required="true"`
- Use `required` HTML attribute for native browser validation

### Error states
- Specific error messages ("Enter a valid email address", not "Invalid input")
- `aria-invalid="true"` on the invalid field
- Error message linked via `aria-describedby` to the field
- Error message visible AND announced to screen readers

```html
<label for="email">Email</label>
<input type="email" id="email" aria-invalid="true" aria-describedby="email-error" required>
<div id="email-error" role="alert">Please enter a valid email address.</div>
```

### Radio + checkbox groups
- Wrap in `<fieldset>` with a `<legend>` describing the group

### Success confirmation
- Form submission success clearly communicated, not just a subtle visual change
- Use `role="status"` or `aria-live="polite"` on the success message container

### Autocomplete
- Add `autocomplete` values to form fields (name, email, tel, street-address, etc.)

### Redundant entry (WCAG 3.3.7)
- Don't require users to re-enter information they've already provided in a multi-step form

## Interactive components

### Modals / dialogs
- Focus moves to the modal when it opens
- Focus is trapped inside the modal while open (Tab cycles within, Shift+Tab cycles backwards)
- ESC key closes the modal
- Focus returns to the trigger element when the modal closes
- Background content gets `aria-hidden="true"` while modal is open
- Modal itself has `role="dialog"` + `aria-modal="true"` + `aria-labelledby` pointing to the title
- Consider Finsweet `fs-modal` — it handles static semantics automatically

Minimal focus trap snippet:
```javascript
function trapFocus(modal) {
  const focusable = modal.querySelectorAll(
    'a[href], button, textarea, input, select, [tabindex]:not([tabindex="-1"])'
  );
  const first = focusable[0];
  const last = focusable[focusable.length - 1];

  modal.addEventListener('keydown', (e) => {
    if (e.key === 'Escape') closeModal(modal);
    if (e.key !== 'Tab') return;
    if (e.shiftKey && document.activeElement === first) {
      e.preventDefault();
      last.focus();
    } else if (!e.shiftKey && document.activeElement === last) {
      e.preventDefault();
      first.focus();
    }
  });
}
```

### Accordions / dropdowns
- Trigger button has `aria-expanded="true|false"`, toggled on click
- Trigger has `aria-controls` pointing to the panel ID
- Panel has matching ID
- Keyboard operable (Enter or Space to toggle)
- Consider Finsweet `fs-a11y` — it manages `aria-expanded`, `aria-controls`, `aria-hidden` dynamically

### Carousels / sliders
- Pause/stop controls available
- Current slide indicated ("Slide 2 of 5") via text or `aria-live`
- Previous/next controls keyboard-accessible
- Respect `prefers-reduced-motion` — disable auto-advance

## Finsweet attributes for accessibility

- **`fs-a11y`**: manages dynamic ARIA state (`aria-expanded`, `aria-controls`, `aria-hidden`) in response to component state changes. Doesn't inject static base ARIA.
- **`fs-modal`**: adds static semantic attributes (`role="dialog"`, `aria-modal="true"`) and can handle focus trap basics.
- **`fs-accordion`**: basic ARIA for accordion components — verify in DOM that `aria-expanded` is toggled correctly.
- **`fs-tabs`**: basic ARIA for tab components.

Verify whatever these attributes generate in the rendered DOM — don't trust attributes alone. Many still need manual completion for full conformance.

## European Accessibility Act (EAA) 2025

The EAA took effect June 28, 2025. It applies to:
- E-commerce websites and apps
- Banking and financial services
- Transport services
- E-books and e-readers
- Telecom services

### EAA requirements
- WCAG 2.1 AA (now functionally WCAG 2.2 AA given the criteria overlap)
- Accessible customer service channels
- Accessibility statement published on the website
- Process for handling accessibility complaints
- Regular accessibility testing and remediation

### Accessibility statement
Dedicated page at `/tilgjengelighet` or `/accessibility` with:
- Current level of conformance
- Known limitations and planned fixes
- Contact info for accessibility feedback
- Date of last accessibility review

### Norwegian context
Norway implements EAA through national law. Public sector sites also comply with WCAG under "likestillings- og diskrimineringsloven". Private sector sites serving EU/EEA customers must comply with EAA as of June 2025.

## WCAG 2.2 AA — criteria commonly missed

These are the new or frequently-overlooked WCAG 2.2 criteria:

- **2.4.11 Focus Not Obscured (Minimum)**: Focus indicators must not be completely hidden by other content
- **2.4.13 Focus Appearance**: Focus outline ≥2px thickness with 3:1 contrast against the element and background
- **2.5.7 Dragging Movements**: Any drag operation has a single-pointer alternative (tap, click, keyboard)
- **2.5.8 Target Size (Minimum)**: Interactive elements ≥24×24 CSS pixels (exception: inline text links)
- **3.2.6 Consistent Help**: Help mechanisms (chat, contact, FAQ) appear in the same location across pages
- **3.3.7 Redundant Entry**: Don't require users to re-enter previously provided info
- **3.3.8 Accessible Authentication (Minimum)**: Authentication doesn't depend on cognitive function tests (solving puzzles, remembering passwords from memory)
- **3.3.9 Accessible Authentication (Enhanced)** (AAA): Extended version of 3.3.8

## Testing process

### Manual testing
1. Navigate entire site using only keyboard — Tab, Shift+Tab, Enter, Space, Escape, Arrow keys
2. Test with screen reader (VoiceOver on Mac, NVDA on Windows)
3. Zoom to 200% and verify no content is lost or overlapping
4. Check pages in grayscale mode
5. Verify all forms complete without a mouse

### Automated testing
- Lighthouse accessibility audit in Chrome DevTools
- axe DevTools browser extension for detailed violations
- WebAIM Contrast Checker for color pairs
- HeadingsMap extension to validate heading structure

### Testing frequency
- Before every site launch
- After major content or design updates
- Quarterly review for ongoing sites
- When adding new interactive components
