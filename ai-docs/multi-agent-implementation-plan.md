# Multi-Agent System Implementation Plan
## Design Spec Implementation with Specialized Domain Expert Agents

**Goal:** Build an extensible multi-agent system where a main orchestrator reads design specs and consults specialized domain expert agents in parallel for Go, Kubernetes, Controllers, CRDs, Testing, and Coding practices.

**Architecture:** Option 2 - Multi-Agent System with Specialized Agents

---

## Phase 0: Project Setup & Architecture Design

**Objective:** Establish project structure, conventions, and foundational architecture decisions.

**Duration:** 2-3 days

### Tasks

#### 0.1 Create Directory Structure
```bash
/UbuntuSync/
├── .claude/
│   └── agents/                    # Custom agent definitions
│       ├── domain-experts/        # Domain expert agents
│       │   ├── go-expert.md
│       │   ├── k8s-expert.md
│       │   ├── controller-expert.md
│       │   ├── crd-expert.md
│       │   ├── unit-test-expert.md
│       │   ├── e2e-test-expert.md
│       │   └── coding-expert.md
│       ├── orchestrator/          # Orchestrator skills/agents
│       │   └── design-spec-orchestrator.md
│       └── templates/             # Templates for new agents
│           └── domain-expert-template.md
├── docs/
│   └── agentic-workflow/
│       ├── architecture.md        # System architecture docs
│       ├── agent-registry.md      # Catalog of all agents
│       └── adding-new-agents.md   # Guide for extending
├── design-specs/                  # Where design specs live
│   └── template.md               # Design spec template
└── config/
    └── agents.yaml               # Agent configuration registry
```

#### 0.2 Define Agent Registry Schema
Create `config/agents.yaml`:
```yaml
version: "1.0"
agent_registry:
  # Domain expert agents
  domain_experts:
    - name: go-expert
      type: domain_expert
      domain: "Go language and idioms"
      enabled: true
      priority: high
      tools:
        - Read
        - Grep
        - Glob
        - WebSearch
      triggers:
        - "go"
        - "golang"
        - "*.go files"

    - name: k8s-expert
      type: domain_expert
      domain: "Kubernetes patterns and best practices"
      enabled: true
      priority: high
      tools:
        - Read
        - Grep
        - Glob
        - WebSearch
        - Bash
      triggers:
        - "kubernetes"
        - "k8s"
        - "deployment"
        - "service"

    - name: controller-expert
      type: domain_expert
      domain: "Kubernetes controller patterns"
      enabled: true
      priority: high
      tools:
        - Read
        - Grep
        - Glob
        - WebSearch
      triggers:
        - "controller"
        - "reconcile"
        - "controller-runtime"

    - name: crd-expert
      type: domain_expert
      domain: "Custom Resource Definitions"
      enabled: true
      priority: medium
      tools:
        - Read
        - Grep
        - Glob
        - WebSearch
      triggers:
        - "crd"
        - "custom resource"
        - "api"

    - name: unit-test-expert
      type: domain_expert
      domain: "Unit testing patterns and practices"
      enabled: true
      priority: high
      tools:
        - Read
        - Grep
        - Glob
        - WebSearch
      triggers:
        - "unit test"
        - "_test.go"
        - "testing"

    - name: e2e-test-expert
      type: domain_expert
      domain: "End-to-end testing with Ginkgo"
      enabled: true
      priority: medium
      tools:
        - Read
        - Grep
        - Glob
        - WebSearch
        - Bash
      triggers:
        - "e2e"
        - "ginkgo"
        - "integration test"

    - name: coding-expert
      type: domain_expert
      domain: "General coding best practices"
      enabled: true
      priority: medium
      tools:
        - Read
        - Grep
        - Glob
      triggers:
        - "code quality"
        - "clean code"
        - "refactoring"

  # Orchestrator agents
  orchestrator_agents:
    - name: design-spec-orchestrator
      type: orchestrator
      description: "Main orchestrator for design spec implementation"
      enabled: true

# Extensibility configuration
extensibility:
  allow_dynamic_agents: true
  agent_template_path: ".claude/agents/templates/domain-expert-template.md"
  auto_discover_agents: true
  agent_directories:
    - ".claude/agents/practices"
    - ".claude/agents/custom"
```

#### 0.3 Create Design Spec Template
Create `design-specs/template.md`:
```markdown
# Design Spec: [Feature Name]

## Overview
Brief description of the feature/change.

## Goals
- Goal 1
- Goal 2
- Goal 3

## Non-Goals
- What is explicitly out of scope

## Proposed Changes

### API Changes
Describe CRD/API modifications if any.

### Controller Changes
Describe controller logic changes if any.

### Other Components
List other affected areas.

## Testing Strategy

### Unit Tests
What unit tests are needed?

### E2E Tests
What e2e tests are needed?

## Implementation Plan
High-level steps (will be enhanced by agent analysis).

## Open Questions
- Question 1?
- Question 2?

## Domain Experts to Consult
<!-- Auto-detected by orchestrator, or manually specify -->
- [ ] Go practices
- [ ] Kubernetes practices
- [ ] Controller practices
- [ ] CRD practices
- [ ] Unit testing practices
- [ ] E2E testing practices
- [ ] General coding practices
```

#### 0.4 Document Architecture
Create `docs/agentic-workflow/architecture.md` with:
- System overview diagram
- Agent interaction flows
- Data flow between orchestrator and domain expert agents
- Decision criteria for agent activation
- Extensibility points

### Deliverables
- [ ] Complete directory structure created
- [ ] `config/agents.yaml` registry created
- [ ] Design spec template created
- [ ] Architecture documentation written

### Success Criteria
- Clear, organized structure that supports extensibility
- Configuration-driven agent management
- Standardized design spec format

---

## Phase 1: Agent Infrastructure Setup

**Objective:** Create foundational components and templates that support agent creation and management.

**Duration:** 3-4 days

### Tasks

