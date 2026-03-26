# Agentic Workflow Architecture

**Version:** 1.0
**Last Updated:** 2026-03-25
**Status:** Active

---

## Table of Contents

1. [System Overview](#system-overview)
2. [Architecture Diagram](#architecture-diagram)
3. [Components](#components)
4. [Data Flow](#data-flow)
5. [Agent Interaction Patterns](#agent-interaction-patterns)
6. [Synthesis Algorithm](#synthesis-algorithm)
7. [Extensibility](#extensibility)
8. [Superpowers Integration](#superpowers-integration)
9. [Continuous Improvement](#continuous-improvement)

---

## System Overview

The Agentic Workflow system is a multi-agent architecture designed to analyze design specifications and provide comprehensive implementation guidance by consulting specialized domain expert agents in parallel.

### Purpose

Enable developers to:
- Receive expert guidance across multiple domain areas simultaneously
- Get consistent, actionable analysis reports
- Leverage best practices without manual consultation
- Reduce code review cycles through upfront analysis
- Scale expertise across the team

### Key Principles

1. **Parallelization First**: All agents run simultaneously for maximum speed
2. **Separation of Concerns**: Each agent is expert in one domain
3. **Extensibility by Design**: Adding new domain areas requires no code changes
4. **Configuration Over Code**: Agents are defined declaratively
5. **Synthesis Over Aggregation**: Recommendations are synthesized, not just collected

---

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                          USER                                    │
│                            │                                     │
│                            ▼                                     │
│                  ┌──────────────────┐                           │
│                  │  Design Spec     │                           │
│                  │  (Markdown File) │                           │
│                  └──────────────────┘                           │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    ORCHESTRATOR LAYER                            │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │  Design Spec Orchestrator                                  │ │
│  │  • Reads and parses design spec                            │ │
│  │  • Loads agent registry (agents.yaml)                      │ │
│  │  • Determines relevant domain expert agents                     │ │
│  │  • Launches agents in parallel                             │ │
│  │  • Collects and synthesizes results                        │ │
│  │  • Generates analysis report                           │ │
│  └────────────────────────────────────────────────────────────┘ │
└────────────────────────────┬────────────────────────────────────┘
                             │
                ┌────────────┼────────────┐
                │            │            │
                ▼            ▼            ▼
┌─────────────────────────────────────────────────────────────────┐
│                   DOMAIN EXPERT LAYER                            │
│                                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐        │
│  │    Go    │  │   K8s    │  │Controller│  │   CRD    │        │
│  │  Expert  │  │  Expert  │  │  Expert  │  │  Expert  │        │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘        │
│                                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                      │
│  │   Unit   │  │   E2E    │  │  Coding  │                      │
│  │   Test   │  │   Test   │  │  Expert  │                      │
│  │  Expert  │  │  Expert  │  │          │                      │
│  └──────────┘  └──────────┘  └──────────┘                      │
│                                                                  │
│  Each agent:                                                     │
│  • Analyzes design spec from domain perspective                 │
│  • Searches codebase for patterns                               │
│  • Researches latest best practices                             │
│  • Identifies risks and concerns                                │
│  • Provides prioritized recommendations                         │
└────────────────────────────┬────────────────────────────────────┘
                             │
                ┌────────────┼────────────┐
                │            │            │
                ▼            ▼            ▼
┌─────────────────────────────────────────────────────────────────┐
│                      TOOL LAYER                                  │
│                                                                  │
│  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐   │
│  │  Read  │  │  Grep  │  │  Glob  │  │  Web   │  │  Web   │   │
│  │        │  │        │  │        │  │ Search │  │ Fetch  │   │
│  └────────┘  └────────┘  └────────┘  └────────┘  └────────┘   │
│                                                                  │
│  Agents use tools to:                                           │
│  • Read existing code and documentation                         │
│  • Search for patterns in codebase                              │
│  • Find similar implementations                                 │
│  • Research latest best practices online                        │
│  • Validate assumptions                                         │
└─────────────────────────────────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    KNOWLEDGE BASE LAYER                          │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │  Codebase (Existing Code)                                   │ │
│  │  • Existing patterns to reference                           │ │
│  │  • Utilities and helpers to leverage                        │ │
│  │  • Conventions to follow                                    │ │
│  └────────────────────────────────────────────────────────────┘ │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │  External Knowledge (Web)                                   │ │
│  │  • Latest best practices                                    │ │
│  │  • Official documentation                                   │ │
│  │  • Industry standards                                       │ │
│  └────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                       OUTPUT LAYER                               │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │  Analysis Report                                            │ │
│  │  • Executive summary                                        │ │
│  │  • Prioritized recommendations (High/Med/Low)               │ │
│  │  • Implementation steps by phase                            │ │
│  │  • Testing strategy                                         │ │
│  │  • Risks and mitigations                                    │ │
│  │  • Existing patterns to leverage                            │ │
│  │  • Open questions                                           │ │
│  │  • Full agent reports (appendix)                            │ │
│  └────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
```

---

## Components

### 1. Orchestrator

**Location:** `.claude/agents/orchestrator/design-spec-orchestrator.md`

**Responsibilities:**
- Parse and understand design specifications
- Load agent registry from `config/agents.yaml`
- Determine which domain expert agents are relevant
- Launch multiple agents in parallel (using Claude Code's Agent tool)
- Collect results from all agents
- Synthesize recommendations into coherent plan
- Resolve conflicts between agent recommendations
- Generate structured analysis report

**Key Algorithms:**
- **Agent Selection**: Match design spec content against agent triggers
- **Parallel Execution**: Launch all selected agents simultaneously
- **Synthesis**: Categorize, merge, and prioritize recommendations
- **Conflict Resolution**: Analyze trade-offs when agents disagree

### 2. Domain Expert Agents

**Location:** `.claude/agents/domain-experts/*.md`

7 specialized agents covering: Go, Kubernetes, Controllers, CRDs, Unit Testing, E2E Testing, and General Coding. See [Agent Registry](agent-registry.md) for the full catalog with expertise areas, triggers, and tools.

Each agent is a markdown file with YAML frontmatter (name, description, triggers) and a prompt body containing domain expertise, analysis framework, and output format. Infrastructure config (model, tools) lives in `config/agents.yaml`.

### 3. Agent Registry

**Location:** `config/agents.yaml`

**Purpose:**
- Central registry of all available agents
- Configuration for agent behavior
- Extensibility settings
- Orchestrator configuration

**Key Sections:**
- `domain_experts`: All domain expert agents
- `orchestrator_agents`: Orchestrator configuration
- `paths`: Agent template and directory locations
- `orchestrator`: Default analysis mode

### 4. Knowledge Base

**Locations:**
- Agent prompts: Domain expertise embedded in agent definitions
- Codebase itself: Existing patterns and conventions
- Web: Latest best practices (via WebSearch)

**Agent Expertise Structure:**
Each agent contains comprehensive domain knowledge in their prompt:
- Core principles
- Best practices by category
- Anti-patterns to avoid
- Common pitfalls
- Tools and libraries
- Testing guidelines
- Analysis framework

### 5. Tools

Agents use Claude Code tools:
- **Read**: Read files and documentation
- **Grep**: Search codebase for patterns
- **Glob**: Find files by pattern
- **WebSearch**: Research latest best practices
- **WebFetch**: Fetch specific web pages for detailed reference

---

## Data Flow

### Step-by-Step Flow

```
1. User creates design spec
   ↓
2. User invokes: /analyze-spec my-feature.md
   ↓
3. Orchestrator reads design spec
   ↓
4. Orchestrator loads agent registry (agents.yaml)
   ↓
5. Orchestrator determines relevant agents
   │
   ├─ Parse spec content
   ├─ Match against agent triggers
   └─ Consider enabled agents
   ↓
6. Orchestrator launches agents in PARALLEL
   │
   ├─ Agent(go-expert, prompt="Analyze spec from Go perspective...")
   ├─ Agent(k8s-expert, prompt="Analyze spec from K8s perspective...")
   ├─ Agent(controller-expert, prompt="Analyze spec from controller perspective...")
   ├─ Agent(crd-expert, prompt="Analyze spec from CRD perspective...")
   ├─ Agent(unit-test-expert, prompt="Analyze spec from unit testing perspective...")
   ├─ Agent(e2e-test-expert, prompt="Analyze spec from e2e testing perspective...")
   └─ Agent(coding-expert, prompt="Analyze spec from coding practices perspective...")
   ↓
7. Each agent independently:
   ├─ Reads design spec
   ├─ Analyzes from domain perspective
   ├─ Searches codebase (Read, Grep, Glob)
   ├─ Researches best practices (WebSearch)
   ├─ Identifies patterns to leverage
   ├─ Flags risks and concerns
   └─ Returns structured recommendations
   ↓
8. Orchestrator collects all agent results
   ↓
9. Orchestrator synthesizes recommendations
   │
   ├─ Categorize by priority (High/Medium/Low)
   ├─ Group by implementation area (API, Controller, Tests, etc.)
   ├─ Identify overlaps (multiple agents agree)
   ├─ Resolve conflicts (agents disagree)
   ├─ Merge risks and concerns
   └─ Generate unified view
   ↓
10. Orchestrator generates analysis report
    │
    ├─ Executive summary
    ├─ Prioritized recommendations
    ├─ Phased implementation steps
    ├─ Testing strategy
    ├─ Risk assessment
    ├─ Questions for clarification
    └─ Full agent reports (appendix)
    ↓
11. Present plan to user
    ↓
12. User reviews and proceeds with implementation
```

### Information Exchange

**Orchestrator → Agents:**
- Full design spec content
- Specific analysis request
- Expected output format

**Agents → Orchestrator:**
- Summary of analysis
- Key findings
- Recommendations (High/Medium/Low priority)
- Risks and concerns
- Existing patterns to leverage
- Questions for clarification

**Agents → Tools:**
- File paths to read
- Search patterns
- Web queries

**Tools → Agents:**
- File contents
- Search results
- Web research findings

---

## Agent Interaction Patterns

### Pattern 1: Parallel Analysis (Primary)

```
Orchestrator
    │
    ├──────┬──────┬──────┬──────┬──────┬──────┐
    ▼      ▼      ▼      ▼      ▼      ▼      ▼
  Agent1 Agent2 Agent3 Agent4 Agent5 Agent6 Agent7
    │      │      │      │      │      │      │
    └──────┴──────┴──────┴──────┴──────┴──────┘
                         │
                         ▼
                   Orchestrator
                   (Synthesis)
```

**When**: Default mode for all design spec analyses
**Why**: Maximum speed, isolated contexts, true parallelization
**Trade-off**: Higher token usage, but much faster results

### Pattern 2: Conditional Analysis

```
Orchestrator
    │
    ├── triggers match? ──► Agent1
    ├── triggers match? ──► Agent2
    └── triggers match? ──X Agent3 (skipped)
                          │
                          ▼
                    Orchestrator
                    (Synthesis)
```

**When**: Smart mode - only invoke agents whose triggers match
**Why**: Optimize for cost and speed on focused changes
**Trade-off**: May miss non-obvious concerns

---

## Synthesis Algorithm

**Step 1: Categorization**
```
For each agent recommendation:
  ├─ Extract priority (High/Medium/Low)
  ├─ Identify implementation area (API/Controller/Tests/etc.)
  └─ Tag with source agent
```

**Step 2: Grouping**
```
Group recommendations by:
  ├─ Priority level
  ├─ Implementation area
  └─ Related concerns
```

**Step 3: Overlap Detection**
```
If multiple agents recommend same thing:
  ├─ Flag as "strongly recommended" (consensus)
  ├─ Combine rationales from all agents
  └─ Increase priority if borderline
```

**Step 4: Conflict Resolution**
```
If agents disagree:
  ├─ Analyze trade-offs from each perspective
  ├─ Consider project context and constraints
  ├─ Make informed decision OR
  └─ Flag for human review with pros/cons
```

**Step 5: Risk Aggregation**
```
Collect all risks:
  ├─ Remove duplicates
  ├─ Identify cross-cutting risks
  ├─ Prioritize by severity × likelihood
  └─ Link to mitigations from agents
```

**Step 6: Plan Generation**
```
Create analysis report:
  ├─ Executive summary (synthesis overview)
  ├─ Critical recommendations (High priority + consensus)
  ├─ Important recommendations (High priority OR Medium + consensus)
  ├─ Nice-to-have (Medium/Low priority)
  ├─ Phased implementation steps
  ├─ Testing strategy (from test experts)
  ├─ Risk assessment (aggregated)
  └─ Agent reports (full details in appendix)
```

---

## Extensibility

See [Adding New Agents](adding-new-agents.md) for the step-by-step guide. No orchestrator changes needed — agents are auto-discovered from the registry.

---

## Superpowers Integration

[Superpowers](https://github.com/obra/superpowers) is an agentic skills framework for systematic software development using TDD, worktrees, and task breakdown. It complements our system: we produce the "what and why" (analysis reports), superpowers handles the "how and when" (plan generation and code implementation).

```
Design Spec → /analyze-spec → Analysis Report → /implement → Implementation Brief → writing-plans → Execution Plan → Execute
```

**Artifacts and boundaries:**

| Artifact | Produced By | Consumed By | Location |
|----------|-------------|-------------|----------|
| Design Spec | User | `/analyze-spec` | `specs/` |
| Analysis Report | Orchestrator | `/implement` | `output/analysis-reports/` |
| Implementation Brief | `/implement` | Superpowers `writing-plans` | `output/briefs/` |
| Execution Plan | Superpowers `writing-plans` | Superpowers subagents | `output/plans/` |

Integration points:
- `/implement` creates a prioritized implementation brief, invokes `writing-plans` to generate the execution plan, and offers to start execution
- Superpowers handles task decomposition, code generation, TDD ordering, and subagent execution
- Feedback loop: Implementation outcomes improve future recommendations

---

## Continuous Improvement

Two commands support iterative improvement of domain expert agents:

**`/eval-agent`** — Evaluate agent output quality
- Runs a single agent against a design spec and saves the raw output to `output/eval-results/`
- Compare two saved results after prompt changes: `/eval-agent --compare <file1> <file2>`
- Use this before and after editing an agent's prompt to verify improvements

**`/review-cycle`** — Post-implementation review
- After completing an implementation, compares analysis report recommendations against the actual code changes (git diff)
- Maps each recommendation to followed / skipped / unpredicted
- Asks structured questions about what worked and what didn't
- Proposes concrete edits to agent prompt files based on lessons learned
- All proposals require human approval before applying
- Reviews saved to `output/review-cycles/` for historical reference

```
Design Spec → Analysis → Implementation → /review-cycle → Agent Improvements → /eval-agent (verify)
```

---

## References

- [User Guide](user-guide.md) — How to use the system
- [Developer Guide](developer-guide.md) — How the system works internally
- [Agent Registry](agent-registry.md) — Catalog of all agents
- [Adding New Agents](adding-new-agents.md) — Step-by-step guide
- [Agent Registry Config](../../config/agents.yaml) — Source of truth for agent configuration
- [Design Spec Template](../../specs/template.md)
