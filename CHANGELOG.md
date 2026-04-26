# Changelog

All notable changes to wfkit are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and wfkit adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.4.0] - 2026-04-26

Big harvest after two weeks of marketing-site builds. New performance reference, expanded MCP behavior coverage, Swiper.js integration patterns, responsive breakpoint patterns.

### Added

- **`references/performance-checklist.md`** — new reference file. Image format/compression/dimensions discipline, font loading and preload policy, script load order with defer/async guidance, CSS animation rules, Core Web Vitals targets, pre-launch sign-off checklist, common Webflow perf traps (slider preload, Lottie, video embeds).
- **Responsive breakpoint patterns** in `references/webflow-pattern.md`. The five recurring moves from `main` → `medium` → `small` → `tiny`: padding/gap ladder, two-col flip on medium, animations off on medium, fixed dimensions unconstrained on medium, position resets on medium. Clarifies that typography responsiveness lives in variable modes while layout responsiveness needs per-breakpoint Designer overrides.
- **Container-large breakout pattern** in `references/webflow-pattern.md`. DOM restructure for right-bleed carousels and marquees: `container-large` wraps only capped content, bleeding element sits in `padding-global + layout` directly, section gets `overflow: hidden` to clip horizontal spillage.
- **Icon insertion workflow** in `references/webflow-pattern.md`. Source decision (memory for simple Phosphor icons, Copy-as-SVG for complex), SVG scrub rules (`width/height="100%"`, `fill="currentColor"`), Embed block + `icon-embed-*` utility wiring, color via parent `color` CSS property.
- **Swiper.js + Webflow integration** section in `references/custom-code-principles.md`. CDN specificity battles solved via attribute-selector overrides in page head. `slidesPerView: 'auto'` vs numeric semantics. Reference initialization with data-attribute config.
- **Collapse-on-leave with IntersectionObserver** pattern in `references/custom-code-principles.md`. Pairs with Finsweet Load More: hide items past initial count when section exits viewport, re-reveal on next click for a fresh experience.
- **Data-attribute-driven JS config** pattern in `references/custom-code-principles.md`. Expose mode/behavior flags as `data-*` attributes; JS reads at init. Enables Designer-driven config swaps without touching script tags.
- **Bulk CMS edits subsection** in SKILL.md MCP operations. For 20+ items, MCP's `list_collection_items` response can overflow agent context. Pattern: subagent parses source → JSON to `/tmp` → Node script reads JSON, loads `.env` token, PATCHes via v2 REST API directly.
- Three new anti-patterns in SKILL.md (#25-27):
  - `whtml_builder` silently drops custom classes with no matching CSS rule. Pre-create styles via `style_tool`, include stub CSS, or audit + re-apply after insert.
  - `whtml_builder` `<img src="...">` doesn't bind to Webflow Assets — `src` becomes a literal URL. Run `element_tool set_image_asset` with asset_id after insert.
  - `style_tool update_style` can land on a combo class instead of the base when the target is part of a combo elsewhere. Verify via `query_styles` where the rule landed.
- Anti-pattern #24 in SKILL.md: pasting Figma's fixed widths into multi-col layouts. Figma frames are 1440px and export pixel widths that are proportions, not targets. Use `width: 50%`, `flex: 1 1 0%`, or `grid-template-columns: 1fr 1fr`.
- Two new MCP property-write quirks in SKILL.md: `whtml_builder` preserves inline SVG as proper DOM elements (positive case — one call ships icon-containing HTML); `min()` / `calc()` wrapping can be stripped by Webflow's CSS compiler (still valid CSS, browsers accept).

### Changed

- **1px hairline rule sharpened** in `references/client-first-reference.md` and anti-pattern in `references/webflow-pattern.md`. Exception now explicitly covers borders, dividers, strokes, `box-shadow` spread, and outline. Captures the WHY: `0.0625rem` rounds to 0.9px or 1.1px depending on zoom level and DPI due to subpixel rendering. Real `1px` stays a crisp hairline.
- **Deep stacking rule sharpened** in `references/client-first-reference.md` §8 with a fourth solution and a "trigger" callout. When you're about to add the 5th class, stop — translate Figma text styles to ONE custom class carrying all properties instead of stacking utilities + a custom class.
- **Anti-pattern #23 sharpened** in SKILL.md to call out the two failure modes: (a) custom class only adding a layout tweak on top of utilities (push to parent layout class), (b) stacking 4 utilities and reaching for a custom class to add the 5th property (consolidate all properties into one custom class).
- Phase 10 (Performance) in SKILL.md now links to the new `references/performance-checklist.md` for the full checklist.
- Reference files table updated with the new performance checklist.

### Notes

This release codifies patterns that hit during repeated builds. New file count: 1 (performance-checklist.md). Touched files: SKILL.md, references/webflow-pattern.md, references/client-first-reference.md, references/custom-code-principles.md.

---

## [1.3.2] - 2026-04-20

### Added
- Anti-pattern #23: combining utility classes with a custom class on the same element. Pick one strategy per element. If a custom class exists only to add a layout tweak on top of a utility-styled element, push that layout behavior up to the parent and drop the redundant class.

---

## [1.3.1] - 2026-04-20

### Fixed
- Scrubbed lingering project-specific phrasing from CHANGELOG, SKILL.md, and `references/custom-code-principles.md`. Finsweet Load More reference implementation now uses generic `data-item-*` attributes instead of domain-specific names. Custom-code-principles text describes mechanics only.
- Tightened CONTRIBUTING.md pre-push guidance: describe the mechanic, not the project or section a pattern came from.

---

## [1.3.0] - 2026-04-20

### Added
- **HARVEST mode.** Meta-mode for updating the wfkit skill itself. Reads a learnings queue, walks through each entry with the user, drafts generalized patches, runs the safety check, bumps version, ships a release. Complements the build-side modes with a structured way to ship improvements to the skill over time. See SKILL.md for the full flow and `CONTRIBUTING.md` for the release process it enforces.

---

## [1.2.0] - 2026-04-20

Patterns captured while building. Anti-patterns and MCP quirks that hit multiple times get codified.

### Added
- Five new anti-patterns in SKILL.md (#18-22):
  - `<script>` tags inside HTML Embed blocks don't execute reliably. JS belongs in Page or Project Settings custom code.
  - CMS text bindings require a real text element (Paragraph, Text Block, Heading). Binding on a layout wrapper fails silently.
  - CSS shorthand via MCP `style_tool` lands in "Custom Properties" instead of native Designer UI. Use longhand for `gap`, `margin: 0`, `border-radius`.
  - Never overwrite a Finsweet-controlled element's `style.display`. Finsweet owns its visibility state; custom code kills the click handler.
  - `element_snapshot_tool` doesn't always execute custom code embeds. Verify on the published staging URL, not the snapshot.
- **MCP property-write quirks section** in SKILL.md. Catalogues the shorthand-to-Custom-Properties gotchas (gap, margin, border-radius), `setBinding` not being exposed, blue-value misreads, and `query_styles` filter reliability.
- **Live vs staged publish semantics.** `PATCH /items/live` updates the staged doc and Designer reflects it immediately. Public pages don't update until full site publish. Binding work doesn't need republish between iterations.
- **Finsweet Load More inline "+X More" pattern** in `references/custom-code-principles.md`. Full reference implementation: move Pagination Next into the grid via `grid.appendChild()`, read total from `.w-page-count`, MutationObserver for remaining-count, `display: contents` trick on auto-generated `.w-dyn-*` wrappers.

### Changed
- Custom code location table clarifies HTML Embed is for HTML + CSS only. Explicit rule added: JavaScript belongs in Page Settings or Project Settings.

---

## [1.1.0] - 2026-04-19

First iteration after real-world use.

### Added
- `/wfkit-upgrade` skill. Detects install type (global-git, local-git, vendored), runs `git pull`, shows "What's new" from CHANGELOG.
- Auto-update check in main `/wfkit` preamble. Checks GitHub once per 24h (cached in `~/.wfkit/last-update-check`), surfaces `UPGRADE_AVAILABLE` when a newer version exists.
- `VERSION` file and `CHANGELOG.md` for version tracking.
- `bin/wfkit-update-check` helper script.
- CSS Specificity section in `references/client-first-reference.md`. Class order in stylesheet determines which property wins. Covers the `margin-top` + `margin-large` gotcha.
- **Critical MCP gotcha: scripts load from CDN, not inline.** Webflow MCP Bridge App scripts serve as external JS from Webflow's CDN, creating a race condition for above-the-fold elements. Critical CSS must go in page `<head>` custom code manually; MCP scripts are for JS behavior only.
- BUILD-mode plan-mode nudge in session startup checklist.
- Phase 0 now explicitly calls out extracting visual details (drop shadows, strokes, radii, filters) from Figma on first pull, not just structure + tokens.

### Changed
- **Style Guide scope rule reframed.** Previous rule "always switch to Style Guide before any style write" was too aggressive and caused disruptive page-ping-pong. New rule: Style Guide for utility classes (`heading-style-*`, `text-size-*`, etc.) and reusable components (used on 2+ pages). Page-specific classes (`{section}_layout`, one-off tweaks) stay on the target page.
- Top-of-skill bullet list and Quality Checklist now reference "CF v2 4-layer wrapper" instead of the outdated "two-tier `_component` + `_layout` pattern" (leftover terminology from v1.0).
- README lists all 6 reference files (was missing `client-first-reference.md` and `custom-code-principles.md`).

### Fixed
- Three legacy "two-tier pattern" references in SKILL.md that contradicted the rest of the file's CF v2 4-layer content.

---

## [1.0.0] - 2026-04-09

Initial release. Distilled from Iverksetter's Webflow build practice and the live Iverksetter 2026 hero build on-camera.

### Added
- Five skill modes: BUILD, AUDIT, REFACTOR, REVIEW, MCP OPS.
- CF v2 4-layer section wrapper as the canonical pattern.
- Variable-first workflow with Color + Typography collections (4 responsive modes).
- Body-level defaults pattern.
- Scoped color groups for component-specific tokens.
- Semantic HTML + `heading-style-*` combo pattern for SEO without visual tradeoffs.
- 25-item WCAG 2.2 AA accessibility baseline.
- 17-item "mistakes to never repeat" anti-pattern list.
- Reference files: webflow-pattern, client-first-reference, accessibility-checklist, seo-checklist, finsweet-attributes-reference, custom-code-principles.
- Hero build walkthrough example.