#### 1.1 Create Agent Template
Create `.claude/agents/templates/domain-expert-template.md`:
```markdown
---
name: {{AGENT_NAME}}
description: {{DOMAIN}} expert agent for design spec analysis
model: opus
type: domain_expert
tools:
  - Read
  - Grep
  - Glob
  - WebSearch
triggers:
  - {{TRIGGER_1}}
  - {{TRIGGER_2}}
---

# {{DOMAIN}} Expert Agent

You are a world-class expert in {{DOMAIN}} with deep knowledge of best practices, patterns, anti-patterns, and industry standards.

## Your Role

When analyzing a design spec, you will:
1. Read and understand the design spec requirements
2. Identify {{DOMAIN}}-specific concerns and opportunities
3. Analyze existing codebase for relevant patterns
4. Research latest best practices if needed
5. Provide concrete, actionable recommendations
6. Flag potential issues, anti-patterns, or risks

## Areas of Expertise

{{LIST_EXPERTISE_AREAS}}

## Analysis Framework

### 1. Requirements Analysis
- What does the design spec require in your domain?
- What are the explicit requirements?
- What are the implicit requirements?

### 2. Current State Assessment
- What relevant patterns exist in the codebase?
- What utilities/helpers are available?
- What conventions are established?

### 3. Best Practices Review
- What best practices apply to these requirements?
- What patterns should be followed?
- What anti-patterns should be avoided?

### 4. Risk Assessment
- What could go wrong in your domain?
- What edge cases need consideration?
- What performance/security/reliability concerns exist?

### 5. Recommendations
Provide specific recommendations with:
- **What**: The recommendation
- **Why**: Justification and benefits
- **How**: Implementation guidance with examples
- **Priority**: High/Medium/Low
- **Effort**: Small/Medium/Large

## Output Format

Structure your analysis as:

### Summary
Brief overview of your analysis (2-3 sentences).

### Key Findings
- Finding 1
- Finding 2
- Finding 3

### Recommendations

#### High Priority
1. **[Recommendation Title]**
   - **What**: Description
   - **Why**: Rationale
   - **How**: Implementation approach
   - **Example**: Code snippet or reference

#### Medium Priority
[Same format]

#### Low Priority
[Same format]

### Risks & Concerns
- Risk 1: Description and mitigation
- Risk 2: Description and mitigation

### Existing Patterns to Leverage
- Pattern 1: Location and usage
- Pattern 2: Location and usage

### Questions for Clarification
- Question 1?
- Question 2?

## Guidelines

- Be specific and actionable
- Provide code examples when possible
- Reference existing codebase patterns
- Consider the broader system context
- Balance idealism with pragmatism
- Highlight trade-offs clearly
- Focus on value delivery

## Research Approach

If needed, you can:
- Search the web for latest {{DOMAIN}} best practices
- Read relevant files in the codebase
- Grep for similar implementations
- Look up official documentation

Always cite sources when recommending external patterns.
```

#### 1.2 Create Agent Registry Helper
Create `.claude/agents/agent-registry-helper.md` (skill or agent):
```markdown
---
name: agent-registry-helper
description: Helper for managing agent registry and discovering agents
---

# Agent Registry Helper

This helper manages the agent registry and assists with agent discovery.

## Capabilities

1. **List Available Agents**: Show all registered domain expert agents
2. **Check Agent Status**: Verify which agents are enabled
3. **Suggest Agents for Spec**: Analyze design spec and suggest relevant agents
4. **Validate Agent Config**: Check agents.yaml for correctness
5. **Generate Agent Report**: Create summary of agent capabilities

## Usage

When called, determine what registry operation is needed:
- List agents: Read and display agents.yaml registry
- Suggest agents: Analyze design spec triggers and match to agents
- Validate: Check yaml syntax and required fields
- Report: Generate markdown summary of all agents

Always reference the source of truth: `/UbuntuSync/config/agents.yaml`
```

#### 1.3 Integration with skill-creator
Plan for using skill-creator to generate new domain expert agents:
```markdown
# Using skill-creator for New Domain Expert Agents

## Approach
Use the skill-creator skill to generate new domain expert agent definitions based on the template.

## Workflow
1. Identify need for new domain expertise area (e.g., "security-practices")
2. Define the domain, expertise areas, and triggers
3. Use skill-creator to generate agent definition from template
4. Review and customize the generated agent
5. Add to agent registry in agents.yaml
6. Test with sample design spec

## Command Pattern
```bash
# Future usage (after skill-creator integration)
/skill-creator create-domain-expert-agent \
  --name security-expert \
  --domain "Security and compliance" \
  --triggers "security,auth,rbac" \
  --template domain-expert-template.md
```

## Benefits
- Consistency across all domain expert agents
- Faster onboarding of new domain expertise areas
- Built-in best practices from template
- Easier maintenance
```

### Deliverables
- [ ] Agent template created and documented
- [ ] Agent registry helper created
- [ ] skill-creator integration plan documented

### Success Criteria
- Templates are clear and comprehensive
- New agents can be created consistently
- Registry management is straightforward

---

## Phase 2: Create Initial Domain Expert Agents

**Objective:** Build the 7 core domain expert agents with rich domain knowledge.

**Duration:** 1-2 weeks (can be parallelized)

### Tasks

#### 2.1 Go Expert Agent
Create `.claude/agents/domain-experts/go-expert.md`:

**Domain expertise:**
- Idiomatic Go patterns
- Error handling (errors.Is, errors.As, wrapping, custom errors)
- Concurrency (goroutines, channels, sync primitives, context)
- Project structure (internal/, pkg/, cmd/)
- Dependency management (go.mod, go.sum)
- Testing (table-driven tests, subtests, testify, gomock)
- Performance (profiling, benchmarking, memory management)
- Common libraries (standard library focus)

**Triggers:**
- Design spec mentions Go code changes
- Files with `.go` extension
- Mentions of goroutines, channels, interfaces

