# Custom Code Principles for Webflow

## Core Rule: Webflow First, JavaScript Second

Use Webflow's native tools before writing custom code:
- **Webflow Interactions** for scroll animations, hover effects, load animations, scroll-into-view triggers
- **Webflow CMS filters and conditional visibility** for content logic
- **Webflow forms** for standard form handling
- **CSS via Global Styles embed** for styling that Webflow Designer can't handle

Only write JavaScript when Webflow literally cannot do what you need.

## Targeting Elements

### Never Target Class Names in JavaScript

Class names belong to the styling layer. They change during design iterations and break JavaScript silently.

**Wrong:**
```javascript
document.querySelector('.hero_heading');
document.querySelectorAll('.card_item');
```

**Right:**
```javascript
document.querySelector('[data-element="hero-heading"]');
document.querySelectorAll('[data-element="card-item"]');
```

### Data Attribute Conventions

Use these `data-` attribute prefixes consistently:

| Prefix | Purpose | Example |
|--------|---------|---------|
| `data-element` | General element targeting | `data-element="navbar"` |
| `data-animate` | Animation triggers | `data-animate="fade-in"` |
| `data-filter` | Filter/sort targets | `data-filter="category"` |
| `data-modal` | Modal triggers and targets | `data-modal="pricing"` |
| `data-tab` | Tab triggers and panels | `data-tab="features"` |
| `data-accordion` | Accordion triggers | `data-accordion="faq-1"` |
| `data-form` | Form-specific behavior | `data-form="contact"` |

Add these in Webflow via the element's custom attributes panel.

## JavaScript Standards

### Clean ES6+

Always use modern JavaScript:

```javascript
// const and let, never var
const container = document.querySelector('[data-element="container"]');
let isOpen = false;

// Arrow functions
const toggleMenu = () => {
  isOpen = !isOpen;
  navbar.classList.toggle('is-open', isOpen);
};

// Template literals
const greeting = `Hei, ${user.name}!`;

// Async/await for external calls
const fetchData = async (endpoint) => {
  try {
    const response = await fetch(endpoint);
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    return await response.json();
  } catch (error) {
    console.error('Fetch failed:', error);
    return null;
  }
};

// Destructuring
const { name, email, phone } = formData;

// Optional chaining
const city = user?.address?.city;
```

### DRY Principle

Extract repeated logic into reusable functions:

```javascript
// Instead of repeating querySelector + event listener patterns:
const bindClick = (selector, handler) => {
  const elements = document.querySelectorAll(selector);
  elements.forEach(el => el.addEventListener('click', handler));
};

bindClick('[data-modal="open"]', openModal);
bindClick('[data-modal="close"]', closeModal);
bindClick('[data-accordion]', toggleAccordion);
```

### Error Handling

Handle errors on all external calls. Internal DOM operations do not need try/catch.

```javascript
// External API call: handle errors
const submitForm = async (data) => {
  try {
    const response = await fetch('/api/submit', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data),
    });
    if (!response.ok) throw new Error(`Submit failed: ${response.status}`);
    showSuccess();
  } catch (error) {
    console.error(error);
    showError('Noe gikk galt. Prøv igjen.');
  }
};
```

### Code Comments

Comment the WHY, not the WHAT:

```javascript
// Good: explains why
// Delay modal open to let page-load animation finish
setTimeout(showModal, 1500);

// Bad: describes what the code already says
// Set timeout to 1500 milliseconds
setTimeout(showModal, 1500);
```

## Performance

### Debounce Scroll and Resize Events

Never run expensive operations on every scroll/resize frame:

```javascript
const debounce = (fn, delay = 100) => {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
};

window.addEventListener('scroll', debounce(handleScroll, 50));
window.addEventListener('resize', debounce(handleResize, 150));
```

### IntersectionObserver for Scroll Triggers

Do not use scroll position calculations. Use IntersectionObserver:

