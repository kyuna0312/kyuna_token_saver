# Project Isolation Patterns

## Why Isolate

- Global CLAUDE.md loads for every project — irrelevant context wastes tokens
- Global settings apply everywhere — per-project needs differ
- Skill plugins active globally — most not relevant to current work

## File Locations

| File | Scope | Purpose |
|------|-------|---------|
| `~/.claude/CLAUDE.md` | Global | Rules for all projects |
| `[project]/CLAUDE.md` | Project | Project-specific rules (merged with global) |
| `~/.claude/settings.json` | Global | Default settings |
| `[project]/.claude/settings.json` | Project | Overrides for this project |

## Project CLAUDE.md Template

```markdown
# [Project Name]
Stack: [e.g., Next.js 14, TypeScript strict, PostgreSQL]

## Rules
- [Style rule]
- [Constraint]
- [Git convention]
```

Target: < 100 words, < 130 tokens.

## Project settings.json Template

```json
{
  "plugins": {
    "enabled": ["plugin-relevant-to-this-project"]
  },
  "autoLoadMemory": false,
  "autoLoadSkills": false
}
```

### What to set per-project

**autoLoadSkills: false** when:
- Project uses only 1-2 specific skills
- Load them via trigger phrase instead

**plugins.enabled: [specific]** when:
- Only some plugins relevant
- Reduces token load from plugin skill files

**autoLoadMemory: false** when:
- Memory files not relevant to this project
- Load manually: "recall [topic]"

## Token Savings from Isolation

### Before isolation (global settings)
```
Global CLAUDE.md: 800 words = ~1,040 tokens
10 active plugins: ~10,000 tokens
5 memory files: ~3,000 tokens
Total: ~14,040 tokens per session
```

### After isolation (project settings)
```
Project CLAUDE.md: 80 words = ~104 tokens
2 relevant plugins: ~2,000 tokens
0 memory files (manual load)
Total: ~2,104 tokens per session
Savings: ~11,936 tokens/session
```

## Multi-Project Setup Pattern

```
~/
├── .claude/
│   ├── CLAUDE.md          # Global: coding style only
│   └── settings.json      # Global: autoLoad all false
└── projects/
    ├── web-app/
    │   ├── CLAUDE.md      # Project: Next.js rules
    │   └── .claude/
    │       └── settings.json  # Project: enable web plugin only
    └── data-pipeline/
        ├── CLAUDE.md      # Project: Python/data rules
        └── .claude/
            └── settings.json  # Project: enable data plugin only
```

## Global CLAUDE.md Best Practices

Keep global CLAUDE.md to universal rules only:
- Code style (formatting, naming)
- Git conventions
- Language preference

Move to project CLAUDE.md:
- Framework-specific rules
- Project constraints
- Team conventions
- Deployment notes

Rule of thumb: If it's only true for one project, it belongs in project CLAUDE.md.
