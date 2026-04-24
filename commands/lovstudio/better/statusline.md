---

allowed-tools: [Read, Write, Edit, Bash, Glob, AskUserQuestion]
description: Install or update the vibe-genius statusline (with daily token tracking + session title)
version: "2.0.0"
author: "公众号:手工川"
aliases: /better-statusline
---

# Better Statusline

Installs (or updates) the **vibe-genius** statusline shipped with this plugin:

```
💥 cc-plugins (main) │ Opus 4.7 (anthropic) │ $66.78 / 5.0M │ V2.1.119 │ <session title>
```

It shows: cwd · git branch · model · provider · **daily cost / daily token usage** · Claude Code version · session title (from `/compact` summary or first user prompt).

## Install (default mode)

### 1. Copy the script into place

```bash
src="$CLAUDE_PLUGIN_ROOT/scripts/statusline/vibe-genius.sh"
dst="$HOME/.claude/statusline.sh"

# Back up any existing file so /rollback works
if [ -f "$dst" ]; then
    mkdir -p "$HOME/.claude/statusline-versions"
    ts=$(date +"%Y%m%d_%H%M%S")
    cp "$dst" "$HOME/.claude/statusline-versions/statusline.sh.$ts.bak"
fi

cp "$src" "$dst"
chmod +x "$dst"
```

### 2. Wire it into settings.json

Edit `~/.claude/settings.json` so `statusLine` points at the script:

```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh",
    "padding": 0
  }
}
```

If `statusLine` already exists, replace its `command` — do not duplicate the key.

### 3. Verify

Run `~/.claude/statusline.sh` once with a sample stdin, or just start a new Claude Code session and confirm the line renders.

## Rollback

Backups live at `~/.claude/statusline-versions/statusline.sh.<timestamp>.bak`.

```bash
ls ~/.claude/statusline-versions/
cp ~/.claude/statusline-versions/statusline.sh.<ts>.bak ~/.claude/statusline.sh
```

## How the daily-token counter works

- Cache file: `~/.claude/.daily_tokens`, one line per `(day, session_id)` storing `byte_offset:session_token_total`.
- Each statusLine tick only parses the **newly-appended bytes** of the current session's transcript — cost is O(delta), not O(transcript size).
- Daily total = sum of `session_token_total` across all lines matching today. Multi-session aggregation is automatic: each session updates its own row, the sum is the day total.
- Day rollover prunes old rows. File truncation resets the offset.

## Legacy behavior (direct string)

If you just want to set `statusLine.command` to an inline string (no script), pass `inline` as the argument — the old v1.0.0 flow still works for that.

ARGUMENTS: $ARGUMENTS
