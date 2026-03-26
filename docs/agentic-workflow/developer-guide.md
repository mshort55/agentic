# Developer Guide

This guide covers how the agentic workflow system works internally and how to extend it. For using the system as an end user, see [User Guide](user-guide.md).

## System Architecture

See [Architecture](architecture.md) for the full system design including:
- Architecture diagram (user → orchestrator → agents → tools → knowledge base → output)
- Component descriptions
- Data flow (step-by-step)
- Agent interaction patterns (parallel, conditional)
- Synthesis algorithm (categorize → group → detect consensus → resolve conflicts → aggregate risks → generate report)
- Extensibility and continuous improvement

## Key Files

See `CLAUDE.md` for the full project structure tree. The most important files for understanding the system:

- `.claude/commands/` — slash commands (`analyze-spec`, `implement`, `eval-agent`, `review-cycle`)
- `.claude/agents/orchestrator/design-spec-orchestrator.md` — main orchestrator logic
- `.claude/agents/domain-experts/*.md` — 7 domain expert agent definitions
- `.claude/agents/templates/` — agent template and shared prompt template
- `config/agents.yaml` — agent registry and configuration

## How the Orchestrator Works

When a user runs `/analyze-spec foo.md`:

1. Claude reads `.claude/commands/analyze-spec.md` — this is the slash command prompt
2. The command tells Claude to read the orchestrator at `.claude/agents/orchestrator/design-spec-orchestrator.md`
3. The orchestrator instructions tell Claude to:
   - Read the design spec
   - Read `config/agents.yaml` to find enabled agents
   - Select agents based on the mode (full/smart/focused)
   - Launch all selected agents in parallel using the Agent tool
   - Synthesize results into an analysis report and save it to `output/analysis-reports/`

The orchestrator is not code — it's a prompt that Claude follows. The Agent tool is Claude Code's built-in mechanism for spawning parallel sub-agents.

## How Agents Work

Each agent is a markdown file with:
- **YAML frontmatter**: name, description, triggers (infrastructure config like model and tools lives in `config/agents.yaml`)
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

### Changing the slash commands
- `.claude/commands/analyze-spec.md` — argument parsing and orchestrator invocation
- `.claude/commands/implement.md` — creates brief, invokes writing-plans, offers execution
- `.claude/commands/eval-agent.md` — agent output quality evaluation and comparison
- `.claude/commands/review-cycle.md` — post-implementation review and agent improvement

### Adjusting configuration
Edit `config/agents.yaml` to change:
- Which agents are enabled/disabled
- Agent model and tool configuration
- Orchestrator defaults (mode, timeouts, parallelism)
- Extensibility settings

### Evaluating agent quality
Use `/eval-agent <agent-name> <spec.md>` to run a single agent against a design spec and save the raw output to `output/eval-results/`. Use `/eval-agent --compare <file1> <file2>` to diff two saved results after prompt changes.

### Reviewing implementation outcomes
After completing an implementation, run `/review-cycle <analysis-report> <commit-range>` to compare recommendations against actual code changes. The review proposes concrete edits to agent prompts based on what worked and what didn't. Reviews are saved to `output/review-cycles/`.

## Maintenance

See the "Extending Existing Agents" section of [Adding New Agents](adding-new-agents.md) for when and how to update agents. Use `agent-registry-helper` for config validation and discovery.

## Related Documents

- [Architecture](architecture.md) — System design and diagrams
- [User Guide](user-guide.md) — End-user documentation
- [Agent Registry](agent-registry.md) — Catalog of all agents
- [Adding New Agents](adding-new-agents.md) — Step-by-step guide
