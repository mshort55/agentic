# Agentic Workflow Guide

Complete guide to using, understanding, and extending the agentic workflow system.

---

## Table of Contents

1. [Using the System](#using-the-system)
2. [Architecture](#architecture)
3. [How It Works Internally](#how-it-works-internally)
4. [Agent Catalog](#agent-catalog)
5. [Adding New Agents](#adding-new-agents)
6. [Extending Existing Agents](#extending-existing-agents)
7. [Continuous Improvement](#continuous-improvement)
8. [Troubleshooting](#troubleshooting)

---

## Using the System

You write a design spec describing a feature, bug fix, or refactor. The system consults specialized domain expert agents in parallel — Go, Kubernetes, controllers, CRDs, unit testing, e2e testing, and general coding — then synthesizes their recommendations into a single analysis report.

### Quick Start

1. Write a design spec (copy `specs/template.md` as a starting point)
2. Run `/analyze-spec your-spec.md`
3. Review the analysis report (saved to `output/analysis-reports/`)
4. Run `/implement` to create the brief, generate the plan, and start execution
5. Execute the plan via subagent-driven-development or executing-plans

### Writing Design Specs

Use the template at `specs/template.md`. The most important sections are:

- **Overview**: What problem are you solving? (2-3 paragraphs)
- **Goals / Non-Goals**: What's in scope and what isn't
- **Proposed Changes**: API, controller, infrastructure changes
- **Testing Strategy**: What tests are needed
- **Domain Experts to Consult**: Check the boxes for relevant domains

The "Domain Experts to Consult" checkboxes help the orchestrator in smart mode, but in full mode (default) all agents are consulted regardless.

See `specs/examples/` for reference specs at different complexity levels:
- `simple-bugfix.md` — minimal scope, 2-3 agents
- `new-feature-with-api.md` — full scope, all 7 agents
- `improve-e2e-tests.md` — testing-focused
- `controller-optimization.md` — performance-focused
- `add-rbac-support.md` — security/cross-cutting

### Using `/analyze-spec`

```bash
# Full analysis with all agents (default)
/analyze-spec your-spec.md

# Smart — auto-detect relevant agents from spec content
/analyze-spec your-spec.md --smart

# Focused — specify exactly which agents
/analyze-spec your-spec.md --focus=crd,controller,go
```

#### Agent short names for `--focus`

| Short Name    | Agent              |
|---------------|--------------------|
| `go`          | go-expert          |
| `k8s`         | k8s-expert         |
| `controller`  | controller-expert  |
| `crd`         | crd-expert         |
| `unit-test`   | unit-test-expert   |
| `e2e-test`    | e2e-test-expert    |
| `coding`      | coding-expert      |

#### Which mode to use

| Situation | Mode | Why |
|-----------|------|-----|
| New feature, unclear scope | (default) | All agents, don't miss anything |
| You know what domains matter | `--focus=x,y` | Skip irrelevant agents |
| Medium change, want auto-detection | `--smart` | Let triggers decide |

### Understanding the Output

The analysis report is saved to `output/analysis-reports/` and has these sections:

- **Executive Summary** — High-level overview and key decisions
- **Key Recommendations** — Prioritized as Critical / Important / Nice to Have, with source agent attribution
- **Implementation Steps** — Phased plan with files to modify and domain-specific guidelines
- **Testing Strategy** — Unit and e2e test recommendations from the testing experts
- **Risks & Mitigation** — Aggregated risks from all agents, with severity and mitigation
- **Existing Patterns** — Codebase patterns the agents found that you should build on
- **Open Questions** — Things that need human decisions before implementation
- **Agent Reports** — Full output from each agent (appendix)

When multiple agents agree on a recommendation, it's flagged as consensus. When they disagree, the orchestrator resolves the conflict or flags it for your review.

### Common Workflows

#### New Feature
1. Write a thorough design spec with API, controller, and testing sections
2. Run `/analyze-spec` (full mode)
3. Review critical recommendations and resolve open questions
4. Run `/implement` to create the brief, generate the plan, and start execution
5. Use the testing strategy section when writing tests

#### Bug Fix
1. Write a minimal design spec: overview, proposed fix, regression test
2. Run `/analyze-spec your-spec.md --focus=go,unit-test`
3. Focus on the go-expert and unit-test-expert recommendations
4. Implement fix and regression test

#### Refactoring
1. Document what you're refactoring and why
2. Run `/analyze-spec your-spec.md --focus=go,coding`
3. Review existing patterns section — don't break conventions
4. Follow recommendations for safe refactoring

#### Performance Optimization
1. Document the bottleneck, measurements, and proposed optimization
2. Run `/analyze-spec your-spec.md --focus=go,controller,coding`
3. Check if agents suggest measuring before optimizing
4. Follow benchmark test recommendations

### After the Analysis

You can ask Claude to:
- Dive deeper into a specific recommendation
- Consult additional agents you didn't include

### Using `/implement`

**Requires:** The superpowers plugin must be installed for plan generation and execution. Install with `/plugin install superpowers@claude-plugins-official`, then `/reload-plugins`. The `/implement` command will check for this and tell you if it's missing.

With the analysis report in hand, run `/implement` to handle everything from brief creation through execution:

```bash
# Implement from the most recent analysis report
/implement

# Implement from a specific analysis report
/implement output/analysis-reports/2026-03-26-143052-my-feature.md
```

This command:
1. Creates an implementation brief at `output/briefs/` — a prioritized routing document that links back to the full analysis report
2. Checks for blocking open questions and asks you to resolve them
3. Invokes superpowers' `writing-plans` to generate the execution plan
4. Offers to start execution via `subagent-driven-development` or `executing-plans`

### Superpowers Integration

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

---

## Architecture

### Architecture Diagram

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
│  │  • Determines relevant domain expert agents                │ │
│  │  • Launches agents in parallel                             │ │
│  │  • Collects and synthesizes results                        │ │
│  │  • Generates analysis report                               │ │
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
└─────────────────────────────────────────────────────────────────┘
```

### Key Principles

1. **Parallelization First**: All agents run simultaneously for maximum speed
2. **Separation of Concerns**: Each agent is expert in one domain
3. **Extensibility by Design**: Adding new domain areas requires no code changes
4. **Configuration Over Code**: Agents are defined declaratively
5. **Synthesis Over Aggregation**: Recommendations are synthesized, not just collected

### Components

**Orchestrator** — `.claude/agents/orchestrator/design-spec-orchestrator.md`
- Parses design specs, loads agent registry, selects agents, launches them in parallel, synthesizes results, resolves conflicts, generates reports

**Domain Expert Agents** — `.claude/agents/domain-experts/*.md`
- 7 specialized agents, each a markdown file with YAML frontmatter (name, description, triggers) and a prompt body containing domain expertise. Infrastructure config (model, tools) lives in `config/agents.yaml`.

**Agent Registry** — `config/agents.yaml`
- Central list of agents with infrastructure config: name, type, enabled, model, tools
- Orchestrator defaults and agent directory paths

**Tools** — Agents use Claude Code tools:
- **Read**: Read files and documentation
- **Grep**: Search codebase for patterns
- **Glob**: Find files by pattern
- **WebSearch**: Research latest best practices
- **WebFetch**: Fetch specific web pages for detailed reference

### Agent Interaction Patterns

**Parallel Analysis (default)** — All agents run simultaneously, orchestrator synthesizes results. Maximum speed, higher token usage.

**Conditional Analysis (smart mode)** — Only agents whose triggers match the spec content are invoked. Optimizes cost but may miss non-obvious concerns.

### Synthesis Algorithm

1. **Categorize** — Extract priority (High/Medium/Low), identify implementation area, tag with source agent
2. **Group** — By priority level, implementation area, and related concerns
3. **Detect Consensus** — Flag recommendations from multiple agents as "strongly recommended", combine rationales
4. **Resolve Conflicts** — Analyze trade-offs, consider project context, decide or flag for human review
5. **Aggregate Risks** — Remove duplicates, identify cross-cutting risks, prioritize by severity x likelihood
6. **Generate Report** — Executive summary, prioritized recommendations, phased steps, testing strategy, risk assessment, full agent reports in appendix

---

## How It Works Internally

### Key Files

See `CLAUDE.md` for the full project structure tree. The most important files:

- `.claude/commands/` — slash commands (`analyze-spec`, `implement`, `eval-agent`, `review-cycle`)
- `.claude/agents/orchestrator/design-spec-orchestrator.md` — main orchestrator logic
- `.claude/agents/domain-experts/*.md` — 7 domain expert agent definitions
- `.claude/agents/templates/` — agent template and shared prompt template
- `config/agents.yaml` — agent registry and configuration

### How the Orchestrator Works

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

### How Agents Work

Each agent is a markdown file with:
- **YAML frontmatter**: name, description, triggers (infrastructure config like model and tools lives in `config/agents.yaml`)
- **Prompt body**: role description, expertise areas, best practices, pitfalls, analysis framework, output format, key principles

When the orchestrator launches an agent, it sends the design spec content as the prompt. The agent then:
1. Analyzes the spec from its domain perspective
2. Searches the codebase for existing patterns (Grep, Glob, Read)
3. Optionally researches latest practices (WebSearch)
4. Returns structured recommendations

Agents are independent — they don't see each other's output. The orchestrator handles synthesis.

### Modifying the System

**Modifying agent expertise** — Edit the agent's `.md` file directly. Changes take effect on the next run — no restart or re-registration needed.

**Changing orchestrator behavior** — Edit `.claude/agents/orchestrator/design-spec-orchestrator.md`. The synthesis algorithm, output format, and agent selection logic are all defined there.

**Changing slash commands:**
- `.claude/commands/analyze-spec.md` — argument parsing and orchestrator invocation
- `.claude/commands/implement.md` — creates brief, invokes writing-plans, offers execution
- `.claude/commands/eval-agent.md` — agent output quality evaluation and comparison
- `.claude/commands/review-cycle.md` — post-implementation review and agent improvement

**Adjusting configuration** — Edit `config/agents.yaml` to change:
- Which agents are enabled/disabled
- Agent model and tool configuration
- Orchestrator defaults (analysis mode)
- Agent template and directory paths

---

## Agent Catalog

All agents configured in `config/agents.yaml`. All use Opus model and tools: Read, Grep, Glob, WebSearch, WebFetch.

### go-expert

**Description:** Go language and idioms

**Expertise:** Idiomatic patterns, error handling (`errors.Is`/`errors.As`, wrapping), concurrency (goroutines, channels, sync, context), project structure (`internal/`, `pkg/`, `cmd/`), testing (table-driven, testify, gomock, benchmarks, `synctest`), performance and memory management.

**Triggers:** `go`, `golang`, `*.go files`, `goroutines`, `channels`, `interfaces`

**When to use:** Any design spec involving Go code changes, new packages, concurrency concerns, or performance work.

---

### k8s-expert

**Description:** Kubernetes patterns and best practices

**Expertise:** Resource patterns and manifests, deployment strategies, service discovery and networking, ConfigMaps and Secrets, RBAC and security, resource limits and requests, health checks (liveness, readiness, startup probes), labels, selectors, namespace organization.

**Triggers:** `kubernetes`, `k8s`, `deployment`, `service`, `configmap`, `secret`, `rbac`

**When to use:** Design specs involving Kubernetes resources, RBAC, networking, deployment configuration, or infrastructure changes.

---

### controller-expert

**Description:** Kubernetes controller patterns

**Expertise:** controller-runtime patterns, reconciliation loop design, event filtering and predicates, error handling and retry logic, leader election, finalizers and garbage collection, status conditions, watches and indexers, owner references, performance and scalability.

**Triggers:** `controller`, `reconcile`, `controller-runtime`, `operator`, `watch`

**When to use:** Design specs involving controller logic, reconciliation loops, operator patterns, or controller performance.

---

### crd-expert

**Description:** Custom Resource Definitions

**Expertise:** API design and versioning, kubebuilder markers and validation, OpenAPI schema design, CEL validation (GA in K8s 1.29), validation ratcheting, status subresource, printer columns, webhook validation, conversion webhooks, backward compatibility.

**Triggers:** `crd`, `custom resource`, `api`, `kubebuilder`, `webhook`

**When to use:** Design specs involving CRD/API changes, validation rules, API versioning, or webhook logic.

---

### unit-test-expert

**Description:** Unit testing patterns and practices

**Expertise:** Table-driven tests, test organization and structure, mocking strategies (gomock, interfaces), test helpers and utilities, coverage analysis, test naming conventions, parallel test execution, benchmark testing (`b.Loop()`), `synctest` for time-dependent code, race detection.

**Triggers:** `unit test`, `_test.go`, `testing`, `mock`, `testify`

**When to use:** Any design spec that needs unit tests, or when evaluating test strategy.

---

### e2e-test-expert

**Description:** End-to-end testing with Ginkgo

**Expertise:** Ginkgo v2 BDD testing, Gomega matchers, test environment setup (envtest, kind), test isolation and cleanup (`DeferCleanup`), flake reduction, parallel execution strategies, `DescribeTableSubtree`, `ReportEntries`, `SpecPriority`, `--gojson-report`, CI/CD integration.

**Triggers:** `e2e`, `ginkgo`, `integration test`, `gomega`

**When to use:** Design specs needing integration or e2e tests, or when improving test infrastructure.

---

### coding-expert

**Description:** General coding best practices

**Expertise:** SOLID principles, DRY/KISS/YAGNI, code organization and modularity, naming conventions, documentation practices, refactoring patterns, technical debt management, security best practices, structured logging (`log/slog`), error handling patterns.

**Triggers:** `code quality`, `clean code`, `refactoring`, `solid`, `code organization`

**When to use:** General code quality concerns, refactoring, or as a complement to domain-specific agents.

---

### design-spec-orchestrator

**Type:** Orchestrator | **Model:** Opus | **Location:** `.claude/agents/orchestrator/design-spec-orchestrator.md`

Reads design specs, selects relevant agents, launches them in parallel, synthesizes their recommendations, resolves conflicts, and generates analysis reports saved to `output/analysis-reports/`. Invoked via `/analyze-spec`.

---

## Adding New Agents

### Step 1: Create Agent Definition

Copy the template and rename for your domain:

```bash
cp .claude/agents/templates/domain-expert-template.md \
   .claude/agents/domain-experts/YOUR-AGENT-NAME.md
```

**Naming convention:** Use lowercase with hyphens. End with `-expert` for consistency.

Examples: `security-expert`, `observability-expert`, `performance-expert`, `migration-expert`

### Step 2: Customize the Agent Definition

Open the new file and replace all template placeholders:

**Frontmatter:**
```yaml
---
name: security-expert
description: Security and compliance expert agent for design spec analysis
triggers:
  - security
  - auth
  - secrets
  - rbac
  - compliance
---
```

Note: Infrastructure config (model, tools) is configured in `config/agents.yaml`, not in the agent frontmatter.

**Key sections to customize:**

1. **Role Description**: What the agent does and its perspective
2. **Areas of Expertise**: Comprehensive list of sub-domains
3. **Best Practices**: Detailed knowledge organized by category
4. **Common Pitfalls**: Anti-patterns and mistakes to flag
5. **Analysis Framework**: How the agent evaluates design specs
6. **Output Format**: Structure for recommendations
7. **Key Principles**: Core rules that guide all recommendations

### Step 3: Embed Domain Expertise

This is the most important step. The agent's value comes from the depth and accuracy of expertise embedded in its prompt. For each area of expertise:

- Document core principles and best practices
- Include concrete code examples where applicable
- List common pitfalls and anti-patterns
- Reference authoritative sources (official docs, RFCs, etc.)
- Provide decision frameworks (when to use X vs Y)
- Cover both fundamentals and advanced topics

**Quality checklist:**
- [ ] Expertise is comprehensive, not just surface-level
- [ ] Advice is current (check latest versions of tools/frameworks)
- [ ] Examples are realistic and follow established patterns
- [ ] Pitfalls include the "why" (not just "don't do X")
- [ ] Key principles are actionable and specific

### Step 4: Configure Triggers

Triggers determine when the orchestrator activates this agent in **smart mode**. Choose triggers that are:

- **Specific enough** to avoid false positives (avoid generic terms like "code" or "fix")
- **Broad enough** to catch relevant design specs
- **Non-overlapping** with other agents when possible (some overlap is fine)

```yaml
triggers:
  - security          # Core domain term
  - authentication    # Specific sub-domain
  - authorization     # Specific sub-domain
  - rbac              # Technical term
  - secrets           # Related concept
  - compliance        # Related concern
  - cve               # Related workflow
```

### Step 5: Register in Agent Registry

Add the new agent to `config/agents.yaml` under `domain_experts`:

```yaml
    - name: security-expert
      type: domain_expert
      enabled: true
      model: opus
      tools:
        - Read
        - Grep
        - Glob
        - WebSearch
        - WebFetch
```

Note: `description` and `triggers` live in the agent's `.md` file frontmatter, not here.

### Step 6: Validate and Test

Run the `agent-registry-helper` to check configuration, then test with a sample spec:

1. Run `/analyze-spec test-spec.md --focus=security`
2. Verify the agent produces comprehensive, actionable recommendations
3. Check the output follows the expected format
4. Ensure recommendations are specific to the design spec, not generic

If using `--focus` mode, add the short name mapping to `.claude/commands/analyze-spec.md`.

### Best Practices for Agents

- **One domain per agent**: Don't create a "security-and-performance-expert"
- **Comprehensive expertise**: Go deep rather than wide
- **Current knowledge**: Research the latest versions and practices before writing the agent
- **Actionable output**: Recommendations should tell the user *what*, *why*, and *how*
- **Codebase awareness**: Instruct the agent to search for existing patterns
- **Triggers**: Be specific (`"rbac"` > `"access"`), include abbreviations (`"kubernetes"` and `"k8s"`), include file patterns (`"*.go"`)
- **Model**: All agents use Opus — since agents run in parallel, using a faster model on some doesn't improve overall analysis time

---

## Extending Existing Agents

To update an existing agent's expertise:

1. Read the current agent definition
2. Research updates (new versions, new best practices, new patterns)
3. Edit the agent file to add/update sections
4. Test with existing and new design specs
5. Verify the agent still produces quality output

**When to update:**
- New major version of a framework (e.g., Go 1.25, Kubernetes 1.31)
- New best practices emerge in the domain
- Feedback indicates the agent missed something important
- Quarterly review (scheduled maintenance)

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

## Troubleshooting

### Agent not triggered in smart mode
- Check triggers in the agent's `.md` frontmatter match design spec content
- Verify agent is `enabled: true`
- Try full mode to confirm agent works at all

### Agent produces generic recommendations
- Deepen the expertise sections in the agent prompt
- Add more specific code examples
- Include project-specific conventions
- Add codebase search instructions

### Agent conflicts with other agents
- Some overlap is expected and healthy (consensus strengthens recommendations)
- If conflicts are frequent, clarify each agent's scope in their prompts
- The orchestrator handles conflict resolution during synthesis

### Agent file not found
- Verify the file is at `.claude/agents/domain-experts/YOUR-AGENT.md`
- Check the filename matches the `name` field in `agents.yaml`
- Run the `agent-registry-helper` discovery scan to detect mismatches

---

## Reference

### File Locations
- Agent template: `.claude/agents/templates/domain-expert-template.md`
- Agent definitions: `.claude/agents/domain-experts/*.md`
- Agent registry: `config/agents.yaml`
- Registry helper: `.claude/agents/tools/agent-registry-helper.md`

### Related
- [README](../../README.md) — Project overview and quick start
- [CLAUDE.md](../../CLAUDE.md) — Project structure and conventions
- [Agent Registry Config](../../config/agents.yaml) — Source of truth for agent configuration
- [Design Spec Template](../../specs/template.md)
