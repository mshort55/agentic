# Developer Guide

This guide covers how the agentic workflow system works internally and how to extend it. For using the system as an end user, see [User Guide](user-guide.md).

## System Architecture

See [Architecture](architecture.md) for the full system design including:
- Architecture diagram (user → orchestrator → agents → tools → knowledge base → output)
- Component descriptions
- Data flow (step-by-step)
- Agent interaction patterns (parallel, sequential, conditional, background)
- Synthesis algorithm (categorize → group → detect consensus → resolve conflicts → aggregate risks → generate plan)
- Performance characteristics and failure modes

## Key Files

```
.claude/
├── commands/
│   └── analyze-spec.md              # /analyze-spec slash command
├── agents/
│   ├── orchestrator/
│   │   └── design-spec-orchestrator.md  # Main orchestrator logic
│   ├── domain-experts/
│   │   ├── go-expert.md
│   │   ├── k8s-expert.md
│   │   ├── controller-expert.md
│   │   ├── crd-expert.md
│   │   ├── unit-test-expert.md
│   │   ├── e2e-test-expert.md
│   │   └── coding-expert.md
│   ├── templates/
│   │   └── domain-expert-template.md
│   └── tools/
│       └── agent-registry-helper.md
config/
└── agents.yaml                       # Agent registry and configuration
design-specs/
├── template.md
└── examples/                         # Reference design specs
```

## How the Orchestrator Works

When a user runs `/analyze-spec design-specs/foo.md`:

1. Claude reads `.claude/commands/analyze-spec.md` — this is the slash command prompt
2. The command tells Claude to read the orchestrator at `.claude/agents/orchestrator/design-spec-orchestrator.md`
3. The orchestrator instructions tell Claude to:
   - Read the design spec
   - Read `config/agents.yaml` to find enabled agents
   - Select agents based on the mode (full/smart/focused/quick)
   - Launch all selected agents in parallel using the Agent tool
   - Synthesize results into an implementation plan

The orchestrator is not code — it's a prompt that Claude follows. The Agent tool is Claude Code's built-in mechanism for spawning parallel sub-agents.

## How Agents Work

Each agent is a markdown file with:
- **YAML frontmatter**: name, description, model, type, tools, triggers
- **Prompt body**: role description, expertise areas, best practices, pitfalls, analysis framework, output format, key principles

When the orchestrator launches an agent, it sends the design spec content as the prompt. The agent then:
1. Analyzes the spec from its domain perspective
2. Searches the codebase for existing patterns (Grep, Glob, Read)
3. Optionally researches latest practices (WebSearch)
4. Returns structured recommendations

Agents are independent — they don't see each other's output. The orchestrator handles synthesis.

## Extending the System

### Adding a new domain expert
See [Adding New Agents](adding-new-agents.md).

### Modifying agent expertise
Edit the agent's `.md` file directly. Changes take effect on the next run — no restart or re-registration needed (the orchestrator reads agent files each time).

### Changing orchestrator behavior
Edit `.claude/agents/orchestrator/design-spec-orchestrator.md`. The synthesis algorithm, output format, and agent selection logic are all defined there.

### Changing the slash command
Edit `.claude/commands/analyze-spec.md`. This controls argument parsing and how the orchestrator is invoked.

### Adjusting configuration
Edit `config/agents.yaml` to change:
- Which agents are enabled/disabled
- Agent priorities and model selection
- Orchestrator defaults (mode, timeouts, parallelism)
- Extensibility settings

### Automated agent creation (future)
See [skill-creator Integration](skill-creator-integration.md) for plans to use skill-creator for agent generation, evaluation, and optimization.

## Maintenance

See the "Extending Existing Agents" section of [Adding New Agents](adding-new-agents.md) for when and how to update agents. Use `agent-registry-helper` for config validation and discovery.

## Related Documents

- [Architecture](architecture.md) — System design and diagrams
- [User Guide](user-guide.md) — End-user documentation
- [Agent Registry](agent-registry.md) — Catalog of all agents
- [Adding New Agents](adding-new-agents.md) — Step-by-step guide
- [skill-creator Integration](skill-creator-integration.md) — Automated agent management
