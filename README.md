# kyuna_token_saver

A Claude Code plugin that reduces token waste and extends context window life. Includes 11 skills, a diagnostic agent, session-start hooks, and a live status line showing context usage.

---

## What It Does

| Skill | What it saves |
|-------|--------------|
| `optimize-claudemd` | Compresses bloated CLAUDE.md — cuts 40–80% of tokens loaded every session |
| `low-token-mode` | Switches Claude to terse response style — cuts reply tokens by 30–50% |
| `reset-context` | Safely resets context window when near full — prevents hard stops |
| `tune-settings` | Diffs and applies token-saving settings (autoLoadMemory, autoLoadSkills, etc.) |
| `manage-skills` | Audits loaded skills, disables unused ones — reduces session overhead |
| `project-isolation` | Scopes skills/hooks to current project only |
| `estimate-tokens` | Estimates tokens in any file before loading it |
| `auto-compact` | Configures compactOnContextFull — auto-compacts instead of stopping |
| `settings-diff` | Shows before/after diff before writing any settings change |
| `check-claudemd-size` | Reports CLAUDE.md word/token count with color-coded warnings |
| `token-statusline` | Adds live context bar to Claude Code status line |

**Agent:** `hook-error-fixer` — diagnoses and fixes broken hook configurations automatically.

**Hook:** Session-start script that warns when CLAUDE.md exceeds size thresholds.

**Status line:**
```
ctx [████████░░] 82%  │  md:~650t
```

---

## Requirements

- Claude Code v2.1.97 or later (for `refreshInterval` support)
- `python3` — used by the token status line script
- `bc` — used for token arithmetic in session-start hook

---

## Installation

### Option A — Clone directly into Claude plugins

```bash
git clone https://github.com/kyuna0312/kyuna_token_saver.git ~/.claude/plugins/kyuna_token_saver
```

Then enable in Claude Code:

```
/plugins enable kyuna_token_saver
```

### Option B — Clone anywhere, load with --plugin-dir

```bash
git clone https://github.com/kyuna0312/kyuna_token_saver.git ~/kyuna_token_saver
claude --plugin-dir ~/kyuna_token_saver
```

### Option C — Use in place (Desktop)

If already cloned to Desktop:

```bash
claude --plugin-dir ~/Desktop/kyuna_token_saver
```

---

## Token Status Line Setup

The status line shows live context window usage at the bottom of the terminal.

**Step 1 — Copy script to permanent location:**

```bash
cp ~/.claude/plugins/kyuna_token_saver/skills/token-statusline/scripts/token-status.sh ~/.claude/token-status.sh
chmod +x ~/.claude/token-status.sh
```

**Step 2 — Add to `~/.claude/settings.json`:**

```json
{
  "statusLine": {
    "type": "command",
    "command": "bash ~/.claude/token-status.sh",
    "refreshInterval": 30
  }
}
```

**Step 3 — Restart Claude Code.**

**Test before wiring up:**

```bash
echo '{"context_window":{"used_percentage":72},"workspace":{"current_dir":"'"$PWD"'"}}' \
  | bash ~/.claude/token-status.sh
```

Should print: `ctx [███████░░░] 72%`

**Color thresholds:**

| Context % | Color  |
|-----------|--------|
| 0–49%     | Green  |
| 50–74%    | Yellow |
| 75–89%    | Orange |
| 90–100%   | Red    |

---

## Session-Start Hook

The plugin includes a hook that runs when Claude Code starts and warns if CLAUDE.md is too large.

It fires automatically when the plugin is enabled. No setup needed.

To customize warning thresholds, edit `hooks/scripts/session-start.sh`:

```bash
WARN_WORDS=600    # yellow warning
CRIT_WORDS=1000   # red critical
```

---

## Skills Quick Reference

Trigger any skill by describing what you want. Examples:

- *"optimize my CLAUDE.md"* → `optimize-claudemd`
- *"switch to low token mode"* → `low-token-mode`
- *"reset context"* → `reset-context`
- *"check my settings"* → `tune-settings`
- *"how many tokens is this file?"* → `estimate-tokens`
- *"set up auto compact"* → `auto-compact`
- *"how big is my CLAUDE.md?"* → `check-claudemd-size`
- *"show me settings diff before saving"* → `settings-diff`
- *"add token counter to status line"* → `token-statusline`
- *"audit my loaded skills"* → `manage-skills`
- *"isolate this project"* → `project-isolation`
- *"fix my hooks"* → triggers `hook-error-fixer` agent

---

## Project Structure

```
kyuna_token_saver/
├── .claude-plugin/
│   └── plugin.json              # Plugin manifest
├── agents/
│   └── hook-error-fixer.md      # Auto-diagnoses broken hooks
├── hooks/
│   ├── hooks.json               # Hook event configuration
│   └── scripts/
│       └── session-start.sh     # CLAUDE.md size warning on startup
└── skills/
    ├── auto-compact/
    ├── check-claudemd-size/
    ├── debug-hooks/
    ├── estimate-tokens/
    ├── low-token-mode/
    ├── manage-skills/
    ├── optimize-claudemd/
    ├── project-isolation/
    ├── reset-context/
    ├── settings-diff/
    ├── token-statusline/        # Live status line counter
    │   ├── scripts/
    │   │   └── token-status.sh  # Status line script (copy to ~/.claude/)
    │   └── references/
    │       └── statusline-setup.md
    └── tune-settings/
```

---

## License

MIT