**Key practices to encode:**
- Prefer standard library
- Accept interfaces, return structs
- Error wrapping and context
- Keep exported API surface small
- Use context for cancellation

#### 2.2 Kubernetes Expert Agent
Create `.claude/agents/domain-experts/k8s-expert.md`:

**Domain expertise:**
- Kubernetes resource patterns
- Deployment strategies (rolling, blue-green, canary)
- Service discovery and networking
- ConfigMaps and Secrets management
- RBAC and security
- Resource limits and requests
- Health checks (liveness, readiness, startup probes)
- Labels and selectors
- Namespace organization

**Triggers:**
- Mentions of deployments, services, pods
- Kubernetes manifests
- Helm charts
- Kustomize

**Key practices to encode:**
- Always set resource limits
- Use meaningful labels
- Implement proper health checks
- Follow least-privilege RBAC
- Use namespaces for isolation

#### 2.3 Controller Expert Agent
Create `.claude/agents/domain-experts/controller-expert.md`:

**Domain expertise:**
- controller-runtime patterns
- Reconciliation loop design
- Event filtering and predicates
- Error handling and retry logic
- Leader election
- Finalizers and garbage collection
- Status conditions
- Watches and indexers
- Owner references
- Performance and scalability

**Triggers:**
- Controller or reconcile logic
- controller-runtime usage
- Operator patterns

**Key practices to encode:**
- Idempotent reconciliation
- Proper error handling (requeue strategies)
- Status updates separate from spec
- Use finalizers for cleanup
- Watch only what's needed
- Implement proper backoff

#### 2.4 CRD Expert Agent
Create `.claude/agents/domain-experts/crd-expert.md`:

**Domain expertise:**
- API design (versioning, compatibility)
- Kubebuilder markers and validation
- OpenAPI schema design
- Default values and optional fields
- Status subresource
- Printer columns
- Webhook validation
- Conversion webhooks
- API conventions (Kubernetes API)

**Triggers:**
- CRD or API changes
- kubebuilder markers
- API versioning

**Key practices to encode:**
- Follow Kubernetes API conventions
- Use proper validation markers
- Design for backward compatibility
- Status is read-only for users
- Use subresources appropriately
- Meaningful printer columns

#### 2.5 Unit Test Expert Agent
Create `.claude/agents/domain-experts/unit-test-expert.md`:

**Domain expertise:**
- Table-driven tests in Go
- Test organization and structure
- Mocking strategies (interfaces, gomock)
- Test helpers and utilities
- Coverage targets and meaningful tests
- Test naming conventions
- Testify usage
- Parallel test execution
- Test fixtures and setup/teardown

**Triggers:**
- Test requirements in spec
- `_test.go` files
- Testing strategy discussion

**Key practices to encode:**
- Table-driven tests for multiple cases
- Test behavior, not implementation
- Clear test names that describe scenario
- Mock external dependencies, not internal logic
- Aim for fast, isolated tests
- Use subtests for organization

#### 2.6 E2E Test Expert Agent
Create `.claude/agents/domain-experts/e2e-test-expert.md`:

**Domain expertise:**
- Ginkgo BDD testing
- Gomega matchers
- Test environment setup (kind, test clusters)
- Test isolation and cleanup
- Test reliability and flake reduction
- Parallel execution strategies
- Test data management
- Long-running test patterns
- Integration with CI/CD

**Triggers:**
- E2E or integration tests
- Ginkgo/Gomega usage
- End-to-end testing strategy

**Key practices to encode:**
- Proper test isolation (cleanup)
- Use Eventually for async operations
- Meaningful test descriptions
- Reduce flakes with proper waits
- Parallel execution where possible
- Clear test data setup

#### 2.7 Coding Expert Agent
Create `.claude/agents/domain-experts/coding-expert.md`:

**Domain expertise:**
- SOLID principles
- DRY, KISS, YAGNI
- Code organization and modularity
- Naming conventions
- Comment and documentation practices
- Code review guidelines
- Refactoring patterns
- Technical debt management
- Performance considerations
- Security best practices

**Triggers:**
- General code quality concerns
- Refactoring discussions
- Code organization

**Key practices to encode:**
- Clear, descriptive names
- Functions do one thing
- Minimize cyclomatic complexity
- Document why, not what
- Consistent style
- Security-first mindset

### For Each Agent

**Implementation steps:**
1. Copy and customize template
2. Research domain-specific best practices
3. Document expertise areas comprehensively in agent prompt
4. Define clear triggers
5. Specify tools needed (Read, Grep, WebSearch, etc.)
6. Add to agent registry in `config/agents.yaml`
7. Create test design spec to validate

### Deliverables
- [ ] 7 domain expert agent definitions created
- [ ] All agents registered in agents.yaml
- [ ] Initial validation completed

### Success Criteria
- Each agent has comprehensive domain knowledge
- Agent prompts are clear and actionable
- Triggers are well-defined
- Outputs follow consistent format

---

## Phase 3: Build Orchestrator

**Objective:** Create the main orchestrator that coordinates domain expert agents and synthesizes recommendations.

**Duration:** 1 week

### Tasks

#### 3.1 Design Orchestrator Logic

**Decision flow:**
1. Read design spec from specified path
2. Parse spec to identify relevant domain expertise areas
3. Check agent registry for enabled agents
4. Match spec requirements to agent triggers
5. Determine which agents to invoke (all or subset)
6. Launch agents in parallel
7. Collect all agent responses
8. Synthesize recommendations
9. Resolve conflicts between agents
10. Generate unified implementation plan

#### 3.2 Create Orchestrator Skill
Create `.claude/agents/orchestrator/design-spec-orchestrator.md`:

