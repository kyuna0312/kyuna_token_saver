---
name: Reset Context
description: This skill should be used when the user says "reset context", "clear context", "things are getting slow", "session is bloated", "too much context", "start fresh", "context is full", or asks to reduce memory usage mid-session.
version: 1.0.0
---

# Reset Context

Perform a controlled context reset to eliminate accumulated bloat and restore fast, focused Claude responses. Use this when sessions slow down due to excess history, loaded skills, or memory files.

## When to Apply

- Responses feel slow or unfocused
- Claude is referencing irrelevant earlier conversation
- Many skills or files have been loaded during session
- User explicitly requests a fresh start

## Reset Procedure

### Step 1: Summarize Current State

Before resetting, capture essential context in under 200 tokens:

```
Current task: [one line]
Key files: [list paths]
Next action: [one line]
```

Output this summary to the user so they can paste it back after reset.

### Step 2: Instruct User to Paste Reset Prompt

Tell user to start a new Claude Code session or use the following prompt at the top of their next message:

```
Reset context.

Only keep:
- Current task: [PASTE TASK HERE]
- Key files: [PASTE FILE PATHS HERE]

Ignore:
- Previous conversation history
- Memory files
- Skills unless explicitly requested

Summarize current state in <200 tokens and continue.
```

### Step 3: Identify What to Preserve

Scan current conversation for:
- Active file paths being edited
- Current git branch
- Specific error messages being debugged
- Any user-defined constraints or preferences

Include only these in the summary. Discard everything else.

## Impact

A proper context reset can reduce token usage by 60-70% per response by eliminating:
- Loaded skill bodies (each skill = 1,000-5,000 tokens)
- Memory file content
- Repeated conversation history
- Stale tool results

## Additional Resources

- **`references/reset-strategies.md`** — Advanced reset patterns for different session types
