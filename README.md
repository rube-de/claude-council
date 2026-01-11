# Council Plugin

Orchestrate multiple AI consultants for consensus-driven reviews and decisions.

## Features

- **4 AI Consultants**: Gemini, Codex, Qwen, GLM-4.7
- **Multiple Workflows**: Parallel, Hierarchical, Adversarial, Consensus
- **Weighted Synthesis**: Expertise-based opinion weighting
- **Rate Limit Handling**: Automatic retry and fallback

## Installation

### Option A: Copy to Personal Config

```bash
# Extract and copy
unzip council-plugin.zip
cp -r council-plugin/skills/* ~/.claude/skills/
cp -r council-plugin/agents/* ~/.claude/agents/
```

### Option B: Project-Level

```bash
# Copy to project
cp -r council-plugin/skills/* .claude/skills/
cp -r council-plugin/agents/* .claude/agents/
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
/council              # General review
/council review       # PR/code review
/council plan         # Plan validation
/council consensus    # Multi-round consensus
/council adversarial  # Advocates vs Critics
/council quick        # Hierarchical (1→4)
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
│       ├── SKILL.md
│       ├── WORKFLOWS.md
│       └── QUICK-REFERENCE.md
├── agents/
│   ├── gemini-consultant.md
│   ├── codex-consultant.md
│   ├── qwen-consultant.md
│   └── glm-consultant.md
└── README.md
```

## Workflows

| Workflow | Use Case | API Calls |
|----------|----------|-----------|
| Parallel | Default, broad feedback | 4 |
| Hierarchical | Quick validation, rate limits | 1-4 |
| Adversarial | Surface trade-offs | 4 |
| Consensus | High-stakes decisions | 4-12 |

## License

MIT