```markdown
---
name: design-spec-orchestrator
description: Orchestrates design spec analysis using domain expert agents
triggers:
  - "analyze design spec"
  - "/analyze-spec"
---

# Design Spec Orchestrator

You are the main orchestrator for design spec analysis and implementation planning.

## Your Responsibilities

1. **Read Design Spec**: Load and parse the design spec
2. **Agent Selection**: Determine which domain expert agents are relevant
3. **Parallel Execution**: Launch all relevant agents simultaneously
4. **Result Synthesis**: Combine agent recommendations into coherent plan
5. **Conflict Resolution**: Reconcile contradictory recommendations
6. **Plan Generation**: Create actionable implementation plan

## Workflow

### Step 1: Load Design Spec
```bash
# Read design spec file
Read file_path=/UbuntuSync/design-specs/{{spec-name}}.md
```

Parse and understand:
- Feature overview and goals
- Proposed changes
- Affected areas (API, controller, tests, etc.)
- Complexity and scope

### Step 2: Load Agent Registry
```bash
Read file_path=/UbuntuSync/config/agents.yaml
```

Identify:
- Which agents are enabled
- Which agents match the spec requirements
- Agent priorities

### Step 3: Determine Agent Activation

Match spec content to agent triggers:
- Go code changes → go-expert
- Kubernetes resources → k8s-expert
- Controller logic → controller-expert
- API/CRD changes → crd-expert
- Test requirements → unit-test-expert, e2e-test-expert
- General code → coding-expert

**Default**: If uncertain, invoke all enabled agents (comprehensive analysis)

### Step 4: Launch Agents in Parallel

Use Agent tool to spawn all relevant agents simultaneously:

```claude
Agent(
  description="Go practices analysis",
  subagent_type="go-expert",
  prompt="Analyze this design spec from Go best practices perspective:

  {{DESIGN_SPEC_CONTENT}}

  Provide recommendations following your standard analysis framework.",
  run_in_background=false
)

Agent(
  description="K8s practices analysis",
  subagent_type="k8s-expert",
  prompt="Analyze this design spec from Kubernetes best practices perspective:

  {{DESIGN_SPEC_CONTENT}}

  Provide recommendations following your standard analysis framework.",
  run_in_background=false
)

# ... repeat for all relevant agents
```

**Important**: Launch all agents in single message for true parallelization.

### Step 5: Collect Results

Wait for all agents to complete. Each agent returns:
- Summary
- Key findings
- Recommendations (High/Medium/Low priority)
- Risks & concerns
- Existing patterns to leverage
- Clarification questions

### Step 6: Synthesize Recommendations

**Synthesis process:**

1. **Categorize by Priority**
   - Collect all High priority recommendations
   - Collect all Medium priority recommendations
   - Collect all Low priority recommendations

2. **Group by Implementation Area**
   - API/CRD changes
   - Controller logic
   - Testing
   - Infrastructure/Deployment
   - Documentation

3. **Identify Overlaps**
   - Where multiple agents recommend same thing (validate/strengthen)
   - Where agents have complementary recommendations (combine)

4. **Resolve Conflicts**
   - Where agents disagree, analyze trade-offs
   - Consider context and priorities
   - Make informed decision or flag for human review

5. **Merge Risks**
   - Aggregate all risks and concerns
   - Identify cross-cutting risks
   - Prioritize by severity and likelihood

### Step 7: Generate Implementation Plan

Create structured implementation plan:

```markdown
# Implementation Plan: {{Feature Name}}

**Generated:** {{Date}}
**Design Spec:** {{spec-path}}
**Agents Consulted:** {{agent-list}}

## Executive Summary
2-3 paragraph overview of the implementation approach, key decisions, and estimated complexity.

## Key Recommendations

### Critical (Must Do)
1. **[Recommendation]** (from: {{agent-name}})
   - Description
   - Rationale
   - Implementation approach

### Important (Should Do)
[Same format]

### Nice to Have (Could Do)
[Same format]

## Implementation Steps

### Phase 1: {{Phase Name}}
**Estimated Effort:** {{Small/Medium/Large}}

Files to modify/create:
- `path/to/file1.go`: Description of changes
- `path/to/file2.go`: Description of changes

Steps:
1. Step 1
2. Step 2
3. Step 3

Domain expertise guidelines:
- From go-expert: {{guideline}}
- From coding-expert: {{guideline}}

### Phase 2: {{Phase Name}}
[Same format]

## Testing Strategy

### Unit Tests
- What to test (from unit-test-expert)
- Test files to create/modify
- Key test cases

### E2E Tests
- What to test (from e2e-test-expert)
- Test scenarios
- Test data requirements

## Risks & Mitigation

### High Risk
1. **Risk:** Description
   - **Source:** {{agent-name}}
   - **Impact:** What could go wrong
   - **Mitigation:** How to address
   - **Owner:** Who should handle this

### Medium Risk
[Same format]

## Existing Patterns to Leverage

### From Codebase
- Pattern 1: `path/to/example.go` - How to use
- Pattern 2: `path/to/example.go` - How to use

### Utilities/Helpers Available
- Helper 1: Purpose and location
- Helper 2: Purpose and location

## Open Questions & Decisions Needed

### For Product/Design
1. Question 1 (from {{agent-name}})
2. Question 2 (from {{agent-name}})

### For Technical Review
1. Question 1 (from {{agent-name}})
2. Question 2 (from {{agent-name}})

## Agent Analysis Summary

### Go Expert
- Key findings
- Top recommendations
- Main concerns

### Kubernetes Expert
[Same format for each agent]

## Appendix: Full Agent Reports

### Go Expert Full Report
{{Full agent output}}

### Kubernetes Expert Full Report
{{Full agent output}}

[Continue for all agents]
```

### Step 8: Present to User

Show the synthesized implementation plan to the user with:
- Clear executive summary
- Prioritized recommendations
- Actionable steps
- Risk assessment
- Questions for clarification

Offer to:
- Dive deeper into specific recommendations
- Start implementation of specific phases
- Consult additional agents if needed
- Update the design spec based on findings

## Usage Examples

### Example 1: Full Analysis
```
User: "Analyze design-specs/add-webhook-support.md"

