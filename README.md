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
cp specs/template.md specs/my-feature.md

# Edit your design spec
vim specs/my-feature.md

# Analyze the spec with domain experts
/analyze-spec my-feature.md

# Review the analysis report (saved to output/analysis-reports/)

# Implement: creates brief, generates plan, offers execution
/implement output/analysis-reports/2026-03-26-143052-my-feature.md
```

---

## Pipeline

```
Design Spec → /analyze-spec → Analysis Report → /implement → Implementation Brief → writing-plans → Execution Plan → Execute
```

| Artifact | What It Is | Where It Lives |
|----------|-----------|----------------|
| Design Spec | User's input describing what to build | `specs/` |
| Analysis Report | Orchestrator's synthesized expert recommendations | `output/analysis-reports/` |
| Implementation Brief | Prioritized routing document referencing the analysis report | `output/briefs/` |
| Execution Plan | Granular task-based plan with exact code | `output/plans/` |

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

See the [Guide](docs/agentic-workflow/guide.md) for details.

---

## Domain Experts

All agents configured in [`config/agents.yaml`](config/agents.yaml):

| Agent | Domain | Model |
|-------|--------|-------|
| **go-expert** | Go language and idioms | Opus |
| **k8s-expert** | Kubernetes patterns | Opus |
| **controller-expert** | Controller-runtime patterns | Opus |
| **crd-expert** | Custom Resource Definitions | Opus |
| **unit-test-expert** | Unit testing practices | Opus |
| **e2e-test-expert** | E2E testing with Ginkgo | Opus |
| **coding-expert** | General coding practices | Opus |

---

## Directory Structure

See `CLAUDE.md` for the full project structure tree.

---

## Extending the System

Adding new domain experts is straightforward:

1. Copy agent template from `.claude/agents/templates/`
2. Customize for your domain with comprehensive expertise in prompt
3. Add to `config/agents.yaml`
4. **No orchestrator changes needed**

See the [Guide](docs/agentic-workflow/guide.md#adding-new-agents) for detailed steps.

---

## Documentation

- [Guide](docs/agentic-workflow/guide.md) — Complete guide: usage, architecture, internals, agent catalog, and extending the system

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
- **Models**: Claude Opus 4.6
- **Configuration**: YAML
- **Documentation**: Markdown
- **Tools**: Read, Grep, Glob, WebSearch, WebFetch

---

**Project Started:** 2026-03-25
