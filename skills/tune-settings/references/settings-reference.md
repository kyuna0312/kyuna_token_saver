# settings.json Full Reference — Token Impact Ratings

## Token-Affecting Settings

### autoLoadMemory
- **Default**: true
- **Token impact**: HIGH — loads all `.remember/*.md` files every session
- **Recommended**: `false` — load memory manually when needed
- **Trade-off**: You must explicitly reference memory content

### autoLoadSkills  
- **Default**: true
- **Token impact**: VERY HIGH — loads all installed skill SKILL.md bodies
- **Recommended**: `false` — skills load via trigger phrases only
- **Trade-off**: Skills won't auto-activate; use explicit trigger phrases

### compactOnContextFull
- **Default**: false
- **Token impact**: NEUTRAL (prevents hard stops)
- **Recommended**: `true` — auto-compact instead of hitting limit
- **Trade-off**: Compaction loses some conversation detail

### verboseOutput
- **Default**: false (varies)
- **Token impact**: MEDIUM — verbose mode adds extra explanation tokens
- **Recommended**: `false` for token savings

### plugins.autoEnable
- **Default**: true
- **Token impact**: MEDIUM — all installed plugins active by default
- **Recommended**: `false` — enable plugins per-project explicitly

## Full Low-Token Config

```json
{
  "autoLoadMemory": false,
  "autoLoadSkills": false,
  "compactOnContextFull": true,
  "verboseOutput": false,
  "plugins": {
    "autoEnable": false,
    "enabled": []
  }
}
```

## Per-Project Settings

Create `.claude/settings.json` in project root to override global:

```json
{
  "plugins": {
    "enabled": ["plugin-name-relevant-to-this-project"]
  },
  "autoLoadMemory": false
}
```

## Token Cost Estimates Per Setting

| Setting | On | Off | Savings |
|---------|-----|-----|---------|
| autoLoadMemory (10 memory files avg) | ~3,000 tokens | 0 | ~3,000/response |
| autoLoadSkills (10 skills avg) | ~15,000 tokens | 0 | ~15,000/response |
| verboseOutput | ~200-500 extra | 0 | ~200-500/response |
| 5 active plugins | ~2,000 tokens | 0 | ~2,000/response |

## Backup Before Editing

Always backup settings before modifying:

```bash
cp ~/.claude/settings.json ~/.claude/settings.json.backup.$(date +%Y%m%d)
```

To restore:
```bash
cp ~/.claude/settings.json.backup.[DATE] ~/.claude/settings.json
```
