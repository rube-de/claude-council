# Council Quick Reference

## Invocation (Explicit Only)

| Command | Action | API Calls |
|---------|--------|-----------|
| `/council` | General council invocation | 4 parallel |
| `/council review` | PR/code review mode | 4 parallel |
| `/council plan` | Plan validation mode | 4 parallel |
| `/council consensus [topic]` | Multi-round consensus | 4-12 (multi-round) |
| `/council adversarial` | Adversarial review | 4 parallel |
| `/council quick` | Hierarchical (1→4) | 1-4 (escalates) |

**Note**: Does NOT auto-trigger. Requires explicit invocation.

## Pre-Flight Check

```bash
# Run before ANY council invocation
for cli in gemini codex qwen opencode; do
  command -v $cli >/dev/null 2>&1 && echo "✓ $cli" || echo "✗ $cli"
done
```

## Expertise Weights

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CONSULTANT EXPERTISE MATRIX                       │
├─────────────┬─────────┬─────────┬─────────┬─────────────────────────┤
│ Task        │ Gemini  │ Codex   │ Qwen    │ GLM-4.7                 │
├─────────────┼─────────┼─────────┼─────────┼─────────────────────────┤
│ Security    │ 0.90    │ 0.80    │ 0.70    │ 0.75                    │
│ PR Review   │ 0.85    │ 0.90    │ 0.80    │ 0.75                    │
│ Architecture│ 0.85    │ 0.70    │ 0.65    │ 0.80                    │
│ Code Quality│ 0.70    │ 0.80    │ 0.90    │ 0.70                    │
│ Performance │ 0.75    │ 0.85    │ 0.85    │ 0.70                    │
│ Brainstorm  │ 0.65    │ 0.60    │ 0.90    │ 0.85                    │
│ Algorithms  │ 0.70    │ 0.75    │ 0.85    │ 0.85                    │
│ Debugging   │ 0.75    │ 0.90    │ 0.80    │ 0.75                    │
│ Chinese Docs│ 0.40    │ 0.40    │ 0.70    │ 0.95                    │
└─────────────┴─────────┴─────────┴─────────┴─────────────────────────┘
```

## Workflow Selection

```
┌─────────────────────────────────────────────────────────────────┐
│                     Which Workflow?                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Quick validation?  ──────────► Hierarchical (start with 1)     │
│        │                              Calls: 1-4 (escalates)    │
│        │                                                        │
│  Rate limits?  ───────────────► Hierarchical or staggered       │
│        │                              Calls: 1-4 (sequential)   │
│        │                                                        │
│  Need trade-offs? ────────────► Adversarial                     │
│        │                              Calls: 4 (parallel)       │
│        │                                                        │
│  Need confidence? ────────────► Multi-round Consensus           │
│        │                              Calls: 4-12 (rounds)      │
│        │                                                        │
│  Default ─────────────────────► Parallel (all 4)                │
│                                       Calls: 4 (parallel)       │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## Partial Success Modes

| Available | Action |
|-----------|--------|
| 4/4 | Full synthesis |
| 3/4 | Proceed + warning |
| 2/4 | Proceed + strong warning |
| 1/4 | Abort → single consultant |
| 0/4 | Abort with error |

## Structured Response Schema

```json
{
  "consultant": "gemini|codex|qwen|glm",
  "success": true,
  "confidence": 0.85,
  "severity": "high",
  "findings": [
    {
      "type": "security",
      "severity": "high",
      "description": "SQL injection risk",
      "location": "src/api.ts:42",
      "recommendation": "Use parameterized queries"
    }
  ],
  "summary": "Found 2 high-severity security issues"
}
```

## Synthesis Formula

```
Weighted Score = Σ(Opinion × Expertise × Confidence) / Σ(Expertise × Confidence)
```

Example:
```
Security finding:
  Gemini (exp=0.9, conf=0.85): CRITICAL → 0.9 × 0.85 = 0.765
  Codex  (exp=0.8, conf=0.90): HIGH     → 0.8 × 0.90 = 0.720
  Qwen   (exp=0.7, conf=0.70): MEDIUM   → 0.7 × 0.70 = 0.490
  GLM    (exp=0.75, conf=0.80): HIGH    → 0.75 × 0.80 = 0.600

Weighted → CRITICAL (Gemini's expertise dominates)
```

## Output Template

```markdown
## Council Review Summary

### Pre-Flight Status
- Gemini: ✓ | Codex: ✓ | Qwen: ✓ | GLM: ✗ (timeout)

### 🚨 Critical (Any consultant)
- [Block-level issues]

### ✅ Consensus (All agree)
- [High-confidence findings]

### ⚠️ Majority (Weighted > 0.7)
- [Strong agreement findings]

### 🔀 Divergent
| Issue | Gemini | Codex | Qwen | GLM | Weighted |
|-------|--------|-------|------|-----|----------|

### Confidence: High/Medium/Low
### Rate Limits: None / Retried: 1 / Skipped: GLM
```

## CLI Commands

```bash
# Gemini
gemini -p "prompt" -f files
gemini -m flash -p "quick check"  # Fast mode
gemini -m pro -p "deep analysis"  # Thorough mode

# Codex
cat file | codex "prompt"
git diff | codex "review changes"
codex --quiet "prompt"  # Less verbose

# Qwen
qwen "@file prompt"
qwen "@src/*.ts analyze these"
qwen -s "@file test this"  # Sandbox mode

# GLM
opencode -m glm-4.7 "prompt"
opencode -m glm-4.7 -f file "prompt"
```

## Pre-Launch Checklist

Before sending to external AIs:

- [ ] Pre-flight CLI check passed
- [ ] No secrets in content (gitleaks scan)
- [ ] Content wrapped in XML delimiters
- [ ] Timeout set (120s default)
- [ ] Rate limit strategy selected (parallel vs staggered)

## Anti-Pattern Quick Check

| ❌ Don't | ✅ Do |
|----------|-------|
| Serial consultation | Parallel within rounds |
| "Don't you think X?" | "Compare X vs Y" |
| Ignore disagreement | Examine trade-offs |
| Dump 4 reports | Synthesize insights |
| Full council for trivial | Match workflow to need |
| Trust consensus blindly | Consider shared blind spots |
| Endless rounds | Max 3 rounds, then human |
| Hammer rate-limited CLI | Backoff, stagger, or skip |
