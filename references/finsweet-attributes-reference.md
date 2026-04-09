# Finsweet Attributes Reference

Finsweet Attributes are script-powered, attribute-based solutions for Webflow. No custom JavaScript needed for most use cases. Each attribute loads via a single script tag and is configured entirely through custom attributes in the Webflow Designer.

## Script Loading

Add to site-level custom code (before </body>):
```html
<script async src="https://cdn.jsdelivr.net/npm/@finsweet/attributes-[name]@1/[name].js"></script>
```

Replace `[name]` with the attribute name (e.g., `cmsfilter`, `cmssort`, `listfilter`, `toc`).

## Callbacks (Run Code After FS Loads)

Use the official callback API to run code after a specific attribute initializes:
```js
window.fsAttributes = window.fsAttributes || [];
window.fsAttributes.push(['attributeName', function(instances) {
  // FS attribute is now loaded and initialized
}]);
```

Common attribute names for callbacks: `cmsfilter`, `cmssort`, `listfilter`, `cmsload`, `toc`.

---

## CMS Filter

Filter CMS Collection Lists with checkboxes, radio buttons, dropdowns, range sliders, and text search.

### Key Attributes
| Attribute | Element | Purpose |
|-----------|---------|---------|
| `fs-cmsfilter-element="list"` | Collection List Wrapper | Identifies the list to filter |
| `fs-cmsfilter-element="filter"` | Input/Select/Checkbox | The filter control |
| `fs-cmsfilter-field="fieldName"` | Filter control | Which CMS field to filter by |
| `fs-cmsfilter-element="empty"` | Any element | Shown when zero results match |
| `fs-cmsfilter-element="results-count"` | Text element | Displays count of visible items |
| `fs-cmsfilter-active="true/false"` | Filter control | Pre-activate a filter on load |
| `fs-cmsfilter-animationin="fade"` | Collection List | Animation when items appear |
| `fs-cmsfilter-animationout="fade"` | Collection List | Animation when items disappear |

### Text Search in CMS Filter
| Attribute | Purpose |
|-----------|---------|
| `fs-cmsfilter-field="*"` | Search across all fields |
| `fs-cmsfilter-field="title,description"` | Search specific fields (comma-separated) |
| `fs-cmsfilter-type="text"` | Set filter type to text search |
| `fs-cmsfilter-debounce="300"` | Delay filtering by 300ms while typing |

---

## List Filter

General-purpose filtering for both CMS and static lists. Simpler than CMS Filter, works on any list.

### Key Attributes
| Attribute | Element | Purpose |
|-----------|---------|---------|
| `fs-list-element="list"` | List wrapper | Identifies the list |
| `fs-list-field="fieldName"` | Text input | Which field to search/filter |
| `fs-list-field="*"` | Text input | Search all fields |
| `fs-list-fuzzy="20"` | Text input | Fuzzy match tolerance (0-100, 0=exact) |
| `fs-list-debounce="300"` | Text input | Delay filtering while typing |
| `fs-list-highlight="true"` | Text input | Highlight matching text in results |
| `fs-list-element="empty"` | Any element | Shown when zero results match |
| `fs-list-element="initial"` | Any element | Shown before any interaction, hidden after |
| `fs-list-operator="equal"` | Text input | Match operator (equal, contain, starts, etc.) |

### Visibility States
- **`fs-list-element="initial"`**: Visible on page load, hidden once user interacts with filter
- **`fs-list-element="empty"`**: Hidden on load, shown when no items match the filter
- These are controlled by FS via inline styles after initialization

---

## CMS Load (Load More / Pagination)

Load all CMS items beyond Webflow's 100-item limit, or add pagination.

### Key Attributes
| Attribute | Element | Purpose |
|-----------|---------|---------|
| `fs-cmsload-element="list"` | Collection List Wrapper | The list to paginate |
| `fs-cmsload-element="pagination"` | Pagination wrapper | Webflow's pagination (can be hidden) |
| `fs-cmsload-mode="load-under"` | Collection List | Appends items below |
| `fs-cmsload-mode="pagination"` | Collection List | Traditional page numbers |
| `fs-cmsload-mode="infinite"` | Collection List | Auto-load on scroll |
| `fs-cmsload-element="loader"` | Any element | Loading spinner shown during fetch |