Orchestrator:
1. Reads design spec
2. Identifies: CRD changes, controller logic, new webhook component
3. Launches: go-expert, k8s-expert, controller-expert, crd-expert,
   unit-test-expert, e2e-test-expert, coding-expert
4. Synthesizes all 7 agent responses
5. Generates comprehensive implementation plan
```

### Example 2: Targeted Analysis
```
User: "Analyze design-specs/add-webhook-support.md focusing on testing"

Orchestrator:
1. Reads design spec
2. Identifies testing focus
3. Launches: unit-test-expert, e2e-test-expert, coding-expert (for test quality)
4. Synthesizes testing-focused recommendations
5. Generates testing-specific implementation plan
```

### Example 3: Iterative Analysis
```
User: "Quick analysis of design-specs/simple-fix.md"

Orchestrator:
1. Reads design spec (small bug fix)
2. Identifies: Simple Go change
3. Launches: go-expert, coding-expert (minimal scope)
4. Provides focused recommendations
5. Quick implementation plan
```

## Configuration

Orchestrator behavior can be customized:
- **Full mode**: Launch all enabled agents (default)
- **Smart mode**: Launch only triggered agents
- **Focused mode**: User specifies which agents
- **Quick mode**: Skip low-priority agents

## Error Handling

If agent fails:
- Log the failure
- Continue with other agents
- Note missing analysis in final plan
- Suggest manual review of that domain expertise area

## Extension Points

When new domain expert agents are added:
- Automatically discovered from agent registry
- Included in parallel launches if triggered
- Results integrated into synthesis
- No orchestrator code changes needed
```

#### 3.3 Create Convenience Slash Command
Create a Claude Code custom slash command that wraps the orchestrator:

Create `.claude/commands/analyze-spec.md`:

This file becomes the `/analyze-spec` command. It uses `$ARGUMENTS` to receive the spec path and options from the user, then follows the orchestrator instructions.

**Usage:**
```bash
/analyze-spec design-specs/add-new-feature.md
/analyze-spec design-specs/bug-fix.md --fast
/analyze-spec design-specs/add-feature.md --focus=unit-test,e2e-test
/analyze-spec design-specs/operator-enhancement.md --focus=k8s,controller
/analyze-spec design-specs/api-change.md --smart
```

**Options:**
- `--full`: Comprehensive analysis with all agents (default)
- `--fast`: Quick analysis with high-priority agents only
- `--focus=<areas>`: Focus on specific domain areas (comma-separated)
- `--smart`: Auto-detect relevant agents from spec content

### Deliverables
- [ ] Orchestrator agent created with full logic
- [ ] Synthesis algorithm implemented
- [ ] Convenience skill created
- [ ] Error handling implemented

### Success Criteria
- Orchestrator successfully coordinates multiple agents
- Results are synthesized coherently
- Implementation plan is actionable and comprehensive
- Conflicts are resolved appropriately

---

## Phase 4: Extensibility Framework

**Objective:** Build mechanisms to easily add new domain expert agents without modifying core system.

**Duration:** 3-5 days

### Tasks

#### 4.1 Create "Add New Agent" Guide
Create `docs/agentic-workflow/adding-new-agents.md`:
```markdown
# Adding New Domain Expert Agents

This guide explains how to add new domain expert agents to the system.

## Quick Start

1. Identify the domain expertise area (e.g., "security-practices")
2. Use the agent template
3. Customize for your domain with comprehensive expertise
4. Register in agent registry
5. Test with sample design spec

## Detailed Steps

### Step 1: Create Agent Definition

```bash
cp .claude/agents/templates/domain-expert-template.md \
   .claude/agents/domain-experts/security-expert.md
```

Customize:
- Replace `{{AGENT_NAME}}` with `security-expert`
- Replace `{{DOMAIN}}` with "Security and Compliance"
- Define expertise areas
- Set triggers
- Configure tools
- Specify model (opus for complex, sonnet for faster)

### Step 2: Define Domain Expertise in Agent Prompt

Add specific areas of expertise to the agent definition:
- Authentication and authorization
- Secrets management
- RBAC design
- Security scanning
- Compliance requirements
- CVE remediation
- Best practices and anti-patterns
- Common pitfalls
- Tools and libraries
- Testing approach
- etc.

### Step 3: Register Agent

Edit `config/agents.yaml`:
```yaml
- name: security-expert
  type: domain_expert
  domain: "Security and compliance practices"
  enabled: true
  priority: high
  tools:
    - Read
    - Grep
    - WebSearch
    - Bash
  triggers:
    - "security"
    - "auth"
    - "secrets"
    - "rbac"
    - "compliance"
```

### Step 4: Test

Create test design spec: `design-specs/test-security-expert.md`

Run: `/analyze-spec design-specs/test-security-expert.md --focus=security`

Verify:
- Agent is triggered
- Analysis is comprehensive
- Recommendations are actionable
- Output follows format

### Step 5: Document

Update `docs/agentic-workflow/agent-registry.md` with:
- Agent name and purpose
- When to use it
- Example triggers
- Key expertise areas

## Using skill-creator (Future)

Once integrated:
```bash
/skill-creator create-domain-expert-agent \
  --name security-expert \
  --domain "Security and compliance" \
  --expertise "auth,secrets,rbac,scanning" \
  --triggers "security,auth,compliance"
```

This will:
- Generate agent definition from template with comprehensive expertise
- Register in agents.yaml
- Prompt for domain-specific customization

## Best Practices

- **Focus**: One clear domain expertise area per agent
- **Tools**: Only request tools the agent will actually use
- **Triggers**: Specific enough to avoid false positives
- **Expertise**: Keep agent prompts comprehensive and updated
- **Testing**: Always test with real design specs
- **Maintenance**: Review and update agent knowledge quarterly

## Common Agent Types

### Analysis Agents
- Focus on reviewing design specs
- Need: Read, Grep, Glob, WebSearch
- Examples: go-expert, k8s-expert

### Implementation Agents
- Can modify code
- Need: Read, Write, Edit, Bash
- Examples: refactoring-agent, migration-agent

### Research Agents
- Deep research and learning
- Need: WebSearch, Read, Grep
- Examples: tech-research, benchmark-agent

## Extension Points

Agents can be extended with:
- Custom memory paths (for specialized knowledge)
- Additional tools (new MCP tools)
- Sub-agents (agents that coordinate other agents)
- Background execution (long-running analysis)
- Persistent state (across multiple specs)
```