```javascript
const observer = new IntersectionObserver(
  (entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        entry.target.classList.add('is-visible');
        observer.unobserve(entry.target); // Stop observing after trigger
      }
    });
  },
  { threshold: 0.2 }
);

document.querySelectorAll('[data-animate]').forEach((el) => observer.observe(el));
```

### Lazy Loading

- Images: Webflow handles this natively. Set loading="lazy" on below-fold images.
- Third-party scripts: Load non-critical scripts dynamically:

```javascript
const loadScript = (src) => {
  const script = document.createElement('script');
  script.src = src;
  script.async = true;
  document.body.appendChild(script);
};

// Load analytics after page is interactive
window.addEventListener('load', () => {
  loadScript('https://analytics.example.com/script.js');
});
```

## Script Placement

### Where to Put Code in Webflow

| Location | Use For |
|----------|---------|
| **Page head** (custom code) | CSS overrides, meta tags, preload hints, critical CSS that must block render |
| **Page before-body** (custom code) | Page-scoped JavaScript, event listeners, DOM manipulation |
| **Site-wide head** (project settings) | Global CSS, font loading, analytics snippets |
| **Site-wide before-body** (project settings) | Global JavaScript utilities, shared functions |
| **HTML Embed on page** | Inline HTML + CSS (JSON-LD schema, SVG, `<style>` blocks). **Not reliable for `<script>` tags** — Webflow often strips or ignores them in Embed blocks. Put JS in Page or Project custom code instead. |
| **Global Styles symbol** | CSS reset overrides, utility styles not available in Designer |

**Rule:** If it's JavaScript, it belongs in Page Settings or Project Settings custom code. HTML Embed blocks are for HTML and CSS only.

### Script Loading

```html
<!-- Non-critical: defer loading -->
<script src="analytics.js" defer></script>

<!-- Critical: load normally in before-body -->
<script src="app.js"></script>

<!-- External: async loading -->
<script src="https://external.com/widget.js" async></script>
```

## Global Styles Embed

The Global Styles embed is a Webflow Symbol containing an HTML Embed element with CSS. Place it at the top of every page.

Common CSS to include:

```css
/* Smooth scrolling (respect reduced motion) */
@media (prefers-reduced-motion: no-preference) {
  html { scroll-behavior: smooth; }
}

/* Focus visible styles */
:focus-visible {
  outline: 2px solid var(--color--brand-primary);
  outline-offset: 2px;
}

/* Hide scrollbar utility */
.hide-scrollbar {
  -ms-overflow-style: none;
  scrollbar-width: none;
}
.hide-scrollbar::-webkit-scrollbar {
  display: none;
}

/* Reduced motion override */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

## Common Patterns

### Form Handling with Custom Endpoint

```javascript
const form = document.querySelector('[data-form="contact"]');

form.addEventListener('submit', async (e) => {
  e.preventDefault();
  const formData = new FormData(form);
  const data = Object.fromEntries(formData);

  const submitButton = form.querySelector('[type="submit"]');
  submitButton.disabled = true;
  submitButton.textContent = 'Sender...';

  try {
    const response = await fetch('https://api.example.com/submit', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data),
    });
    if (!response.ok) throw new Error('Submit failed');
    form.style.display = 'none';
    document.querySelector('[data-element="success-message"]').style.display = 'block';
  } catch (error) {
    console.error(error);
    document.querySelector('[data-element="error-message"]').style.display = 'block';
    submitButton.disabled = false;
    submitButton.textContent = 'Send';
  }
});
```

### CMS Filter with Attribute Targeting

```javascript
const filterButtons = document.querySelectorAll('[data-filter]');
const items = document.querySelectorAll('[data-element="cms-item"]');

