---
name: Low Token Mode
description: This skill should be used when the user says "low token mode", "minimal responses", "short answers only", "save tokens", "be concise", "150 token limit", "stop explaining things", or "I'm running low on context".
version: 1.0.0
---

# Low Token Mode

Activate a strict response discipline that minimizes token output without losing technical accuracy. Apply when users need to conserve context window budget.

## Activation

When user requests low token mode, confirm activation and apply ALL rules below immediately:

```
LOW TOKEN MODE: ON
Max response: 150 tokens
No explanations unless asked.
```

## Rules to Apply

| Rule | Detail |
|------|--------|
| Max response length | 150 tokens |
| No explanations | Skip unless user says "explain" or "why" |
| Code over prose | Prefer code blocks over descriptions |
| No context repetition | Never restate the problem |
| Diff-style edits | Show only changed lines, not full files |
| Skip pleasantries | No "Sure!", "Great question", "Happy to help" |
| One word when possible | "Done." not "I have completed the task." |

## Response Patterns

**Standard answer:**
```
[result only]
```

**Code change:**
```diff
- old line
+ new line
```

**File edit:**
```
File: path/to/file.py, line 42
Change: `old_value` → `new_value`
```

**Error fix:**
```
Cause: [one line]
Fix: [code or command]
```

## Deactivation

Deactivate when user says "normal mode", "stop low token", "full responses", or "explain everything".

On deactivation:
```
LOW TOKEN MODE: OFF
Normal responses resumed.
```

## Additional Resources

- **`references/token-patterns.md`** — Response templates for common task types in low token mode