---

## CMS Sort

Sort CMS Collection Lists by any field.

### Key Attributes
| Attribute | Element | Purpose |
|-----------|---------|---------|
| `fs-cmssort-element="trigger"` | Button/link | Click to sort by this field |
| `fs-cmssort-field="fieldName"` | Sort trigger | Which CMS field to sort by |
| `fs-cmssort-order="asc"` | Sort trigger | Default sort direction (asc/desc) |
| `fs-cmssort-type="number"` | Sort trigger | Data type (string, number, date) |

---

## Table of Contents (TOC)

Auto-generates a table of contents from headings in a Rich Text element.

### Key Attributes
| Attribute | Element | Purpose |
|-----------|---------|---------|
| `fs-toc-element="contents"` | Rich Text block | Source of headings |
| `fs-toc-element="list"` | Empty div | Where TOC links are generated |
| `fs-toc-element="link"` | Link template inside list | Template for each TOC entry |

### Heading Level Wrappers
Each heading level needs its own wrapper div inside the TOC list for proper nesting:
- H2 links go in the root of the list
- H3 links go in a nested wrapper under H2
- H4 links go in a nested wrapper under H3

### Active State
- FS adds `.w--current` class to the active TOC link during scroll
- Style `.w--current` for active state (e.g., color change, bold)
- Style non-current links for "past" state if desired

### Important Learnings
- TOC only works on the **published site**, not in Designer preview
- Only H2-H6 are indexed (H1 is excluded)
- A MutationObserver on the TOC element causes an **infinite loop** — avoid this
- Each heading level needs its own wrapper div for proper nesting structure

---

## Combo Box

Search-as-you-type dropdown. Purpose-built for search inputs with dropdown results.

### Key Attributes
| Attribute | Element | Purpose |
|-----------|---------|---------|
| `fs-combobox-element="dropdown"` | Wrapper div | The dropdown container |
| `fs-combobox-element="text-input"` | Text input | The search input |
| `fs-combobox-element="select"` | List element | The filterable options list |
| `fs-combobox-element="option-template"` | List item | Template for each option |
| `fs-combobox-element="empty"` | Any element | Shown when no options match |
| `fs-combobox-element="clear"` | Button | Clears the input |

### Dropdown Visibility
Combo Box automatically handles show/hide of the dropdown based on input focus and value. No custom JavaScript needed for basic dropdown behavior.

---

## Flash Prevention Pattern

**Problem**: FS-controlled elements (dropdowns, empty states, filtered lists) flash briefly on page load before the FS script initializes and hides them.

**Solution**: CSS-first hide with JavaScript cleanup.

```html
<style id="init-hide">
  .dropdown-wrapper,
  .empty-state {
    display: none;
  }
</style>
<script>
  document.addEventListener('DOMContentLoaded', function () {
    var input = document.querySelector('[fs-list-field]');
    var wrapper = document.querySelector('.dropdown-wrapper');
    var initStyle = document.getElementById('init-hide');
    if (!input || !wrapper) return;

    var cleaned = false;

    function showWrapper() {
      wrapper.style.display = 'block';
      if (!cleaned && initStyle) {
        // Remove only the empty-state rule, keep wrapper rule
        initStyle.textContent = '.dropdown-wrapper { display: none; }';
        cleaned = true;
      }
    }

    input.addEventListener('input', function () {
      wrapper.style.display = this.value.length > 0 ? 'block' : 'none';
    });

    input.addEventListener('focus', function () {
      if (this.value.length > 0) showWrapper();
    });

    document.addEventListener('click', function (e) {
      if (!wrapper.contains(e.target) && e.target !== input) {
        wrapper.style.display = 'none';
      }
    });
  });
</script>
```

### Why This Pattern Works
1. **CSS in `<head>`** hides elements before first paint — no flash
2. **`id` on the style tag** lets JavaScript modify the rules after FS loads
3. **On first interaction**, the script rewrites the style tag to remove rules FS needs to control (like empty state), while keeping rules the script manages (like wrapper visibility)
4. **FS uses inline styles** which override stylesheet rules — but only after FS initializes. The CSS prevents the flash in the gap before initialization.

