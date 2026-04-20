# Skill Audit Reference

## Token Cost by Skill Size

| SKILL.md size | Tokens loaded per session |
|---------------|--------------------------|
| 500 words | ~650 tokens |
| 1,000 words | ~1,300 tokens |
| 2,000 words | ~2,600 tokens |
| 3,000 words | ~3,900 tokens |
| 5,000 words | ~6,500 tokens |

**Formula**: words × 1.3 = tokens

## Audit Procedure

### Step 1: List all skill directories
```bash
ls ~/.claude/plugins/cache/*/*/skills/ 2>/dev/null
ls ~/.claude/plugins/*/skills/ 2>/dev/null
```

### Step 2: Count words per SKILL.md
```bash
find ~/.claude -name "SKILL.md" -exec sh -c 'echo "$(wc -w < "$1") $1"' _ {} \; | sort -rn
```

### Step 3: Categorize by need

**Keep if:**
- Triggered at least once this week
- Provides unique functionality
- Token cost < 500 per session

**Disable if:**
- Never triggered in current project
- Duplicate of another skill
- Token cost > 2000 with low usage

## Skill Registry Locations

```
~/.claude/plugins/cache/           # Installed via marketplace
~/.claude/plugins/                 # Local installs
[project]/.claude/skills/          # Project-scoped skills
```

## Disabling Skills

### Method 1: Plugin disable (all skills in plugin)
In `~/.claude/settings.json`:
```json
{
  "plugins": {
    "enabled": ["plugin1", "plugin2"]
  }
}
```
Remove plugin name from `enabled` list.

### Method 2: Global autoLoadSkills off
```json
{
  "autoLoadSkills": false
}
```
All skills disabled. Load manually via trigger phrase only.

### Method 3: Project-level override
In `[project]/.claude/settings.json`:
```json
{
  "plugins": {
    "enabled": ["only-relevant-plugin"]
  }
}
```

## Cost/Benefit Analysis

### High-value skills (keep)
- Project-specific domain knowledge
- Frequently triggered
- Saves more tokens than it costs (e.g., saves rewriting reference material)

### Low-value skills (consider disabling)
- Generic skills with info Claude already knows
- Rarely triggered
- Large SKILL.md for simple tasks

## Token Budget Example

**Typical session with 10 skills loaded:**
```
autoLoadSkills: true
10 skills × 1,500 words avg × 1.3 = ~19,500 tokens/session
At $3/1M tokens input = ~$0.06/session just for skill loading
```

**With autoLoadSkills: false (load only needed):**
```
Trigger 2 skills × 1,500 words × 1.3 = ~3,900 tokens/session
Savings: ~15,600 tokens/session = ~$0.047/session
```
