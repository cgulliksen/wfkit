---
name: wfkit-upgrade
description: |
  Upgrade wfkit to the latest version. Detects install type (global vs vendored),
  runs git pull or re-clone, and shows what's new from CHANGELOG. Use when asked
  to "upgrade wfkit", "update wfkit", or "get the latest wfkit".
allowed-tools:
  - Bash
  - Read
  - AskUserQuestion
---

# /wfkit-upgrade

Upgrade wfkit to the latest version from GitHub.

## Inline upgrade flow

This section is referenced by the main `/wfkit` preamble when it detects `UPGRADE_AVAILABLE`.

### Step 1: Ask the user

Use `AskUserQuestion`:
- Question: "wfkit **v{new}** is available (you're on v{old}). Upgrade now?"
- Options: ["Yes, upgrade now", "Not now"]

If "Not now": skip to Step 5, don't upgrade. The cache file `~/.wfkit/last-update-check` still holds the pending upgrade notification — it will surface again on the next cache miss (24h from now).

If "Yes, upgrade now": proceed to Step 2.

### Step 2: Detect install type

```bash
if [ -d "$HOME/.claude/skills/wfkit/.git" ]; then
  INSTALL_TYPE="global-git"
  INSTALL_DIR="$HOME/.claude/skills/wfkit"
elif [ -d ".claude/skills/wfkit/.git" ]; then
  INSTALL_TYPE="local-git"
  INSTALL_DIR=".claude/skills/wfkit"
elif [ -d ".agents/skills/wfkit/.git" ]; then
  INSTALL_TYPE="local-git"
  INSTALL_DIR=".agents/skills/wfkit"
elif [ -d "$HOME/.claude/skills/wfkit" ]; then
  INSTALL_TYPE="vendored-global"
  INSTALL_DIR="$HOME/.claude/skills/wfkit"
elif [ -d ".claude/skills/wfkit" ]; then
  INSTALL_TYPE="vendored-local"
  INSTALL_DIR=".claude/skills/wfkit"
else
  echo "ERROR: wfkit not found"
  exit 1
fi
echo "Install type: $INSTALL_TYPE at $INSTALL_DIR"
```

### Step 3: Save old version

```bash
OLD_VERSION=$(cat "$INSTALL_DIR/VERSION" 2>/dev/null | tr -d '[:space:]' || echo "unknown")
```

### Step 4: Upgrade

**For git installs** (global-git, local-git):
```bash
cd "$INSTALL_DIR"
STASH_OUTPUT=$(git stash 2>&1)
git fetch origin
git reset --hard origin/main
```

If `$STASH_OUTPUT` contains "Saved working directory", warn the user: "Note: local changes were stashed in `$INSTALL_DIR`. Run `git stash pop` there to restore them if wanted."

**For vendored installs** (vendored-global, vendored-local):
```bash
TMP_DIR=$(mktemp -d)
git clone --depth 1 https://github.com/cgulliksen/wfkit.git "$TMP_DIR/wfkit"
mv "$INSTALL_DIR" "$INSTALL_DIR.bak"
mv "$TMP_DIR/wfkit" "$INSTALL_DIR"
rm -rf "$INSTALL_DIR.bak" "$TMP_DIR"
```

If the clone fails, restore from backup:
```bash
rm -rf "$INSTALL_DIR"
mv "$INSTALL_DIR.bak" "$INSTALL_DIR"
```

### Step 5: Clear update-check cache

```bash
rm -f ~/.wfkit/last-update-check
```

This forces a fresh check on the next `/wfkit` invocation (not needed immediately since the user is obviously using it right now — but guarantees the stale "upgrade available" notification doesn't resurface).

### Step 6: Show What's New

Read `$INSTALL_DIR/CHANGELOG.md`. Find the entry for the new version. Summarize 3-6 bullets focused on user-facing changes. Skip internal refactors unless they're significant.

Format:
```
wfkit v{new} — upgraded from v{old}.

What's new:
- [bullet 1]
- [bullet 2]
- [bullet 3]

Run `/wfkit` as usual. Happy building.
```

### Step 7: Continue

If this upgrade was triggered from a `/wfkit` preamble check (not a direct `/wfkit-upgrade` invocation), continue with whatever the user originally wanted to do. The upgrade is done — no further action needed.

---

## Standalone usage

When invoked directly as `/wfkit-upgrade` (not from a preamble):

1. Force a fresh update check (bypass cache):
```bash
~/.claude/skills/wfkit/bin/wfkit-update-check --force 2>/dev/null || \
.claude/skills/wfkit/bin/wfkit-update-check --force 2>/dev/null || true
```

2. If output is `UPGRADE_AVAILABLE <old> <new>`: follow Steps 2-6 above.

3. If output is empty: tell the user "You're on the latest version of wfkit (v{version})." where `{version}` is read from `$INSTALL_DIR/VERSION`.
