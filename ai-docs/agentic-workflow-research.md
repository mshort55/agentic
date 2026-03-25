# Agentic Workflow Research for Design Spec Implementation

## Your Requirements

Build an agentic workflow where:
- Main agent reads a design spec (high-level markdown with feature goals)
- Agent consults specialized knowledge sources for:
  - Go practices
  - Kubernetes practices
  - Kubernetes controller practices
  - CRD practices
  - Unit testing practices
  - E2E test practices (Ginkgo)
  - General coding practices
- System must be extensible for future practice areas
- Must follow current agentic workflow best practices (March 2026)

## Key Research Findings

### Claude Code / Agent SDK Capabilities

**Agent SDK Overview:**
- Claude Agent SDK (renamed from Claude Code SDK) enables building autonomous agents
- Available in Python and TypeScript
- Agents can read files, run commands, search web, edit code autonomously
- Same tools and agent loop that power Claude Code

**Installation:**
```bash
pip install claude-agent-sdk  # Python 3.10+
npm install @anthropic-ai/claude-agent-sdk  # TypeScript
```

**Key Features (March 2026):**
- Subagents for specialized tasks
- Skills system for reusable capabilities
- Hooks for automated behaviors
- Memory system for persistence across conversations
- Multi-agent coordination

### Agentic Workflow Patterns (2026 Best Practices)

**Core Architectural Patterns:**

1. **Sequential/Chain Pattern**: Fixed pipeline where output of step A → B → C (assembly line)

2. **Hierarchical/Supervisor Pattern**: Coordinator agent delegates to specialized worker agents

3. **Swarm/Collaborative Pattern**: Peer agents with emergent coordination, challenging and converging

4. **Hybrid Custom Workflows**: Mix of patterns (most production systems use this)

**Key Principles:**

- **Start Simple**: Begin with single agent, add multi-agent structure when needed for parallelism, separation of duties, reliability, or permission boundaries

- **Structure Over Complexity**: Most failures come from missing structure, not model capability. Treat agents like code, not chat interfaces.

- **Share Minimally**: Share only what's needed, keep sensitive fields scoped to the agent that needs them

- **Tool Safety**: Strict validation, clear boundaries, examples of what NOT to do, least-privilege permissions

- **Buy/Fork Before Building**: Use high-quality open-source components rather than building from scratch

### Model Context Protocol (MCP)

New 2026 standard by Anthropic for how agents access tools and resources, enabling standardized tool sharing across agents.

## Implementation Options

### Option 1: Hierarchical Supervisor Pattern with Skills

**Architecture:**
```
Main Agent (Supervisor)
├── Reads design spec
├── Determines which practice areas are needed
└── Consults Skills for each area:
    ├── /go-practices skill
    ├── /kubernetes-practices skill
    ├── /controller-practices skill
    ├── /crd-practices skill
    ├── /unit-testing-practices skill
    ├── /e2e-testing-practices skill
    └── /coding-practices skill
```

**How It Works:**
- Create a skill for each practice area using Claude Code's skill system
- Each skill is a reusable, invocable prompt with domain expertise
- Main agent orchestrates by calling skills as needed
- Skills return recommendations/guidance to main agent
- Main agent synthesizes into implementation plan

**Pros:**
- ✅ Leverages built-in Claude Code skill system
- ✅ Skills are simple to create and maintain
- ✅ Easy to extend - just add new skill files
- ✅ Low overhead - skills execute in main conversation
- ✅ Can use skill triggering for automatic invocation
- ✅ Good for knowledge retrieval and guidance

**Cons:**
- ❌ Skills execute sequentially within main context
- ❌ Limited parallelization capabilities
- ❌ All skill execution shares same token budget
- ❌ Skills can't maintain separate state or memory

**Best For:**
- Quick guidance lookups
- Lightweight practice consultations
- When practices are documented and stable
- Single-threaded workflows

---

### Option 2: Multi-Agent System with Specialized Agents

**Architecture:**
```
Orchestrator Agent
├── Reads design spec
├── Spawns parallel subagents:
│   ├── Go Practices Agent (subagent_type: "go-expert")
│   ├── K8s Practices Agent (subagent_type: "k8s-expert")
│   ├── Controller Practices Agent (subagent_type: "controller-expert")
│   ├── CRD Practices Agent (subagent_type: "crd-expert")
│   ├── Unit Test Practices Agent (subagent_type: "test-expert")
│   ├── E2E Test Practices Agent (subagent_type: "e2e-expert")
│   └── Coding Practices Agent (subagent_type: "coding-expert")
└── Synthesizes all agent results into implementation plan
```

