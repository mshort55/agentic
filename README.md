# Agentic Workflow System

Multi-agent system for analyzing design specifications and providing comprehensive implementation guidance through specialized domain expert agents.

---

## Overview

The Agentic Workflow System coordinates multiple domain expert agents to analyze design specifications in parallel, synthesizing their recommendations into actionable analysis reports.

**Key Features:**
- **7 Domain Expert Agents**: Go, Kubernetes, Controllers, CRDs, Unit Testing, E2E Testing, Coding
- **Parallel Execution**: All agents run simultaneously for speed
- **Smart Synthesis**: Intelligent combination and conflict resolution
- **Extensible**: Add new domains without code changes
- **Configuration-Driven**: Declarative YAML-based agent registry
- **Superpowers Integration**: Bridges analysis to TDD-driven execution (requires `superpowers` plugin)

---

## Quick Start

```bash
# Create a design spec from template
cp design-specs/template.md design-specs/my-feature.md

# Edit your design spec
vim design-specs/my-feature.md

# Analyze the spec with domain experts
/analyze-spec my-feature.md

# Review the analysis report (saved to analysis-reports/)

# Implement: creates brief, generates plan, offers execution
/implement analysis-reports/2026-03-26-143052-my-feature.md
```

---

## Pipeline

```
Design Spec → /analyze-spec → Analysis Report → /implement → Implementation Brief → writing-plans → Execution Plan → Execute
```

| Artifact | What It Is | Where It Lives |
|----------|-----------|----------------|
| Design Spec | User's input describing what to build | `design-specs/` |
| Analysis Report | Orchestrator's synthesized expert recommendations | `analysis-reports/` |
| Implementation Brief | Prioritized routing document referencing the analysis report | `docs/superpowers/briefs/` |
| Execution Plan | Granular task-based plan with exact code | `docs/superpowers/plans/` |

---

## Architecture

```
Design Spec
    │
    ▼
Orchestrator ──► Determines relevant experts
    │           Launches in parallel
    ├─────┬─────┬─────┬─────┬─────┬─────┐
    ▼     ▼     ▼     ▼     ▼     ▼     ▼
  Go    K8s  Ctrl  CRD  Unit  E2E  Code
Expert Expert Expert Expert Test  Test Expert
    │     │     │     │     │     │     │
    └─────┴─────┴─────┴─────┴─────┴─────┘
                    │
                    ▼
         Analysis Report (saved)
```

See [Architecture Documentation](docs/agentic-workflow/architecture.md) for details.

---

## Domain Experts

All agents configured in [`config/agents.yaml`](config/agents.yaml):

| Agent | Domain | Priority | Model |
|-------|--------|----------|-------|
| **go-expert** | Go language and idioms | High | Opus |
| **k8s-expert** | Kubernetes patterns | High | Opus |
| **controller-expert** | Controller-runtime patterns | High | Opus |
| **crd-expert** | Custom Resource Definitions | Medium | Opus |
| **unit-test-expert** | Unit testing practices | High | Sonnet |
| **e2e-test-expert** | E2E testing with Ginkgo | Medium | Sonnet |
| **coding-expert** | General coding practices | Medium | Sonnet |

---

## Directory Structure

```
.claude/
├── commands/
│   ├── analyze-spec.md            # /analyze-spec slash command
│   ├── implement.md              # /implement slash command
│   └── eval-agent.md            # /eval-agent slash command
├── agents/
│   ├── orchestrator/              # Orchestrator agent
│   ├── domain-experts/            # 7 domain expert agents
│   ├── templates/                 # Agent template
│   └── tools/                     # Registry helper
config/
└── agents.yaml                    # Agent registry and configuration
design-specs/                      # User-written design specs (input)
├── template.md
└── examples/
analysis-reports/                  # Saved analysis reports (from /analyze-spec)
eval-results/                      # Saved agent eval outputs (from /eval-agent)
docs/
├── agentic-workflow/              # System documentation
└── superpowers/
    ├── briefs/                    # Implementation briefs (from /implement)
    └── plans/                     # Execution plans (from writing-plans)
ai-docs/                           # Historical planning & research docs
```

---

## Extending the System

Adding new domain experts is straightforward:

1. Copy agent template from `.claude/agents/templates/`
2. Customize for your domain with comprehensive expertise in prompt
3. Add to `config/agents.yaml`
4. **No orchestrator changes needed**

See [Adding New Agents](docs/agentic-workflow/adding-new-agents.md) for the detailed guide.

---

## Documentation

- [User Guide](docs/agentic-workflow/user-guide.md) — How to use the system
- [Developer Guide](docs/agentic-workflow/developer-guide.md) — How the system works internally
- [Architecture](docs/agentic-workflow/architecture.md) — System design and diagrams
- [Agent Registry](docs/agentic-workflow/agent-registry.md) — Catalog of all agents
- [Adding New Agents](docs/agentic-workflow/adding-new-agents.md) — How to add domain experts

---

## Design Principles

1. **Parallelization First** - All agents run simultaneously
2. **Domain Expertise** - Each agent is expert in one area
3. **Extensibility by Design** - No code changes for new domains
4. **Configuration Over Code** - Declarative definitions
5. **Synthesis Over Aggregation** - Smart recommendation combination

---

## Technology Stack

- **Platform**: Claude Code
- **Models**: Claude Opus 4.6, Sonnet 4.5
- **Configuration**: YAML
- **Documentation**: Markdown
- **Tools**: Read, Grep, Glob, WebSearch, Bash

---

**Project Started:** 2026-03-25