#### 4.2 Agent Validation & Discovery

**Note:** Validation and discovery capabilities are consolidated into the existing `agent-registry-helper` tool (`.claude/agents/tools/agent-registry-helper.md`). Separate `agent-validator` and `agent-discovery` tools were considered but deemed redundant since the registry helper already provides:

- **Validation**: Check agent config for correctness, required fields, valid tools, trigger overlap
- **Discovery**: Scan filesystem for agent files, cross-reference against registry, detect unregistered or missing agents
- **Listing**: Show all agents with status, priority, and configuration
- **Suggestions**: Analyze design specs and recommend which agents to invoke

#### 4.4 skill-creator Integration Plan

Document how to use skill-creator to generate new agents:

Create `docs/agentic-workflow/skill-creator-integration.md`:
```markdown
# skill-creator Integration

## Current Status
skill-creator is available for creating and managing skills in Claude Code.

## Integration Approach

### Phase 1: Manual Template Usage
Use existing templates to create new domain expert agents manually.

### Phase 2: skill-creator for Agent Generation (Future)
Enhance skill-creator to understand domain expert agent pattern:

1. **Create agent-specific skill templates**
   - domain-expert-agent template in skill-creator format
   - Include customization prompts
   - Auto-populate from parameters

2. **Add domain-expert-agent creation command**
   ```bash
   /skill-creator create-domain-expert-agent
   ```

   Prompts for:
   - Agent name
   - Domain
   - Expertise areas
   - Triggers
   - Tools needed

3. **Auto-registration**
   - Automatically update agents.yaml
   - Run validation
   - Suggest test design spec

### Phase 3: Agent Optimization with skill-creator
Use skill-creator to:
- Run evals on agent performance
- Benchmark agent response quality
- Optimize agent prompts
- Measure impact of changes

## Workflow Example

```bash
# Create new agent
/skill-creator create-domain-expert-agent \
  --name observability-expert \
  --domain "Observability and monitoring"

# skill-creator prompts:
# - What expertise areas? (metrics, logging, tracing, alerting)
# - What triggers? (observability, prometheus, grafana, metrics)
# - What tools needed? (Read, Grep, WebSearch, Bash)
# - Priority level? (high, medium, low)

# skill-creator generates:
# 1. .claude/agents/domain-experts/observability-expert.md (with comprehensive expertise)
# 2. Updates config/agents.yaml
# 3. Runs validation
# 4. Creates test design spec

# Optimize agent
/skill-creator optimize-agent observability-expert \
  --run-evals \
  --test-specs design-specs/examples/

# skill-creator:
# 1. Runs agent against test specs
# 2. Measures response quality
# 3. Suggests prompt improvements
# 4. Shows variance analysis
```

## Benefits
- Faster agent creation
- Consistent agent structure
- Built-in validation
- Quality optimization
- Performance measurement
```

### Deliverables
- [ ] Adding new agents guide created
- [ ] Validation & discovery consolidated in agent-registry-helper (no separate tools needed)
- [ ] skill-creator integration plan documented

### Success Criteria
- Clear process for adding new agents
- Automated validation of agents
- Easy to extend system without breaking existing functionality

---

## Phase 5: Testing & Validation

**Objective:** Thoroughly test the multi-agent system end-to-end.

**Duration:** 1 week

### Tasks

#### 5.1 Create Test Design Specs

Create variety of test specs in `design-specs/examples/`:

1. **Simple bug fix** (`simple-bugfix.md`)
   - Minimal changes
   - Single file affected
   - Tests: go-expert, coding-expert

2. **New feature with API** (`new-feature-with-api.md`)
   - CRD changes
   - Controller logic
   - Tests required
   - Tests: All agents

3. **Testing infrastructure** (`improve-e2e-tests.md`)
   - Focus on testing practices
   - Tests: unit-test-expert, e2e-test-expert, coding-expert

4. **Controller optimization** (`controller-optimization.md`)
   - Performance improvements
   - Controller patterns
   - Tests: controller-expert, go-expert, coding-expert

5. **Security enhancement** (`add-rbac-support.md`)
   - Security-focused
   - RBAC changes
   - Tests: k8s-expert, crd-expert, coding-expert

#### 5.2 Test Orchestration

For each test spec:
1. Run: `/analyze-spec design-specs/examples/{{spec}}.md`
2. Verify:
   - Correct agents triggered
   - All agents complete successfully
   - Results are synthesized coherently
   - Implementation plan is actionable
   - No duplicate recommendations
   - Conflicts are resolved
   - Risks are identified

#### 5.3 Test Agent Quality

For each domain expert agent:
1. Verify expertise is comprehensive
2. Check recommendations are specific and actionable
3. Ensure examples are relevant
4. Validate risk assessment is thorough
5. Confirm questions for clarification are useful

#### 5.4 Test Extensibility

1. Add a new agent (e.g., `performance-expert`)
2. Register in agents.yaml
3. Run orchestrator
4. Verify new agent is automatically included
5. Confirm no changes to orchestrator needed

#### 5.5 Performance Testing

Measure:
- Time for parallel agent execution
- Token usage per analysis
- End-to-end latency
- Compare to sequential execution

#### 5.6 Test Suite Documentation

Deferred until a real codebase is available. The test design specs in `design-specs/examples/` serve as reference examples for writing design specs. Formal validation procedures should be written against a real project where agents can search actual code.

### Deliverables
- [ ] 5+ test design specs created (as reference examples)
- [ ] Validation performed against real codebase (deferred)
- [ ] Test suite documented

