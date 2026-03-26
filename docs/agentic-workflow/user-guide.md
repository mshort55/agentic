# Agentic Workflow User Guide

## What This System Does

You write a design spec describing a feature, bug fix, or refactor. The system consults specialized domain expert agents in parallel — Go, Kubernetes, controllers, CRDs, unit testing, e2e testing, and general coding — then synthesizes their recommendations into a single analysis report.

## Quick Start

1. Write a design spec (copy `design-specs/template.md` as a starting point)
2. Run `/analyze-spec your-spec.md`
3. Review the analysis report (saved to `analysis-reports/`)
4. Run `/implement` to create the brief, generate the plan, and start execution
5. Execute the plan via subagent-driven-development or executing-plans

## Writing Design Specs

Use the template at `design-specs/template.md`. The most important sections are:

- **Overview**: What problem are you solving? (2-3 paragraphs)
- **Goals / Non-Goals**: What's in scope and what isn't
- **Proposed Changes**: API, controller, infrastructure changes
- **Testing Strategy**: What tests are needed
- **Domain Experts to Consult**: Check the boxes for relevant domains

The "Domain Experts to Consult" checkboxes help the orchestrator in smart mode, but in full mode (default) all agents are consulted regardless.

See `design-specs/examples/` for reference specs at different complexity levels:
- `simple-bugfix.md` — minimal scope, 2-3 agents
- `new-feature-with-api.md` — full scope, all 7 agents
- `improve-e2e-tests.md` — testing-focused
- `controller-optimization.md` — performance-focused
- `add-rbac-support.md` — security/cross-cutting

## Using `/analyze-spec`

```bash
# Full analysis with all agents (default)
/analyze-spec your-spec.md

# Quick — high-priority agents only (go, k8s, controller, unit-test)
/analyze-spec your-spec.md --fast

# Smart — auto-detect relevant agents from spec content
/analyze-spec your-spec.md --smart

# Focused — specify exactly which agents
/analyze-spec your-spec.md --focus=crd,controller,go
```

### Agent short names for `--focus`

| Short Name    | Agent              |
|---------------|--------------------|
| `go`          | go-expert          |
| `k8s`         | k8s-expert         |
| `controller`  | controller-expert  |
| `crd`         | crd-expert         |
| `unit-test`   | unit-test-expert   |
| `e2e-test`    | e2e-test-expert    |
| `coding`      | coding-expert      |

### Which mode to use

| Situation | Mode | Why |
|-----------|------|-----|
| New feature, unclear scope | `--full` (default) | Don't miss anything |
| Small bug fix, clear scope | `--fast` | Speed over thoroughness |
| You know what domains matter | `--focus=x,y` | Skip irrelevant agents |
| Medium change, want auto-detection | `--smart` | Let triggers decide |

## Understanding the Output

The analysis report is saved to `analysis-reports/` and has these sections:

- **Executive Summary** — High-level overview and key decisions
- **Key Recommendations** — Prioritized as Critical / Important / Nice to Have, with source agent attribution
- **Implementation Steps** — Phased plan with files to modify and domain-specific guidelines
- **Testing Strategy** — Unit and e2e test recommendations from the testing experts
- **Risks & Mitigation** — Aggregated risks from all agents, with severity and mitigation
- **Existing Patterns** — Codebase patterns the agents found that you should build on
- **Open Questions** — Things that need human decisions before implementation
- **Agent Reports** — Full output from each agent (appendix)

When multiple agents agree on a recommendation, it's flagged as consensus. When they disagree, the orchestrator resolves the conflict or flags it for your review.

## Common Workflows

### New Feature
1. Write a thorough design spec with API, controller, and testing sections
2. Run `/analyze-spec` (full mode)
3. Review critical recommendations and resolve open questions
4. Run `/implement` to create the brief, generate the plan, and start execution
5. Use the testing strategy section when writing tests

### Bug Fix
1. Write a minimal design spec: overview, proposed fix, regression test
2. Run `/analyze-spec your-spec.md --fast`
3. Focus on the go-expert and unit-test-expert recommendations
4. Implement fix and regression test

### Refactoring
1. Document what you're refactoring and why
2. Run `/analyze-spec your-spec.md --focus=go,coding`
3. Review existing patterns section — don't break conventions
4. Follow recommendations for safe refactoring

### Performance Optimization
1. Document the bottleneck, measurements, and proposed optimization
2. Run `/analyze-spec your-spec.md --focus=go,controller,coding`
3. Check if agents suggest measuring before optimizing
4. Follow benchmark test recommendations

## After the Analysis

You can ask Claude to:
- Dive deeper into a specific recommendation
- Consult additional agents you didn't include

### Next: `/implement`

**Requires:** The superpowers plugin must be installed for plan generation and execution. Install with `/plugin install superpowers@claude-plugins-official`, then `/reload-plugins`. The `/implement` command will check for this and tell you if it's missing.

With the analysis report in hand, run `/implement` to handle everything from brief creation through execution:

```bash
# Implement from the most recent analysis report
/implement

# Implement from a specific analysis report
/implement analysis-reports/2026-03-26-143052-my-feature.md
```

This command:
1. Creates an implementation brief at `docs/superpowers/briefs/` — a prioritized routing document that links back to the full analysis report
2. Checks for blocking open questions and asks you to resolve them
3. Invokes superpowers' `writing-plans` to generate the execution plan
4. Offers to start execution via `subagent-driven-development` or `executing-plans`

```
Design Spec → /analyze-spec → Analysis Report → /implement → Implementation Brief → writing-plans → Execution Plan → Execute
```

## Common Tasks

### Updating an Agent's Expertise
Edit the agent file directly at `.claude/agents/domain-experts/<agent>.md`. No restart or registration changes needed — the orchestrator reads agent files on each run.

### Evaluating Agent Quality
Run `/eval-agent <agent-name> <spec.md>` to test an agent against a design spec and save the raw output. After making prompt changes, run it again and compare with `/eval-agent --compare <old-result> <new-result>`.

### Reviewing a Completed Implementation
After finishing an implementation, run `/review-cycle <analysis-report> <commit-range>` to compare what agents recommended against what was actually built. The review identifies skipped recommendations, unpredicted changes, and proposes concrete edits to agent prompts based on lessons learned.

### Checking Agent Configuration
Use the `agent-registry-helper` to list agents, validate config, or discover unregistered agent files.

### Adding a New Domain Expert
See [Adding New Agents](adding-new-agents.md) for the step-by-step guide.

## File Locations

| What | Where |
|------|-------|
| Design spec template | `design-specs/template.md` |
| Example specs | `design-specs/examples/` |
| Analysis reports | `analysis-reports/` |
| Eval results | `eval-results/` |
| Review cycles | `review-cycles/` |
| Implementation briefs | `docs/superpowers/briefs/` |
| Agent definitions | `.claude/agents/domain-experts/*.md` |
| Orchestrator | `.claude/agents/orchestrator/design-spec-orchestrator.md` |
| Slash commands | `.claude/commands/` |
| Agent registry | `config/agents.yaml` |

## Related Documents

- [Agent Registry](agent-registry.md) — Full catalog of all agents
- [Developer Guide](developer-guide.md) — How the system works internally
- [Adding New Agents](adding-new-agents.md) — How to add domain experts
