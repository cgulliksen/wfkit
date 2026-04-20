# Contributing

wfkit is a personal, opinionated skill maintained by one person. PRs and issues are welcome for additional references, anti-patterns, and example walkthroughs. The release process below is documented so it's reproducible across sessions (human or AI) and so any PR can be merged without losing context.

## Release process

All releases follow semver and [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

1. **Apply the change.** Edit `SKILL.md`, `references/`, or `examples/` with the new pattern, fix, or anti-pattern.
2. **Bump `VERSION`.** Patch for typos and fixes, minor for new patterns or sections, major for breaking structural changes.
3. **Update `CHANGELOG.md`.** Add a new `## [x.y.z] - YYYY-MM-DD` entry above the previous one. Use `Added`, `Changed`, `Fixed`, `Removed` groups.
4. **Run the pre-push safety check** (see below). Do not skip it.
5. **Commit, tag, push.**
   ```
   git add SKILL.md CHANGELOG.md VERSION references/ examples/
   git commit -m "Release vX.Y.Z: <one-line summary>"
   git tag vX.Y.Z
   git push origin main
   git push origin vX.Y.Z
   ```
6. **Sync installed skill** (if you use wfkit locally):
   ```
   cd ~/.claude/skills/wfkit && git pull
   ```

## Pre-push safety check

wfkit is a public repo that codifies learnings from real client work. Patterns go public. Client identity does not.

Before every push, grep the working tree AND the staged diff for:

- **Identifying proper nouns.** Any company name other than the maintainer's own brand. If a pattern was discovered on a real build, describe it generically ("a content migration", "a real-world build", "a data-driven grid") rather than naming the project.
- **Secrets.** Patterns that indicate keys or tokens:
  ```
  grep -riE "sk-[a-zA-Z0-9]{20,}|pk_[a-zA-Z0-9]{20,}|api[_-]?key|bearer\s+[a-zA-Z0-9]|authorization:\s*[a-zA-Z0-9]" .
  ```
- **Webflow identifiers.** Site IDs, workspace IDs, collection IDs, page IDs are 24-char hex and pointing back to client infrastructure:
  ```
  grep -riE "[a-f0-9]{24}" .
  ```
  Any match needs to be either a documentation placeholder (e.g. `6800000000000000000000aa`) or removed.
- **Private URLs.** Staging domains, preview URLs, internal dashboards:
  ```
  grep -riE "\.webflow\.io|staging\.|\.vercel\.app|\.netlify\.app" .
  ```
  These should only appear as generic examples (`example.webflow.io`), never as real links.
- **Internal commercial detail.** Pricing, contract terms, personal contact info, internal strategy language.

Also check commit messages for the same categories. `git log -p` before push reveals historical content too — if a sensitive string ever landed in a past commit, a `git filter-repo --replace-text` sweep is the remedy (destructive, coordinates force-push and tag reset).

## Source of learnings

Learnings typically originate from real-world builds. The codification flow is:

1. During a build session, document the learning in the session's own log (generic or named — the session log is private).
2. Before a release, review the candidate learnings and decide which generalize. Keep only the pattern, scrub the context.
3. If a learning only makes sense with client-specific context, it does not belong in wfkit. Keep it in the maintainer's private notes.

## Style conventions

- No em dashes (`—`) in public-facing files (`README.md`, `CHANGELOG.md`). Use periods or colons.
- Bullet points only when enumerating. Prose for reasoning.
- Link to `references/*.md` from SKILL.md rather than duplicating content.