filterButtons.forEach((button) => {
  button.addEventListener('click', () => {
    const category = button.getAttribute('data-filter');

    filterButtons.forEach((btn) => btn.classList.remove('is-active'));
    button.classList.add('is-active');

    items.forEach((item) => {
      const match = category === 'all' || item.dataset.category === category;
      item.style.display = match ? '' : 'none';
    });
  });
});
```

### Finsweet Load More — inline "+X More" button pattern

Move the Collection List's Pagination Next button into the grid itself so it reads as a final item with a dynamic "+X More" label, without breaking Finsweet's click behavior.

**Structure requirements:**

- Collection List Wrapper holds the Pagination element (Webflow structural constraint — you can't drag it out).
- Grid anchor on the Dyno List with `data-{name}-list` attribute plus the Finsweet List v2 attributes `fs-list-element="list"` and `fs-list-load="more"`.
- Pagination element gets `data-{name}-pagination`.
- Header total span: `data-{name}-total` (e.g. `<span data-{name}-total>60</span>+ items`).
- Inside Pagination Next, a span for remaining count: `data-{name}-remaining`.

**Hard rules:**

1. Move the Next button with `grid.appendChild(nextBtn)` — event listeners follow the DOM node.
2. Hide the Pagination wrapper (`style.display = 'none'`) — Next button is now outside it.
3. **Never** touch `nextBtn.style.display` from custom code. Finsweet owns visibility on that button. Overwriting it silently kills the click handler.
4. Read total from `.w-page-count` text (format: `"1 / N"`) × items-per-page. Finsweet populates this asynchronously — poll until it has content.
5. MutationObserver on the grid updates only the remaining-counter span text. Never element display, never class changes.

**Reference implementation:**

```html
<script>
(function () {
  const ITEMS_PER_PAGE = 11; // match Collection List setting
  const MAX_ATTEMPTS = 40;   // ~4s at 100ms

  function init() {
    const grid = document.querySelector('[data-item-list]');
    const paginationWrap = document.querySelector('[data-item-pagination]');
    if (!grid || !paginationWrap) return;

    const nextBtn = paginationWrap.querySelector('.w-pagination-next');
    const pageCountEl = paginationWrap.querySelector('.w-page-count');
    const totalSpan = document.querySelector('[data-item-total]');
    const remainingSpan = document.querySelector('[data-item-remaining]');
    if (!nextBtn) return;

    grid.appendChild(nextBtn);
    paginationWrap.style.display = 'none';

    let total = 0;
    let attempts = 0;

    function updateRemaining() {
      const rendered = grid.querySelectorAll('.w-dyn-item').length;
      const remaining = Math.max(total - rendered, 0);
      if (remainingSpan) remainingSpan.textContent = remaining;
      // Do NOT touch nextBtn.style.display — Finsweet owns it
    }

    function tryReadTotal() {
      const text = pageCountEl ? pageCountEl.textContent.trim() : '';
      const m = text.match(/\/\s*(\d+)/);
      if (m) {
        total = parseInt(m[1], 10) * ITEMS_PER_PAGE;
        const rounded = Math.floor(total / 10) * 10;
        if (totalSpan && rounded > 0) totalSpan.textContent = rounded;
        updateRemaining();
        new MutationObserver(updateRemaining).observe(grid, { childList: true });
        return;
      }
      if (++attempts < MAX_ATTEMPTS) setTimeout(tryReadTotal, 100);
    }

    tryReadTotal();
  }

  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', init);
  } else {
    init();
  }
})();
</script>
```

**CSS companion — `display: contents` trick:**

Webflow auto-generates wrappers (`.w-dyn-list`, `.w-dyn-items`) that break flex flow when the parent is a flex-wrap grid. Apply `display: contents` on those wrappers so the items and moved Next button lay out as direct flex children:

```css
[data-item-list] .w-dyn-list,
[data-item-list] .w-dyn-items,
[data-item-list] .w-pagination-wrapper { display: contents; }
```

Place the `<style>` block in page head or in a sibling HTML Embed. Keep the JS in page/project custom code.

### Copy to Clipboard

```javascript
document.querySelectorAll('[data-element="copy-button"]').forEach((button) => {
  button.addEventListener('click', async () => {
    const text = button.getAttribute('data-copy');
    try {
      await navigator.clipboard.writeText(text);
      button.textContent = 'Kopiert!';
      setTimeout(() => (button.textContent = 'Kopier'), 2000);
    } catch {
      console.error('Copy failed');
    }
  });
});
```
