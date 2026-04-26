# Performance Checklist

How to ship fast Webflow sites. Use during BUILD (Phase 10), AUDIT, and pre-launch REVIEW. Webflow gives you ~80% of the perf wins for free if you follow the basics; the other 20% is image discipline, font discipline, and not letting third-party scripts run wild.

## When to use

- **Every BUILD** — image compression and font setup are easier to do right than to retrofit
- **Pre-launch REVIEW** — run the full checklist before flipping the search-indexing switch
- **AUDIT mode** — when a site feels slow, walk this top to bottom
- **Post-launch** — if Lighthouse scores drift, the regression is almost always in images, fonts, or a new third-party script

---

## 1. Images

The single biggest perf lever on a marketing site.

### Format decision

| Source                          | Use         |
|---------------------------------|-------------|
| Photos, complex imagery         | WebP (fallback JPEG) |
| Logos, icons, illustrations     | SVG         |
| Photos with hard transparency   | PNG → WebP  |
| UI screenshots                  | WebP        |
| Short loops, tiny animations    | WebP / animated WebP / video |

Webflow auto-generates WebP variants on upload. Original format still matters because Webflow serves the smaller of (original, generated WebP). Upload as WebP when you can.

### Compression before upload

Squoosh (squoosh.app) or TinyPNG. Target sizes:

- **Hero images**: < 200kB
- **Section imagery**: < 100kB
- **Card / thumbnail**: < 50kB
- **Icons / logos**: SVG, < 5kB after SVGO

Hard rule: never upload a 4MB raw export to Assets. The "Webflow will handle it" reflex is wrong — it generates variants, but bloated originals still bloat the page.

### Dimensions before upload

Resize to **2× the largest displayed size**. A hero shown at 1200×630 ships at 2400×1260 max. Never ship a 5000px-wide image for a 600px display.

### Responsive images (srcset)

Webflow auto-generates srcset for any image inserted via Designer's image element. **It does NOT auto-generate srcset for `<img>` tags inside HTML Embed blocks** — those ship as fixed URLs.

For full srcset support, use the Designer image element. For Embed-block icons (SVG), srcset doesn't apply.

### Lazy-loading policy

| Element                           | Loading      |
|-----------------------------------|--------------|
| Hero image (above fold)           | `eager` — set explicitly |
| Below-fold images                 | `lazy` (Webflow default) |
| Background-image CSS on hero      | `eager` (no native lazy) |
| OG / share images                 | n/a          |

Webflow's default is `lazy` for most images. For the hero, set Loading to `Eager` in the image element settings — lazy-loading the LCP image is the #1 cause of poor Lighthouse LCP scores on Webflow sites.

### CMS images

Every CMS image field needs:
- Alt text (required for a11y AND avoids a SEO penalty)
- Compressed source (clients will upload 5MB phone photos otherwise — set the expectation in handoff)
- Same dimension discipline as static images

Document this in client handoff. A "how to upload images to your CMS" doc saves perf debt.

---

## 2. Fonts

### Self-hosted vs Google Fonts

Self-host when possible. Google Fonts requires a third-party DNS lookup + connection on every cold visit. Self-hosting via Webflow's font upload eliminates that.

When using Google Fonts (multi-language, exotic glyphs, fast iteration): accept the cost, don't pretend it's free.

### Subsetting

Upload only the weights actually used. A typeface with 9 weights × 2 styles = 18 files. If the site uses regular, semibold, and bold — upload 3 files, not 18.

### Preload policy

Preload critical fonts in Project Settings → Custom Code → Head:

```html
<link rel="preload" href="https://your-site.webflow.com/fonts/your-font.woff2"
      as="font" type="font/woff2" crossorigin>
```

Only preload fonts visible in the first viewport. Preloading everything is worse than preloading nothing.

### font-display

Webflow's font upload UI sets `font-display: swap` by default — keep it. Avoid `font-display: block`, which causes FOIT (flash of invisible text).

### Caps

Max 2 font families per site. Max 3-4 weights per family. If the design needs more, push back — the perf cost is real.

---

## 3. Scripts & custom code

### Load order

| Code                              | Location                          |
|-----------------------------------|-----------------------------------|
| Critical above-the-fold CSS       | Page Settings → Head              |
| Analytics, tag managers           | Page Settings → Head (defer)      |
| Schema markup (JSON-LD)           | Page Settings → Head              |
| Behavioral JS (event listeners)   | Page Settings → Before `</body>`  |
| Third-party widgets               | Page Settings → Before `</body>`  |
| Cookie banner                     | Page Settings → Head              |

**Never** put `<script>` tags inside HTML Embed blocks — they don't execute reliably.

### Defer vs async

- **`defer`** — script downloads in parallel, executes after HTML is parsed. Default choice for non-critical JS.
- **`async`** — script downloads in parallel, executes as soon as ready (can block parsing). Use only for fully independent scripts (analytics).
- **Neither** — blocks parsing. Reserve for inline scripts that must run synchronously (rare).

### Script audit

Before launch, list every external script:
- What does it do?
- Is it actually used on this page?
- Does it have a perf budget (TBT impact)?

Common offenders: hotjar, intercom, fullstory, multiple analytics tools, abandoned A/B testing scripts.