### Key Principles
- Hide with CSS what FS will control, but only until FS is ready
- Elements inside a hidden wrapper don't need separate CSS hiding (the wrapper hides them)
- Don't use `!important` — it blocks FS inline styles from overriding
- Don't use MutationObserver on FS-managed elements — can cause infinite loops
- FS callback API (`window.fsAttributes.push`) is unreliable for style overrides — use the CSS rewrite pattern instead

---

## Accessibility Attributes

Finsweet ships two attributes specifically for accessibility, plus a few others that generate partial ARIA out of the box.

### `fs-a11y` — dynamic ARIA state

Manages dynamic ARIA properties (`aria-expanded`, `aria-controls`, `aria-hidden`, `aria-selected`) in response to component state changes. Doesn't inject base ARIA — it updates what you've already set when state changes.

Use with: accordions, dropdowns, tabs, toggle menus, disclosure components.

```html
<script async src="https://cdn.jsdelivr.net/npm/@finsweet/attributes-a11y@1/a11y.js"></script>
```

Key pattern: set the initial ARIA attributes in the Designer (`aria-expanded="false"`, `aria-controls="panel-1"`), then let `fs-a11y` flip them as the component toggles.

### `fs-modal` — accessible modal semantics

Adds static semantic attributes (`role="dialog"`, `aria-modal="true"`) and handles basic focus management (focus moves to modal on open, focus returns to trigger on close).

Still verify manually:
- Focus trap inside the modal (Tab cycles within)
- ESC key closes the modal
- Background content gets `aria-hidden="true"` while open
- Modal has an accessible name via `aria-labelledby` pointing to the title

### Other attributes and their ARIA output

| Attribute | Static ARIA out of the box | Still need to verify |
|-----------|---------------------------|---------------------|
| `fs-accordion` | `aria-expanded` toggled on trigger | `aria-controls` pointing to panel, panel ID matches |
| `fs-tabs` | `role="tablist"`, `role="tab"`, `role="tabpanel"`, `aria-selected` | Keyboard arrow navigation, `aria-controls` |
| `fs-cmsfilter` | None (filter controls are just inputs) | `aria-live` on results count, label associations |
| `fs-combobox` | `role="combobox"` on input | `aria-expanded`, `aria-activedescendant`, `aria-autocomplete`, arrow key navigation |
| `fs-modal` | `role="dialog"`, `aria-modal="true"` | Focus trap, ESC close, `aria-labelledby` |
| `fs-toc` | `.w--current` class on active link | Tab order, skip-link to TOC |

**Rule**: never trust an attribute alone. Always inspect the rendered DOM and walk through with keyboard + screen reader before marking a component done.

### `aria-live` for filter results

When using `fs-cmsfilter` with a results count, add `aria-live="polite"` to the count element so screen readers announce updates:

```html
<div fs-cmsfilter-element="results-count" aria-live="polite" aria-atomic="true">
  0 results
</div>
```

---

## Common Pitfalls

1. **Flash of unstyled content (FOUC)**: FS scripts load async. Elements it controls will show briefly before FS hides them. Always use the CSS-first hide pattern above.

2. **MCP scripts vs page head CSS**: MCP-registered scripts load from CDN and may have race conditions with FS. For critical CSS (flash fixes), use **inline CSS in page head**, not MCP scripts.

3. **MutationObserver on FS elements**: FS modifies DOM attributes continuously during filtering. Observing these elements causes infinite loops. Never attach MutationObserver to FS-controlled elements.

4. **FS doesn't manage wrapper visibility**: FS filters items *within* a list. It does not show/hide the wrapper container. If you need a dropdown that appears on search and disappears when empty, you need the flash prevention pattern above.

5. **Inline styles vs CSS specificity**: FS sets inline `display` styles on filtered items and empty states. Stylesheet rules (even with classes) are overridden by inline styles. But `!important` in stylesheets blocks inline styles — never use it on FS-controlled elements.

6. **TOC only works published**: The Finsweet TOC attribute only generates links on the published site. Don't debug TOC issues in Designer preview.

7. **Debounce timing**: When combining FS filtering with custom visibility scripts, account for the debounce delay. If `fs-list-debounce="300"`, FS won't filter until 300ms after the last keystroke.