### Success Criteria
- All test design specs produce quality implementation plans
- Agents work correctly in parallel
- System is extensible without code changes
- Performance is acceptable (agents complete in < 2 minutes)

---

## Phase 6: Documentation & Productionization

**Objective:** Complete documentation and prepare for production use.

**Duration:** 3-5 days

### Tasks

#### 6.1 User Documentation

Create `docs/agentic-workflow/user-guide.md`:
```markdown
# Agentic Workflow User Guide

## Quick Start

1. Write a design spec using the template
2. Run `/analyze-spec design-specs/your-spec.md`
3. Review the implementation plan
4. Start coding based on recommendations

## Writing Design Specs

[Detailed guide on writing good design specs]

## Understanding the Analysis

[How to interpret agent recommendations]

## Working with Implementation Plans

[How to use the generated plans effectively]

## Common Workflows

### New Feature Development
1. Create design spec
2. Run full analysis
3. Review high-priority recommendations
4. Implement in phases
5. Validate against testing recommendations

### Bug Fixes
1. Create minimal design spec
2. Run quick analysis (--fast)
3. Focus on relevant domain expertise area
4. Implement fix
5. Verify tests

### Refactoring
1. Document refactoring goals
2. Run focused analysis
3. Review existing patterns
4. Follow recommendations
5. Ensure tests pass

## Tips for Success

- Be specific in design specs
- Review all agent recommendations
- Ask clarifying questions
- Iterate on the plan
- Keep agent prompts updated with latest practices

## Troubleshooting

[Common issues and solutions]
```

#### 6.2 Developer Documentation

Create `docs/agentic-workflow/developer-guide.md`:
```markdown
# Agentic Workflow Developer Guide

## Architecture

[Detailed architecture diagrams and explanations]

## Agent System

### How Agents Work
[Agent lifecycle, tools, execution model]

### Creating New Agents
[Step-by-step guide with examples]

### Agent Best Practices
[Patterns for effective agents]

## Orchestrator

### Orchestration Logic
[How the orchestrator works]

### Synthesis Algorithm
[How recommendations are combined]

### Conflict Resolution
[How conflicts are handled]

## Extensibility

### Adding New Domain Expertise Areas
[Detailed guide]

### Custom Agents
[Advanced agent types]

### Integration Points
[Where to hook in custom logic]

## Maintenance

### Updating Agent Expertise
[How and when to update agent prompts]

### Agent Performance Optimization
[Using skill-creator for optimization]

### Monitoring and Metrics
[What to track]
```

#### 6.3 Agent Registry Documentation

Create `docs/agentic-workflow/agent-registry.md`:
```markdown
# Agent Registry

Complete catalog of all domain expert agents.

## Domain Expert Agents

### go-expert
- **Domain:** Go language and idioms
- **Purpose:** Review Go code for best practices
- **Triggers:** go, golang, *.go files
- **Tools:** Read, Grep, Glob, WebSearch
- **Priority:** High
- **When to use:** Any design spec involving Go code changes

[Detailed description, expertise areas, example recommendations]

### k8s-expert
[Same format]

[Continue for all agents]

## Orchestrator Agents

### design-spec-orchestrator
[Description of orchestrator]

## Future Agents

Planned additions:
- security-expert
- performance-expert
- observability-expert
- migration-expert
```

#### 6.4 Create Runbook

Create `docs/agentic-workflow/runbook.md`:
```markdown
# Agentic Workflow Runbook

## Common Tasks

### Analyzing a Design Spec
```bash
/analyze-spec design-specs/your-spec.md
```

### Adding a New Domain Expert Agent
1. Copy template
2. Customize
3. Register
4. Test
5. Document

### Updating an Existing Agent
1. Edit agent file (update expertise in prompt)
2. Test with example specs
3. Document changes

### Troubleshooting Failed Agent
1. Check agent definition
2. Validate YAML
3. Test agent in isolation
4. Review error logs
5. Check tool permissions

### Optimizing Agent Performance
1. Use skill-creator to run evals
2. Analyze response quality
3. Optimize prompt
4. Retest
5. Measure improvement

## Maintenance Schedule

### Weekly
- Review agent usage metrics
- Check for new domain expertise areas

### Monthly
- Update agent prompts with latest best practices
- Review and optimize slow agents
- Validate all agents still work

### Quarterly
- Major review of all agents
- Update to latest best practices
- Add new agents as needed
- Archive obsolete agents
```

#### 6.5 Examples and Templates

Create `docs/agentic-workflow/examples/`:
- Example design specs (various complexities)
- Example agent analyses
- Example implementation plans
- Example workflow scenarios

### Deliverables
- [ ] User guide with common tasks section (consolidates runbook) — `user-guide.md`
- [ ] Developer guide referencing existing architecture/agent docs — `developer-guide.md`
- [ ] Agent registry catalog — `agent-registry.md`
- [ ] Examples already created in Phase 5 — `design-specs/examples/`

### Success Criteria
- Documentation is comprehensive and clear
- New users can get started easily
- Developers can extend the system
- Common tasks have clear procedures

---

## Phase 7: Integration with Development Workflow

**Objective:** Integrate the agentic workflow into daily development practices.

**Duration:** Ongoing

### Tasks

#### 7.1 Create Workflow Integration

Options for integration:
1. **Pre-commit hook**: Validate design specs before commit
2. **PR automation**: Auto-analyze design specs in PRs
3. **CI/CD integration**: Run analysis as part of build
4. **IDE integration**: Trigger analysis from IDE

#### 7.2 Team Onboarding

1. Training session on agentic workflow
2. Hands-on practice with sample specs
3. Creating first design specs
4. Running first analyses
5. Implementing based on recommendations

#### 7.3 Feedback Loop

1. Collect feedback on agent recommendations
2. Track which recommendations are followed
3. Measure impact on code quality
4. Iterate on agent prompts
5. Add new domain expertise areas based on needs

