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
6. [Decision Criteria](#decision-criteria)
7. [Extensibility Points](#extensibility-points)
8. [Technology Stack](#technology-stack)

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
│  │  Read  │  │  Grep  │  │  Glob  │  │  Web   │  │  Bash  │   │
│  │        │  │        │  │        │  │ Search │  │        │   │
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

Each agent is a markdown file with YAML frontmatter (name, model, type, tools, triggers) and a prompt body containing domain expertise, analysis framework, and output format.

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
- `extensibility`: Settings for adding new agents
- `monitoring`: Metrics and logging configuration

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
- **Bash**: Execute commands (e.g., test checks)

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
   └─ Consider enabled agents and priorities
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

### Pattern 2: Sequential Analysis (Fallback)

```
Orchestrator
    │
    ▼
  Agent1
    │
    ▼
  Agent2
    │
    ▼
  Agent3
    │
    ▼
Orchestrator
(Synthesis)
```

**When**: Agent dependency exists (rare), or deliberate choice for cost optimization
**Why**: Lower token usage, shared context
**Trade-off**: Slower execution time

### Pattern 3: Conditional Analysis

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

### Pattern 4: Background Execution

```
Orchestrator
    │
    ├──► Agent1 (foreground)
    ├──► Agent2 (foreground)
    └──► Agent3 (background) ──► Notified when complete
         │
         ▼
    Continue work
         │
         ▼
    Agent3 completes ──► Integrate results
```

**When**: Long-running research agents
**Why**: Don't block on optional deep analysis
**Trade-off**: More complex coordination

---

## Decision Criteria

### When to Invoke Which Agents?

Four modes are available: Full (all agents), Smart (trigger-matched), Focused (user-specified), and Quick (high-priority only). See [User Guide](user-guide.md#which-mode-to-use) for when to use each mode.

### Synthesis Algorithm

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

## Extensibility Points

### 1. Adding New Domain Expert Agents

See [Adding New Agents](adding-new-agents.md) for the step-by-step guide. No orchestrator changes needed — agents are auto-discovered from the registry.

### 2. Custom Agent Types

Beyond domain expert agents:
- **Implementation agents**: Can modify code (Edit, Write tools)
- **Research agents**: Deep research (WebSearch focus)
- **Validation agents**: Check compliance, security
- **Migration agents**: Guide migrations

Configure in `agents.yaml` with `type: custom`.

### 3. Integration Points

**Pre-commit hooks:**
```bash
# Validate design spec before commit
claude-code /validate-spec design-specs/my-spec.md
```

**CI/CD pipeline:**
```yaml
- name: Analyze Design Spec
  run: |
    claude-code /analyze-spec $FEATURE.md > analysis.md
    # Upload as artifact or comment on PR
```

**IDE integration:**
```
Right-click design spec → "Analyze with Claude Code"
```

### 4. Configuration Extensions

**Custom orchestration modes:**
```yaml
orchestrator:
  custom_modes:
    security-focused:
      always_include: [security-expert, coding-expert]
      optional: [go-expert, k8s-expert]
```

**Agent sets:**
```yaml
agent_sets:
  api-changes:
    - crd-expert
    - k8s-expert
    - controller-expert
  testing-only:
    - unit-test-expert
    - e2e-test-expert
```

### 5. Tool Extensions

New tools can be added to agents via MCP (Model Context Protocol):
- Custom linters
- Security scanners
- Performance profilers
- Database query tools

---

## Technology Stack

### Core Platform
- **Claude Code**: Agentic execution environment
- **Claude API**: Models (Opus, Sonnet)
- **Agent SDK**: For custom agent implementations (future)

### Configuration
- **YAML**: Agent registry and configuration
- **Markdown**: Agent definitions, documentation, design specs

### Tools (Claude Code Built-in)
- **Read**: File reading
- **Grep**: Content search
- **Glob**: File pattern matching
- **WebSearch**: Internet research
- **Bash**: Command execution

### Storage
- **Filesystem**: All configuration, agents, docs stored as files
- **Git**: Version control for all artifacts
- **No database**: Stateless architecture

### Integration
- **CLI**: `/analyze-spec` command
- **Git hooks**: Pre-commit validation
- **CI/CD**: GitHub Actions, etc.
- **MCP**: Model Context Protocol for tool extensibility

---

## Performance Characteristics

### Latency
- **Agent execution**: 30-90 seconds per agent (parallel)
- **Total analysis**: ~2 minutes (limited by slowest agent)
- **Sequential would be**: ~10+ minutes (7 agents × 90 sec each)

### Token Usage
- **Per agent**: ~5K-15K tokens (input + output)
- **7 agents**: ~50K-100K tokens total
- **Orchestrator**: ~20K-30K tokens (synthesis)
- **Total**: ~70K-130K tokens per analysis

### Throughput
- **Parallel agents**: 7 agents simultaneously
- **Configurable**: `max_parallel_agents` in config
- **Rate limits**: Claude API limits apply

### Scalability
- **Agents**: Easily scale to 10-20 agents
- **Design specs**: No limit
- **Codebase size**: Tools handle large repos efficiently

---

## Security Considerations

### Tool Access
- Agents have READ access to codebase
- No WRITE access (analysis only, not implementation)
- WebSearch is outbound only
- Bash access limited to read-only commands

### Data Privacy
- Design specs may contain sensitive information
- Stays within user's Claude Code session
- No external storage by default
- Logging configurable

### Configuration Security
- `agents.yaml` is version-controlled
- Changes require code review
- Validation on load prevents malformed agents

---

## Failure Modes and Resilience

### Agent Failure
**Scenario**: One agent fails or times out

**Handling:**
- `continue_on_agent_failure: true` (default)
- Orchestrator notes missing analysis
- Continues with remaining agents
- Flags gap in final report
- Suggests manual review of failed domain

### Orchestrator Failure
**Scenario**: Orchestrator crashes during synthesis

**Handling:**
- Agent results are logged
- Can manually review agent outputs
- Re-run orchestrator on cached results (future)

### Invalid Configuration
**Scenario**: `agents.yaml` is malformed

**Handling:**
- Validation on load
- Clear error messages
- Falls back to default configuration

### Network Issues
**Scenario**: WebSearch fails

**Handling:**
- Agents note research limitation
- Rely on embedded expertise in prompts
- Continue with codebase analysis
- Flag reduced confidence in recommendations

---

## Monitoring and Observability

### Metrics Tracked
- Agent usage frequency
- Execution times per agent
- Token consumption
- Recommendation acceptance rate
- Synthesis quality feedback

### Logging
- Agent invocations
- Tool usage
- Errors and warnings
- Synthesis decisions

### Debug Mode
```yaml
monitoring:
  debug_mode: true  # Verbose logging
  log_agent_prompts: true
  log_tool_calls: true
```

---

## Future Enhancements

### Planned Features
1. **Agent memory**: Agents remember past analyses
2. **Learning loop**: Optimize prompts based on feedback
3. **Custom agents via skill-creator**: Generate agents on demand
4. **Agent optimization**: Use skill-creator evals
5. **Caching**: Cache agent results for similar specs
6. **Streaming results**: Show agent progress in real-time

### Research Areas
1. **Agent collaboration**: Agents communicate with each other
2. **Hierarchical agents**: Sub-agents for deep dives
3. **Multi-spec analysis**: Compare multiple design specs
4. **Automated implementation**: Agents write code, not just recommend

### Superpowers Integration

[Superpowers](https://github.com/obra/superpowers) is an agentic skills framework for systematic software development using TDD, worktrees, and task breakdown. It complements our system: we produce the "what and why" (analysis reports), superpowers handles the "how and when" (plan generation and code implementation).

```
Design Spec → /analyze-spec → Analysis Report → /implement → Implementation Brief → writing-plans → Execution Plan → Execute
```

**Artifacts and boundaries:**

| Artifact | Produced By | Consumed By | Location |
|----------|-------------|-------------|----------|
| Design Spec | User | `/analyze-spec` | `design-specs/` |
| Analysis Report | Orchestrator | `/implement` | `analysis-reports/` |
| Implementation Brief | `/implement` | Superpowers `writing-plans` | `docs/superpowers/briefs/` |
| Execution Plan | Superpowers `writing-plans` | Superpowers subagents | `docs/superpowers/plans/` |

Integration points:
- `/implement` creates a prioritized implementation brief, invokes `writing-plans` to generate the execution plan, and offers to start execution
- Superpowers handles task decomposition, code generation, TDD ordering, and subagent execution
- Feedback loop: Implementation outcomes improve future recommendations

### Continuous Improvement

Two commands support iterative improvement of domain expert agents:

**`/eval-agent`** — Evaluate agent output quality
- Runs a single agent against a design spec and saves the raw output to `eval-results/`
- Compare two saved results after prompt changes: `/eval-agent --compare <file1> <file2>`
- Use this before and after editing an agent's prompt to verify improvements

**`/review-cycle`** — Post-implementation review
- After completing an implementation, compares analysis report recommendations against the actual code changes (git diff)
- Maps each recommendation to followed / skipped / unpredicted
- Asks structured questions about what worked and what didn't
- Proposes concrete edits to agent prompt files based on lessons learned
- All proposals require human approval before applying
- Reviews saved to `review-cycles/` for historical reference

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
- [Design Spec Template](../../design-specs/template.md)
