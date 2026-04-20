# Auto-Compact Strategies

## What Compaction Does

When context approaches limit, Claude summarizes conversation history into a compressed summary. Preserves:
- Key decisions made
- Files modified
- Current task state
- Important constraints

Loses:
- Exact wording of past exchanges
- Intermediate reasoning steps
- Verbose explanations already given

## compactOnContextFull Setting

```json
{
  "compactOnContextFull": true
}
```

**Without this**: Session hard-stops at limit. All work lost. Must restart manually.

**With this**: Auto-compacts and continues. Work preserved in summary.

## When Compaction Triggers

Approximate context limits:
- Claude Opus 4: ~200k tokens
- Claude Sonnet 4: ~200k tokens  
- Claude Haiku: ~200k tokens

Compaction triggers at ~80-90% of limit.

## PreCompact Hook (Advanced)

Add important information before compaction:

```json
{
  "hooks": {
    "PreCompact": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "bash $CLAUDE_PLUGIN_ROOT/hooks/scripts/pre-compact.sh"
      }]
    }]
  }
}
```

Example pre-compact.sh:
```bash
#!/bin/bash
# Output key state to preserve through compaction
echo "=== PRESERVE THROUGH COMPACTION ==="
if [ -f "$CLAUDE_PROJECT_DIR/CONTEXT.md" ]; then
  cat "$CLAUDE_PROJECT_DIR/CONTEXT.md"
fi
echo "Current working directory: $CLAUDE_PROJECT_DIR"
```

## Compaction vs. Reset

| Situation | Use |
|-----------|-----|
| Long session, work ongoing | compactOnContextFull: true |
| Starting fresh after completion | /reset-context |
| Switching to unrelated task | New session |
| Context polluted with errors | Manual reset |

## Compaction Quality Tips

Before context fills, create a `CONTEXT.md` checkpoint:
```markdown
# Current Context

## Task
[What we're doing]

## State
[Where we are]

## Next
[Exact next step]
```

Compaction will include this file in summary, giving better continuity.

## Settings to Reduce Need for Compaction

These settings reduce token consumption, extending sessions before compaction:

```json
{
  "autoLoadSkills": false,
  "autoLoadMemory": false,
  "compactOnContextFull": true
}
```

With skills/memory off:
- Save ~10-20k tokens/session on loading
- Sessions last proportionally longer
- Fewer compactions needed