### MCP-registered scripts

Webflow MCP scripts load from CDN, not inline. Critical above-the-fold CSS (hide/show toggles, layout-blocking styles) must go in Page Settings → Head manually, not via MCP.

---

## 4. CSS

### Don't animate layout properties

Animating `width`, `height`, `top`, `left`, `margin`, `padding` triggers layout + paint on every frame. Use `transform` and `opacity` — they composite on the GPU.

```css
/* Bad */
.element { transition: top 0.3s; }
.element:hover { top: -10px; }

/* Good */
.element { transition: transform 0.3s; }
.element:hover { transform: translateY(-10px); }
```

### will-change

Use sparingly. `will-change: transform` on a constantly-animating element helps; `will-change` on every interactive element hurts (memory pressure).

### Critical CSS via head custom code

For above-the-fold elements that depend on JS-toggled visibility (search dropdowns, modal triggers), the initial hidden state needs to ship in Head custom code, not a separate stylesheet. Otherwise the element flashes visible before JS loads.

---

## 5. Core Web Vitals targets

| Metric                       | Target  | What it measures |
|------------------------------|---------|-----------------|
| LCP (Largest Contentful Paint)| < 2.5s | Largest visible element rendered |
| CLS (Cumulative Layout Shift) | < 0.1  | Unexpected layout shifts |
| INP (Interaction to Next Paint)| < 200ms | Tap/click response time |
| TTFB (Time to First Byte)     | < 800ms | Server response speed |
| FCP (First Contentful Paint)  | < 1.8s | First text/image painted |

Webflow handles TTFB. Everything else depends on what we ship.

### LCP — usually the hero image

Causes of bad LCP on Webflow:
- Hero image not eager-loaded
- Hero image too large (uncompressed)
- Hero image waiting on a font swap
- Hero image inside a slow-loading slider/carousel

### CLS — almost always missing image dimensions or font-swap

Set width/height on every image. Use `font-display: swap` (default) and accept the swap moment, or preload to minimize it.

### INP — usually a heavy script blocking the main thread

Audit before-body scripts. Defer non-critical work. Avoid synchronous JS in IX2 actions.

---

## 6. Pre-launch performance checklist

Run through this before flipping search-indexing on.

### Images

- [ ] Every image compressed (Squoosh / TinyPNG)
- [ ] Hero image set to Eager loading
- [ ] All other images Lazy
- [ ] All CMS images have alt text
- [ ] No image larger than 2× displayed size
- [ ] No raw exports in Assets (4MB+ files)

### Fonts

- [ ] Max 2 families
- [ ] Self-hosted where possible
- [ ] Only weights actually used uploaded
- [ ] Critical fonts preloaded in Head
- [ ] `font-display: swap` (Webflow default)

### Scripts

- [ ] Script audit done — every external script justified
- [ ] All non-critical JS deferred or async
- [ ] No `<script>` tags inside HTML Embed blocks
- [ ] Cookie banner loads early but doesn't block render
- [ ] Analytics fires (test in DevTools network tab)

### CSS

- [ ] No layout-property animations (width, height, top, left)
- [ ] `will-change` only on actually-animated elements
- [ ] Critical above-the-fold CSS in Head custom code

### Tests

- [ ] Lighthouse mobile score ≥ 90 on the home page
- [ ] Lighthouse mobile score ≥ 85 on every other key page
- [ ] PageSpeed Insights "Real-world" data reviewed (if site has traffic)
- [ ] Core Web Vitals all green
- [ ] Tested on actual mobile device (not just emulator)

---

## 7. Tools

| Tool                         | Use                                    |
|------------------------------|----------------------------------------|
| Lighthouse (Chrome DevTools) | Per-page audit, mobile + desktop       |
| PageSpeed Insights (web.dev) | Real-world Core Web Vitals from CrUX  |
| WebPageTest                  | Detailed waterfall, filmstrip          |
| Squoosh.app                  | Image compression                      |
| TinyPNG                      | Bulk image compression                 |
| SVGOMG                       | SVG cleanup + minification             |

---

## 8. Common Webflow perf traps

- **Slider with eager-loaded slides** — the LCP image lives in the active slide, but Webflow's default slider preloads all slides. Set non-active slides to lazy.
- **Lottie animations on every section** — JSON parse + render cost on each. Limit to 1-2 per page, defer non-critical.
- **Custom embed for Vimeo/YouTube** — embedded players ship ~500KB of JS each. Use a thumbnail + click-to-play pattern when possible.
- **Background videos in hero** — usually 1-3MB minimum. Replace with WebP poster + autoplay video below the fold, or skip entirely.
- **Multiple Google Fonts requests** — every weight = an additional HTTP request unless properly subsetted.
- **Forgotten staging scripts** — Hotjar, FullStory, dev-only debug tools left enabled in production.

---

## When to break the rules

- Internal tools, low-traffic landing pages, MVP launches: don't optimize early. Ship, then audit.
- Behind-the-paywall pages: SEO doesn't apply; perf still matters for retention but the budget can relax.
- Heavy interactive product demos (3D, WebGL, complex animations): accept the bigger bundle. Document the perf tradeoff in handoff.
