# Agentic Workflow System

Multi-agent system for analyzing design specifications and providing comprehensive implementation guidance through specialized domain expert agents.

---

## Overview

The Agentic Workflow System coordinates multiple domain expert agents to analyze design specifications in parallel, synthesizing their recommendations into actionable implementation plans.

**Key Features:**
- **7 Domain Expert Agents**: Go, Kubernetes, Controllers, CRDs, Unit Testing, E2E Testing, Coding
- **Parallel Execution**: All agents run simultaneously for speed
- **Smart Synthesis**: Intelligent combination and conflict resolution
- **Extensible**: Add new domains without code changes
- **Configuration-Driven**: Declarative YAML-based agent registry

---

## Quick Start

### Current Status: Phase 0 Complete ✅

Foundation established. Ready to begin Phase 1.

See [Phase 0 Summary](ai-docs/phase-0-summary.md) for completion details.

### Using the System (Future)

Once implementation is complete:

```bash
# Create a design spec from template
cp design-specs/template.md design-specs/my-feature.md

# Edit your design spec
vim design-specs/my-feature.md

# Analyze the spec (Phase 3+)
/analyze-spec design-specs/my-feature.md

# Review the generated implementation plan
```

---

## Directory Structure

```
/UbuntuSync/Agentic/
├── .claude/
│   └── agents/
│       ├── domain-experts/   # Domain expert agent definitions (Phase 2)
│       ├── orchestrator/     # Orchestrator agents (Phase 3)
│       ├── templates/        # Agent templates (Phase 1)
│       └── tools/            # Helper tools (Phase 1+)
├── ai-docs/                  # AI-generated planning & documentation
│   ├── agentic-workflow-research.md
│   ├── multi-agent-implementation-plan.md
│   ├── phase-0-summary.md
│   └── TERMINOLOGY_UPDATE.md
├── config/
│   └── agents.yaml          # Agent registry and configuration
├── design-specs/
│   ├── template.md          # Design spec template
│   └── examples/            # Example specs (Phase 5)
├── docs/
│   ├── domain-expertise/    # Domain knowledge docs (Phase 2)
│   └── agentic-workflow/
│       └── architecture.md  # System architecture
└── README.md                # This file
```

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
            Synthesis & Plan
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

## Implementation Roadmap

See [Multi-Agent Implementation Plan](ai-docs/multi-agent-implementation-plan.md) for detailed phases.

### Phase 0: Project Setup ✅ COMPLETE
- Directory structure
- Agent registry configuration
- Design spec template
- Architecture documentation

### Phase 1: Agent Infrastructure (3-4 days)
- Agent template
- Domain expertise documentation template
- Agent registry helper
- skill-creator integration

### Phase 2: Domain Expert Agents (1-2 weeks)
- Build 7 domain expert agents
- Create domain knowledge documentation

### Phase 3: Build Orchestrator (1 week)
- Main orchestrator logic
- Synthesis algorithm
- Implementation plan generation

### Phase 4: Extensibility Framework (3-5 days)
- Agent discovery
- Validation tools
- Adding new agents guide

### Phase 5: Testing & Validation (1 week)
- Create test design specs
- End-to-end testing
- Performance validation

### Phase 6: Documentation (3-5 days)
- User guide
- Developer guide
- Runbook

### Phase 7: Integration (Ongoing)
- Workflow integration
- Team onboarding
- Feedback loop

**Target:** 4-6 weeks to production
**MVP:** 2-3 weeks (Phases 0-3)

---

## Key Documentation

### Planning & Research
- [Agentic Workflow Research](ai-docs/agentic-workflow-research.md) - Initial research and design options
- [Implementation Plan](ai-docs/multi-agent-implementation-plan.md) - Detailed phased implementation
- [Phase 0 Summary](ai-docs/phase-0-summary.md) - Phase 0 completion details
- [Terminology Update](ai-docs/TERMINOLOGY_UPDATE.md) - Naming decisions

### Architecture & Design
- [System Architecture](docs/agentic-workflow/architecture.md) - Complete system design
- [Agent Registry](config/agents.yaml) - Agent configuration

### Templates
- [Design Spec Template](design-specs/template.md) - Template for creating specs

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

## Extending the System

Adding new domain experts is straightforward:

1. Copy agent template from `.claude/agents/templates/`
2. Customize for your domain expertise area
3. Add to `config/agents.yaml`
4. Create domain documentation in `docs/domain-expertise/`
5. **No orchestrator changes needed** ✅

See Phase 4 implementation plan for detailed guide.

---

## Contributing

### Workflow
1. Create design spec from template
2. Analyze with orchestrator (when available)
3. Review recommendations
4. Implement
5. Share feedback to improve agents

### Improving Agents
- Update domain expertise docs
- Refine agent prompts
- Use skill-creator for optimization (future)

---

## Status & Next Steps

**Current Phase:** Phase 0 Complete ✅
**Next Phase:** Phase 1 - Agent Infrastructure Setup

See [Phase 0 Summary](ai-docs/phase-0-summary.md) for what was completed.

See [Implementation Plan](ai-docs/multi-agent-implementation-plan.md) for next steps.

---

## Questions?

- **How does it work?** → [Architecture](docs/agentic-workflow/architecture.md)
- **What was the research?** → [Research Doc](ai-docs/agentic-workflow-research.md)
- **What's the plan?** → [Implementation Plan](ai-docs/multi-agent-implementation-plan.md)
- **What's been done?** → [Phase 0 Summary](ai-docs/phase-0-summary.md)

---

**Project Started:** 2026-03-25
**Status:** Foundation Complete, Ready for Phase 1
