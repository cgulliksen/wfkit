# wfkit

Let Claude Code build your Webflow marketing sites. Feed it your Figma design. It reads the tokens, sets up the Style Guide, builds variables with responsive breakpoints, and lays out perfectly Client-First-structured sections.

Spend more time designing and thinking up cooler sites. Less time on the boring plumbing.

---

## What it does

`wfkit` is a single Claude Code skill that teaches Claude how to build Webflow marketing sites using:

- **Finsweet Client-First v2** as the foundation (always cloned, never blank)
- **Style Guide page as the single source of truth.** All class definitions live there
- **Variables before styles** with 4 responsive typography modes, so responsiveness lives in the variable instead of per-breakpoint Designer overrides
- **The CF v2 4-layer section pattern.** `section_{name}` → `padding-global + padding-section-*` → `container-*` → `{name}_layout`
- **Accessibility baked in.** 25-item WCAG 2.2 AA baseline, not a pre-launch checklist

It also includes a comprehensive anti-pattern list ("mistakes to never repeat") so Claude catches common Webflow mistakes in review.

## Five modes

Invoke with an explicit mode or let Claude infer from your intent:

| Mode | Command | What it does |
|------|---------|--------------|
| BUILD | `/wfkit build` | Full A–Z site build from clonable to launch |
| AUDIT | `/wfkit audit` | Read-only structural review with report, no changes |
| REFACTOR | `/wfkit refactor` | Scoped fix of an existing section or page |
| REVIEW | `/wfkit review` | Pre-launch QA across performance, a11y, SEO, cross-browser |
| MCP OPS | `/wfkit mcp` | Quick read/write MCP operations on an already-correct site |

Or call without a mode (`/wfkit` or natural language like "build the hero") and it infers which mode fits.

## Install

### Option 1: Global skill (recommended)

Clone wfkit into your Claude Code skills folder:

```sh
cd ~/.claude/skills
git clone https://github.com/cgulliksen/wfkit.git
```

Restart Claude Code. `/wfkit` is now available everywhere.

### Option 2: Per-project

Clone into a project's local skills folder:

```sh
cd your-project
mkdir -p .claude/skills
cd .claude/skills
git clone https://github.com/cgulliksen/wfkit.git
```

The skill is only available when Claude Code runs in this project.

### Webflow MCP

`wfkit` uses the [official Webflow MCP server](https://developers.webflow.com/mcp) to read and write Webflow sites directly from Claude Code. You'll need:

1. A Webflow account (Workspace or Site plan)
2. The Webflow MCP enabled in Claude Code settings
3. The Designer bridge app running in the Webflow Designer tab when performing write operations

See the [Webflow MCP docs](https://developers.webflow.com/mcp) for the current setup.

## Usage

After installing, open a Webflow project in the Designer, then in Claude Code:

```
/wfkit build iverksetter 2026 hero section
```

wfkit will:

1. Smoke test the MCP bridge and confirm the target site ID
2. Walk through the A–Z build checklist phase-by-phase
3. Switch to Style Guide page, set up variable collections, bind body and heading defaults
4. Build the requested section using the CF v2 4-layer wrapper pattern
5. Verify at all 4 breakpoints

For an audit:

```
/wfkit audit https://example.com
```

For a refactor of an existing hero:

```
/wfkit refactor hero section
```

## Why this exists

Webflow is a great builder, but keeping sites consistent across multi-page projects, client handoffs, and rebrands is hard. Finsweet Client-First gives us the structure. wfkit encodes the patterns on top, so Claude Code can apply them reliably on every build.

## Reference files

| File | What it covers |
|------|----------------|
| [SKILL.md](./SKILL.md) | Main skill. Modes, phases, prime directives, quality checklist |
| [references/webflow-pattern.md](./references/webflow-pattern.md) | The full Client-First v2 layering pattern, variable collections, body defaults, scoped color groups, MCP workflow |
| [references/client-first-reference.md](./references/client-first-reference.md) | Finsweet Client-First v2 canonical reference. Naming rules, utility class inventory, spacing strategy, CSS specificity, fluid responsive |
| [references/custom-code-principles.md](./references/custom-code-principles.md) | JavaScript, CSS embeds, interactivity, performance optimization |
| [references/accessibility-checklist.md](./references/accessibility-checklist.md) | 25-item WCAG 2.2 AA baseline with code snippets |
| [references/finsweet-attributes-reference.md](./references/finsweet-attributes-reference.md) | Finsweet Attributes (CMS Filter, Sort, Load, fs-a11y, fs-modal) |
| [references/seo-checklist.md](./references/seo-checklist.md) | Per-page SEO + schema + AEO/GEO |

## Contributing

wfkit is an opinionated distillation of how we build Webflow sites. PRs are welcome, especially for:

- Additional reference docs (custom code patterns, animation patterns, CMS patterns)
- Example walkthroughs
- Bug fixes or clarifications in the skill file
- Anti-patterns we haven't documented yet

Open an issue first if you want to propose a big structural change. We want the skill to stay opinionated and coherent rather than becoming a grab bag.

## Maintainer

Built and maintained by [Christopher Gulliksen](https://www.linkedin.com/in/cgulliksen/), founder of [Iverksetter](https://iverksetter.no).

Iverksetter is a growth marketing + tech agency for Norwegian startups. We build MVPs, design brands, run performance marketing, and ship Webflow sites every week. wfkit is how we do Webflow.

- Website: [iverksetter.no](https://iverksetter.no)
- LinkedIn: [Christopher Gulliksen](https://www.linkedin.com/in/cgulliksen/)

## Credits

- [Finsweet](https://finsweet.com) for Client-First v2. The foundation this skill builds on
- [Garry Tan](https://github.com/garrytan/gstack) for the [gstack](https://github.com/garrytan/gstack) skill layout that inspired this structure
- [Anthropic](https://claude.com/claude-code) for Claude Code

## License

MIT. See [LICENSE](./LICENSE).
