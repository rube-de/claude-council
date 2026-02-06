# Council Plugin

Orchestrate multiple AI consultants for consensus-driven reviews and decisions.

## Features

- **4 External AI Consultants**: Gemini, Codex, Qwen, GLM-4.7 (model diversity)
- **5 Claude Subagents**: Security, Bugs, Compliance, History, Quality (concern depth with native tool access)
- **Dual-Layer Review**: External consensus + Claude depth, all in parallel
- **Multiple Workflows**: Parallel, Review, Hierarchical, Adversarial, Consensus
- **Concern-Specific Review**: Security, Architecture, Bugs, Quality focus modes
- **Confidence Scoring**: Sonnet scoring agent filters false positives (0-100 scale)
- **Blind Mode**: `--blind` flag strips Claude subagent tool access for equal-footing comparison
- **Weighted Synthesis**: Expertise-based opinion weighting
- **False Positive Filtering**: Explicit taxonomy prevents noise at the source
- **Git History Context**: Blame + commit history in review workflows
- **Rate Limit Handling**: Automatic retry and fallback

## Installation

### Step 1: Download

**Option A: Download Release (Recommended)**
```bash
# Download latest release
curl -LO https://github.com/rube-de/claude-council/releases/latest/download/council-plugin.zip
unzip council-plugin.zip
```

Or download directly from [Releases](https://github.com/rube-de/claude-council/releases).

**Option B: Clone Repository**
```bash
git clone https://github.com/rube-de/claude-council.git
cd claude-council
```

### Step 2: Install

**Personal Config (all projects)**
```bash
cp -r skills/* ~/.claude/skills/
cp -r agents/* ~/.claude/agents/
```

**Project-Level (current project only)**
```bash
cp -r skills/* .claude/skills/
cp -r agents/* .claude/agents/
```

## Prerequisites

The following CLIs must be installed and authenticated:

| CLI | Install | Auth |
|-----|---------|------|
| `gemini` | [Gemini CLI](https://github.com/google-gemini/gemini-cli) | `gemini auth` |
| `codex` | [Codex CLI](https://github.com/openai/codex) | OAuth |
| `qwen` | [Qwen CLI](https://github.com/QwenLM/qwen-cli) | API key |
| `opencode` | [OpenCode CLI](https://github.com/opencode-ai/opencode) | API key |

## Usage

### Invoke the Council

```
/council                        # General review
/council review                 # Code review (auto-detect concerns + scoring)
/council review security        # All 4 consultants focus on security
/council review architecture    # All 4 consultants focus on architecture
/council review bugs            # All 4 consultants focus on bugs
/council review quality         # All 4 consultants focus on code quality
/council plan                   # Plan validation
/council consensus              # Multi-round consensus
/council adversarial            # Advocates vs Critics
/council quick                  # Hierarchical (1→4)
```

### How Review Works

```
/council review [concern?] [--blind?]
     │
     ├─ Layer 1: External Consultants (4x, parallel, same prompt)
     │   Gemini, Codex, Qwen, GLM → model diversity consensus
     │
     ├─ Layer 2: Claude Subagents (5x, parallel, different concerns)
     │   Security, Bugs, Compliance, History, Quality → depth + tool access
     │   (or --blind: via CLI, no tool access)
     │
     └─ Layer 3: Scoring
         review-scorer (Sonnet) → scores all findings 0-100
         Filter at threshold (>= 80) → final report
```

### Individual Consultants

```
# Use directly via Task tool
gemini-consultant
codex-consultant
qwen-consultant
glm-consultant
```

## Contents

```
council-plugin/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── council/
│       ├── SKILL.md            # Core definition, synthesis, scoring spec
│       ├── WORKFLOWS.md        # Detailed workflow procedures
│       └── QUICK-REFERENCE.md  # Lookup tables, CLI commands, templates
├── agents/
│   ├── gemini-consultant.md    # External: Gemini CLI (architecture, security)
│   ├── codex-consultant.md     # External: Codex CLI (PR review, debugging)
│   ├── qwen-consultant.md      # External: Qwen CLI (quality, brainstorming)
│   ├── glm-consultant.md       # External: OpenCode/GLM-4.7 (multilingual, algorithms)
│   ├── claude-security.md      # Internal: Security review (opus, tool access)
│   ├── claude-bugs.md          # Internal: Bug detection (opus, tool access)
│   ├── claude-compliance.md    # Internal: CLAUDE.md compliance (haiku, tool access)
│   ├── claude-history.md       # Internal: Git history analysis (haiku, tool access)
│   ├── claude-quality.md       # Internal: Code quality (haiku, tool access)
│   └── review-scorer.md        # Internal: Confidence scoring (sonnet)
└── README.md
```

## Workflows

| Workflow | Use Case | API Calls |
|----------|----------|-----------|
| Parallel | Default, broad feedback | 4 |
| Review | Code review with scoring | 4 + scoring + escalation |
| Review (focused) | Single-concern deep review | 4 + scoring |
| Hierarchical | Quick validation, rate limits | 1-4 |
| Adversarial | Surface trade-offs | 4 |
| Consensus | High-stakes decisions | 4-12 |

## Review Confidence Scoring

After consultants return findings, an internal Sonnet agent scores each one:

| Score | Meaning |
|-------|---------|
| 0 | False positive, doesn't hold up |
| 25 | Might be real, unverified |
| 50 | Real but minor, unlikely in practice |
| 75 | Verified real, will impact functionality |
| 100 | Confirmed, frequent, evidence conclusive |

Only findings scoring **>= 80** appear in the final report.

## License

MIT