**How It Works:**
- Create custom agent definitions for each practice area
- Each agent has specialized tools, prompts, and knowledge
- Orchestrator launches agents in parallel using Agent tool
- Agents can do deep research, file analysis, web searches independently
- Results returned to orchestrator for synthesis

**Pros:**
- ✅ True parallelization - all agents run simultaneously
- ✅ Each agent has isolated context and token budget
- ✅ Agents can perform complex research (WebSearch, file reads, codebase analysis)
- ✅ Better separation of concerns
- ✅ Can run agents in background for long-running tasks
- ✅ Each agent can have specialized tools and permissions
- ✅ Scales well with complexity

**Cons:**
- ❌ More complex to set up initially
- ❌ Higher token usage (each agent has full context)
- ❌ Need to create agent definition files
- ❌ Coordination overhead for orchestrator
- ❌ Results are point-in-time (agents don't persist)

**Best For:**
- Complex analysis requiring research
- When practices need to analyze codebase
- Parallel processing is important
- Dynamic, evolving practice areas

---

### Option 3: Hybrid Skill + Agent System

**Architecture:**
```
Main Agent
├── Reads design spec
├── Quick consultations via Skills (for stable practices):
│   ├── /go-practices skill
│   └── /coding-practices skill
└── Deep research via Subagents (for complex areas):
    ├── K8s Controller Analysis Agent
    ├── CRD Design Agent
    └── Testing Strategy Agent
```

**How It Works:**
- Use skills for quick, well-defined practice lookups
- Use agents for areas requiring codebase analysis, research, or complex reasoning
- Main agent decides which consultation method based on task complexity
- Best of both worlds approach

**Pros:**
- ✅ Optimized resource usage - skills for simple, agents for complex
- ✅ Flexibility to handle different complexity levels
- ✅ Can parallelize where needed, serialize where not
- ✅ Easier to extend - new areas default to skills, upgrade to agents if needed
- ✅ Lower token costs for simple lookups

**Cons:**
- ❌ More complex decision logic in orchestrator
- ❌ Inconsistent interface (some skills, some agents)
- ❌ Requires maintenance of two systems
- ❌ May be over-engineered for simple use cases

**Best For:**
- Mixed complexity scenarios
- When some practices are stable, others evolving
- Optimizing for cost/performance
- Production systems with varied needs

---

### Option 4: Knowledge Base + RAG + Single Agent

**Architecture:**
```
Single Agent with Knowledge Base
├── Reads design spec
├── Accesses practice knowledge base (markdown files):
│   ├── docs/practices/go-practices.md
│   ├── docs/practices/k8s-practices.md
│   ├── docs/practices/controller-practices.md
│   ├── docs/practices/crd-practices.md
│   ├── docs/practices/testing-practices.md
│   └── docs/practices/coding-practices.md
└── Uses Grep/Read to retrieve relevant sections
```

**How It Works:**
- Maintain markdown files with practice guidelines
- Agent reads design spec, identifies relevant practices
- Uses Grep to find relevant sections in practice docs
- Synthesizes into implementation guidance
- Simple, direct, no multi-agent complexity

**Pros:**
- ✅ Simplest approach - easy to understand and maintain
- ✅ Practice docs are version controlled and reviewable
- ✅ Fast - no agent spawning overhead
- ✅ Easy to extend - add new markdown files
- ✅ Team can contribute to practice docs directly
- ✅ Low token usage
- ✅ Practices are explicit and auditable

**Cons:**
- ❌ No parallelization
- ❌ Limited to static knowledge
- ❌ Can't perform dynamic research or codebase analysis
- ❌ Agent must do all synthesis work
- ❌ May miss context-specific guidance

**Best For:**
- Well-documented, stable practices
- Small to medium teams
- When you want human-editable practice docs
- Quick MVP implementation

---

### Option 5: Agent SDK Custom Multi-Agent System

**Architecture:**
```python
# Custom orchestrator using Agent SDK
from claude_agent_sdk import Agent, create_workflow

# Define specialized agents
go_agent = Agent(
    name="go-practices",
    system_prompt="You are a Go expert...",
    tools=[Read, Grep, WebSearch],
    memory_path="./memory/go-practices/"
)

k8s_agent = Agent(
    name="k8s-practices",
    system_prompt="You are a Kubernetes expert...",
    tools=[Read, Grep, WebSearch, Bash],
    memory_path="./memory/k8s-practices/"
)

# Orchestrator coordinates
workflow = create_workflow([
    ("read_spec", read_design_spec),
    ("analyze_parallel", [go_agent, k8s_agent, controller_agent]),
    ("synthesize", synthesize_results),
    ("generate_plan", create_implementation_plan)
])
```

**How It Works:**
- Build custom multi-agent system using Agent SDK
- Full programmatic control over agent lifecycle
- Can implement sophisticated coordination patterns
- Agents can have persistent memory
- Can integrate with external systems (databases, APIs)

**Pros:**
- ✅ Maximum flexibility and control
- ✅ Agents can have persistent memory across runs
- ✅ Custom coordination logic
- ✅ Can integrate with CI/CD, databases, external APIs
- ✅ Production-ready with proper error handling
- ✅ Can implement complex workflow patterns
- ✅ Testable and deployable as standalone service

**Cons:**
- ❌ Most complex to implement
- ❌ Requires Python/TypeScript development
- ❌ Higher maintenance burden
- ❌ Infrastructure requirements (hosting, deployment)
- ❌ Steeper learning curve

**Best For:**
- Production systems at scale
- When you need full control and customization
- Integration with existing infrastructure
- Long-running, automated workflows
- Enterprise deployments

---

## Comparison Matrix

| Criteria | Skills | Agents | Hybrid | Knowledge Base | Agent SDK |
|----------|--------|--------|--------|----------------|-----------|
| Setup Complexity | ⭐ Low | ⭐⭐⭐ Medium | ⭐⭐⭐ Medium | ⭐ Low | ⭐⭐⭐⭐⭐ High |
| Parallelization | ❌ No | ✅ Yes | ⚡ Partial | ❌ No | ✅ Yes |
| Extensibility | ✅ Easy | ✅ Easy | ⚡ Moderate | ✅ Easy | ✅ Easy |
| Token Efficiency | ✅ High | ⚡ Medium | ✅ High | ✅ High | ⚡ Medium |
| Research Capability | ⚡ Limited | ✅ Full | ✅ Full | ❌ None | ✅ Full |
| Maintenance | ✅ Low | ⚡ Medium | ⚡ Medium | ✅ Low | ❌ High |
| Production Ready | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes |
| Codebase Analysis | ❌ Limited | ✅ Yes | ✅ Yes | ❌ No | ✅ Yes |

## Recommended Implementation Strategy

### Phase 1: Start Simple (Week 1-2)
**Approach: Knowledge Base + Single Agent (Option 4)**

1. Create `docs/practices/` directory structure
2. Document each practice area in markdown files
3. Create a simple orchestrator that:
   - Reads design spec
   - Identifies relevant practice areas
   - Greps/reads practice docs
   - Synthesizes implementation plan

**Why start here:**
- Fastest time to value
- Forces documentation of practices (valuable regardless)
- Easy for team to review and contribute
- Validates the workflow concept
- Low complexity, low risk

### Phase 2: Add Parallelization (Week 3-4)
**Approach: Upgrade to Multi-Agent System (Option 2)**

1. Convert practice docs into agent prompts
2. Create specialized agent definitions
3. Orchestrator launches agents in parallel
4. Each agent analyzes design spec from their domain perspective
5. Orchestrator synthesizes results

**Why this next:**
- Significant performance improvement with parallelization
- Agents can analyze existing codebase for patterns
- Can perform web research for latest best practices
- Still using built-in Claude Code Agent system
- Natural evolution from Phase 1

### Phase 3: Optimize and Scale (Month 2+)
**Approach: Hybrid or Agent SDK (Options 3 or 5)**

Based on learnings from Phases 1-2:
- If simple lookups dominate → Hybrid approach
- If need CI/CD integration → Agent SDK
- If complex coordination needed → Agent SDK
- Otherwise stick with Phase 2 approach

## Implementation Details

### Sample Skill Definition (Go Practices)

```markdown
---
name: go-practices
description: Consult Go best practices for code review and implementation
triggers:
  - "go best practices"
  - "golang patterns"
  - "go coding standards"
---

You are a Go expert consultant. When asked about Go practices, provide guidance on:

## Areas of Expertise:
- Idiomatic Go code patterns
- Error handling (errors.Is, errors.As, wrapping)
- Concurrency (goroutines, channels, sync primitives)
- Project structure and organization
- Testing patterns (table-driven tests, testify, mock generation)
- Performance considerations
- Common pitfalls to avoid

## Guidelines:
1. Always prefer simplicity over cleverness
2. Follow effective Go guidelines
3. Consider error handling edge cases
4. Think about concurrency safety
5. Recommend standard library solutions first

When consulting on a design spec:
1. Read the spec carefully
2. Identify Go-specific concerns
3. Provide concrete, actionable recommendations
4. Reference existing codebase patterns where relevant
5. Flag potential issues early
```

### Sample Agent Definition (Controller Practices)

```yaml
---
name: controller-expert
description: Kubernetes controller best practices agent
model: opus
tools:
  - Read
  - Grep
  - Glob
  - WebSearch
  - Bash
system_prompt: |
  You are an expert in Kubernetes controller development with deep knowledge of:
  - controller-runtime patterns
  - Reconciliation loops
  - Event handling and filtering
  - Error handling and retry strategies
  - Leader election
  - Garbage collection and finalizers
  - RBAC and security
  - Performance and scaling

  When analyzing a design spec:
  1. Identify resources being reconciled
  2. Review reconciliation logic requirements
  3. Check error handling strategy
  4. Evaluate scalability concerns
  5. Validate RBAC requirements
  6. Assess testing approach
  7. Flag anti-patterns

  Provide specific, actionable recommendations with code examples.
---
```

### Sample Orchestrator Flow

```markdown
# Design Spec Implementation Workflow

## Step 1: Understand Spec
- Read design spec file
- Extract key requirements
- Identify affected areas (API, controller, tests, etc.)

## Step 2: Consult Practice Experts
Launch in parallel:
- Go practices review
- K8s practices review
- Controller practices review (if controller changes)
- CRD practices review (if API changes)
- Unit test practices review
- E2E test practices review
- General coding practices review

## Step 3: Analyze Existing Codebase
- Find similar patterns in current code
- Identify files that need changes
- Check for existing utilities/helpers

## Step 4: Synthesize Recommendations
- Combine all expert recommendations
- Resolve conflicts between different practices
- Prioritize recommendations
- Create actionable implementation plan

## Step 5: Generate Implementation Plan
Output structured plan with:
- Files to create/modify
- Key implementation steps
- Testing requirements
- Potential risks/challenges
- Estimated complexity
```

## Next Steps

1. **Review and decide**: Choose implementation option based on:
   - Team size and expertise
   - Complexity of your projects
   - Time available for setup
   - Need for parallelization
   - Integration requirements

2. **Start with MVP**: I recommend starting with Option 4 (Knowledge Base) to:
   - Document practices immediately
   - Validate workflow concept
   - Get team buy-in
   - Learn what works

3. **Iterate based on needs**: Upgrade to multi-agent (Option 2) when:
   - Need faster execution (parallelization)
   - Practices need codebase analysis
   - Want dynamic research capabilities

4. **Consider Agent SDK** (Option 5) only if:
   - Building production automation
   - Need CI/CD integration
   - Have dedicated infrastructure
   - Team has development capacity

## Resources

### Claude Code & Agent SDK
- [Agent SDK Overview](https://platform.claude.com/docs/en/agent-sdk/overview)
- [Agent SDK Quickstart](https://platform.claude.com/docs/en/agent-sdk/quickstart)
- [Claude Code Features in SDK](https://platform.claude.com/docs/en/agent-sdk/claude-code-features)
- [Python SDK GitHub](https://github.com/anthropics/claude-agent-sdk-python)

### Agentic Workflow Best Practices
- [Agentic Workflows in 2026: Ultimate Guide](https://vellum.ai/blog/agentic-workflows-emerging-architectures-and-design-patterns)
- [2026 Guide to Agentic Workflow Architectures](https://www.stackai.com/blog/the-2026-guide-to-agentic-workflow-architectures)
- [Multi-Agent Workflows - GitHub Blog](https://github.blog/ai-and-ml/generative-ai/multi-agent-workflows-often-fail-heres-how-to-engineer-ones-that-dont/)
- [Best Practices for AI Agent Implementations 2026](https://onereach.ai/blog/best-practices-for-ai-agent-implementations/)
- [How to Build Multi-Agent Systems: Complete 2026 Guide](https://dev.to/eira-wexford/how-to-build-multi-agent-systems-complete-2026-guide-1io6)

### AI Orchestration & Patterns
- [Top 10+ Agentic Orchestration Frameworks & Tools 2026](https://aimultiple.com/agentic-orchestration)
- [AI Agent Orchestration: Coordination, Scale and Strategy](https://kanerika.com/blogs/ai-agent-orchestration/)
- [Deloitte: Unlocking Value with AI Agent Orchestration](https://www.deloitte.com/us/en/insights/industry/technology/technology-media-and-telecom-predictions/2026/ai-agent-orchestration.html)
- [Agentic AI Strategy - Deloitte Insights](https://www.deloitte.com/us/en/insights/topics/technology-management/tech-trends/2026/agentic-ai-strategy.html)
- [Multi-Agent Systems & AI Orchestration Guide 2026](https://www.codebridge.tech/articles/mastering-multi-agent-orchestration-coordination-is-the-new-scale-frontier)

---

**Document created:** March 25, 2026
**Research focus:** Agentic workflow best practices for design spec implementation with extensible practice consultation
