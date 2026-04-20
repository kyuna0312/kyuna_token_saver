# Token Benchmarks

## Word-to-Token Conversion

**Rule**: 1 word ≈ 1.3 tokens (English prose)

| Words | Tokens (approx) |
|-------|----------------|
| 100 | 130 |
| 300 | 390 |
| 500 | 650 |
| 1,000 | 1,300 |
| 2,000 | 2,600 |
| 5,000 | 6,500 |

**Code**: denser — 1 word ≈ 1.5-2 tokens due to punctuation, symbols.

## Baseline Token Costs by Component

### CLAUDE.md / Memory files
| Size | Tokens |
|------|--------|
| 50 words (minimal) | ~65 |
| 200 words (good) | ~260 |
| 600 words (bloated) | ~780 |
| 1,000 words (critical) | ~1,300 |

### Skills (SKILL.md body)
| Size | Tokens |
|------|--------|
| 500 words | ~650 |
| 1,500 words (target) | ~1,950 |
| 3,000 words (max) | ~3,900 |
| 5,000 words (too large) | ~6,500 |

### Plugins (all skill bodies combined)
| # Skills in plugin | Avg tokens per plugin |
|--------------------|-----------------------|
| 1 skill | ~1,950 |
| 5 skills | ~9,750 |
| 10 skills | ~19,500 |

### Memory files (~/.remember/*.md)
| Count | Avg tokens |
|-------|-----------|
| 1 file | ~390 |
| 5 files | ~1,950 |
| 10 files | ~3,900 |

## Total Session Budget Example

```
Component                    Tokens
------------------------------------------
System prompt (base)          ~2,000
Global CLAUDE.md (500 words)  ~650
Project CLAUDE.md (100 words) ~130
5 active skills               ~9,750
3 memory files                ~1,170
------------------------------------------
Session start total:          ~13,700
```

Each response adds: user tokens + response tokens + above.

## Pricing Reference (2024)

| Model | Input price | Cost at 10k tokens |
|-------|-------------|-------------------|
| Claude Opus 4 | $15/1M | $0.15 |
| Claude Sonnet 4 | $3/1M | $0.03 |
| Claude Haiku | $0.25/1M | $0.0025 |

**Savings from 10k token reduction per response:**
- Sonnet: $0.03/response
- 100 responses/day: $3/day = ~$90/month

## Quick Audit Command

```bash
# Total token estimate for all loaded context
echo "=== Token Audit ===" && \
echo "Global CLAUDE.md:" && \
[ -f "$HOME/.claude/CLAUDE.md" ] && echo "  $(echo "$(wc -w < $HOME/.claude/CLAUDE.md) * 1.3 / 1" | bc) tokens" || echo "  not found" && \
echo "Memory files:" && \
find "$HOME/.claude" -name "*.md" -path "*remember*" 2>/dev/null | while read f; do
  words=$(wc -w < "$f")
  echo "  $f: $(echo "$words * 1.3 / 1" | bc) tokens"
done
```