#### 7.4 Metrics and Monitoring

Track:
- Number of design specs analyzed
- Average analysis time
- Agent utilization (which agents used most)
- Recommendation acceptance rate
- Code quality improvements
- Developer satisfaction

#### 7.5 Superpowers Integration ✅

Integrated [superpowers](https://github.com/obra/superpowers) as the execution layer after analysis.

**Completed:**
1. ✅ Shared knowledge layer (`CLAUDE.md`) — documents two-system workflow, handoff pipeline, project conventions
2. ✅ Handoff command (`.claude/commands/prepare-implementation.md`) — `/prepare-implementation` creates a prioritized implementation brief that references the analysis report (for `writing-plans` to consume)
3. ✅ Output directories — `analysis-reports/` (from `/analyze-spec`), `docs/superpowers/briefs/` (from `/prepare-implementation`), `docs/superpowers/plans/` (from `writing-plans`)
4. ✅ User guide, developer guide, orchestrator updated with new handoff flow
5. ✅ Clear separation of concerns: our system handles analysis, superpowers handles plan generation and execution

**Design decision:** `/prepare-implementation` generates an implementation brief, NOT an execution plan. Superpowers' `writing-plans` skill is purpose-built for task decomposition with strict code completeness, TDD ordering, and implementer-aware granularity. Our value is the multi-agent analysis depth, not reimplementing plan generation.

**Pipeline:** `Design Spec → /analyze-spec → Analysis Report → /prepare-implementation → Implementation Brief → writing-plans → Execution Plan → Execute`

#### 7.6 Self-Optimization Loop

The end-to-end workflow should continuously improve its own files based on real outcomes.

**Goal:** After each full cycle (design spec → analysis → implementation), lessons learned are captured and used to optimize domain expert prompts, agent registry config, templates, and orchestrator logic.

**Implementation:**

1. **Capture phase** - After implementation completes, review step records:
   - Which recommendations were followed vs skipped (and why)
   - Problems that arose that no agent predicted
   - Patterns that emerged and should be codified
   - Which agents provided the most/least value

2. **Analysis phase** - A self-optimization agent analyzes the review and proposes changes:
   - Update domain expert prompts (add patterns that worked, remove advice that didn't)
   - Adjust `agents.yaml` (triggers, priorities, model selections)
   - Improve design spec template (add fields agents actually needed)
   - Refine orchestrator synthesis format

3. **Apply phase** - Proposed changes submitted for human review:
   - All changes require human approval before commit
   - Changes tracked via git for easy rollback
   - Metrics tracked to verify improvements over time

**Examples of self-learned improvements:**
- "go-expert never catches X pattern → add to go-expert prompt"
- "crd-expert trigger list missing 'validation webhook' → add trigger"
- "implementation plans lack migration steps → update orchestrator synthesis format"
- "e2e-test-expert recommendations too generic → add project-specific conventions"

### Deliverables
- [ ] Integration with development workflow
- [ ] Team trained
- [ ] Feedback mechanisms in place
- [ ] Metrics collection started
- [ ] Superpowers handoff workflow documented and tested
- [ ] Self-optimization loop capturing lessons learned
- [ ] First round of self-improvements proposed and reviewed

### Success Criteria
- Team regularly uses agentic workflow
- Design specs are standard practice
- Code quality improves measurably
- Developer satisfaction is high
- Implementation plans successfully execute via superpowers
- System files improve over time based on real outcomes

---

## Success Metrics

### System Performance
- ✅ Agents complete analysis in < 2 minutes
- ✅ 95%+ successful agent executions
- ✅ Zero orchestrator failures

### Quality Metrics
- ✅ Implementation plans are actionable
- ✅ Recommendations are specific and relevant
- ✅ 80%+ of recommendations accepted by developers
- ✅ Reduced code review cycles

### Extensibility
- ✅ New agents can be added in < 1 day
- ✅ No orchestrator changes needed for new agents
- ✅ Agent validation catches 100% of config errors

### Adoption
- ✅ 80%+ of features have design specs
- ✅ 90%+ of design specs are analyzed
- ✅ Developer satisfaction > 4/5

---

## Risk Mitigation

### Risk: Agents provide conflicting recommendations
**Mitigation:** Orchestrator synthesis includes conflict resolution; flag for human review when needed

### Risk: Analysis takes too long
**Mitigation:** Implement timeout, background execution, or partial results

### Risk: Agents become stale
**Mitigation:** Monthly review schedule, automated validation, easy update process

### Risk: Token costs too high
**Mitigation:** Smart agent selection, caching, result reuse for similar specs

### Risk: Low adoption
**Mitigation:** Clear value demonstration, easy onboarding, lightweight process

---

## Timeline Summary

- **Phase 0:** 2-3 days - Setup
- **Phase 1:** 3-4 days - Infrastructure
- **Phase 2:** 1-2 weeks - Create agents (parallelizable)
- **Phase 3:** 1 week - Build orchestrator
- **Phase 4:** 3-5 days - Extensibility
- **Phase 5:** 1 week - Testing
- **Phase 6:** 3-5 days - Documentation
- **Phase 7:** Ongoing - Integration, Superpowers, Self-Optimization

**Total:** 4-6 weeks to full production

**MVP (Phases 0-3):** 2-3 weeks

---

## Next Steps

1. Review and approve this implementation plan
2. Set up initial directory structure (Phase 0)
3. Begin agent infrastructure work (Phase 1)
4. Parallelize agent creation (Phase 2)
5. Build orchestrator (Phase 3)
6. Test and iterate (Phases 4-5)
7. Document and deploy (Phases 6-7)

## Questions for Discussion

1. Should we start with all 7 agents or prioritize a subset?
2. What model should we use for agents (opus vs sonnet)?
3. How should we handle token costs at scale?
4. Should we integrate skill-creator now or later?
5. What CI/CD integration is needed?
6. Who will maintain and update agent prompts?
