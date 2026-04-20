# Settings Diff and Safety Reference

## Why Always Diff First

Settings changes are hard to reverse without a backup. Showing a diff before writing:
1. Prevents accidental overwrites
2. Makes changes reviewable
3. Builds user confidence
4. Documents what changed

## Backup Convention

Always create a timestamped backup:
```bash
cp ~/.claude/settings.json ~/.claude/settings.json.backup.$(date +%Y%m%d-%H%M%S)
```

Backup naming: `settings.json.backup.YYYYMMDD-HHMMSS`

To restore:
```bash
# List backups
ls ~/.claude/settings.json.backup.*

# Restore specific backup
cp ~/.claude/settings.json.backup.20241015-143022 ~/.claude/settings.json
```

## Diff Display Format

Show changes clearly before applying:

```
Current settings.json:
{
  "autoLoadMemory": true,        ← will change
  "autoLoadSkills": true,        ← will change
  "compactOnContextFull": false  ← will change
}

Proposed changes:
{
  "autoLoadMemory": false,       ← saves ~3,000 tokens
  "autoLoadSkills": false,       ← saves ~15,000 tokens  
  "compactOnContextFull": true   ← prevents hard stops
}

Token savings: ~18,000 tokens/session
```

## Settings Merge vs Replace

### Safe merge (preferred)
Read existing → add/update only changed keys → write back.

Preserves any custom settings not being changed.

### Replace (destructive)
Write entire new file. Loses any keys not explicitly included.

**Never replace** — always merge.

## Settings JSON Structure

```json
{
  "autoLoadMemory": false,
  "autoLoadSkills": false,
  "compactOnContextFull": true,
  "verboseOutput": false,
  "plugins": {
    "autoEnable": false,
    "enabled": ["plugin-name"]
  }
}
```

## What Each Setting Affects

| Setting | Change | Risk |
|---------|--------|------|
| autoLoadMemory | false | Low — manual load still works |
| autoLoadSkills | false | Low — trigger phrases still load skills |
| compactOnContextFull | true | Low — improves reliability |
| verboseOutput | false | Low — less text output |
| plugins.autoEnable | false | Medium — new plugins won't auto-enable |

All changes are reversible by editing settings.json.

## Python Merge Pattern

Safe settings merge without destroying existing config:

```python
import json
from pathlib import Path

settings_path = Path.home() / ".claude" / "settings.json"

# Read existing (or start empty)
existing = {}
if settings_path.exists():
    with open(settings_path) as f:
        existing = json.load(f)

# Apply changes (merge, don't replace)
changes = {
    "autoLoadMemory": False,
    "autoLoadSkills": False,
    "compactOnContextFull": True
}
existing.update(changes)

# Write back
with open(settings_path, "w") as f:
    json.dump(existing, f, indent=2)
```

## Validation After Write

After writing settings.json, validate:

```bash
python3 -c "import json; json.load(open('$HOME/.claude/settings.json'))" && echo "Valid JSON" || echo "ERROR: Invalid JSON"
```
