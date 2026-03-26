# Adding New Domain Expert Agents

**Version:** 1.0
**Last Updated:** 2026-03-25

---

## Quick Start

1. Copy the agent template
2. Customize for your domain with comprehensive expertise
3. Register in the agent registry
4. Test with a sample design spec
5. Document the new agent

---

## Detailed Steps

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
      domain: "Security and compliance practices"
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

### Step 6: Validate

Run the `agent-registry-helper` to check configuration. It can verify:
- Agent definition file has valid structure
- Required fields are present
- Agent is properly registered in `agents.yaml`
- No naming conflicts with existing agents

### Step 7: Test

Create a test design spec that exercises the new agent's domain:

```bash
# Create test spec
cat > specs/test-security-expert.md << 'EOF'
# Design Spec: Add RBAC Support

## Overview
Add role-based access control to the operator's API.

## Proposed Changes
### API Changes
- Add Role and RoleBinding CRDs
- Add authentication middleware

### Security Requirements
- Support for OIDC tokens
- Secret rotation
- Audit logging

## Domain Experts to Consult
- [x] Security practices
EOF
```

Test the agent:
1. Run `/analyze-spec test-security-expert.md --focus=security`
2. Verify the agent produces comprehensive, actionable recommendations
3. Check the output follows the expected format
4. Ensure recommendations are specific to the design spec, not generic

### Step 8: Update Documentation

Add the new agent to [agent-registry.md](agent-registry.md) with:
- Agent name and purpose
- When to use it
- Key expertise areas
- Example triggers

If using `--focus` mode, add the short name mapping to `.claude/commands/analyze-spec.md`.

---

## Best Practices

### Agent Design

- **One domain per agent**: Each agent should be expert in one clear domain. Don't create a "security-and-performance-expert".
- **Comprehensive expertise**: Go deep rather than wide. The agent's value is proportional to the depth of knowledge in its prompt.
- **Current knowledge**: Research the latest versions and practices before writing the agent. Outdated advice is worse than no advice.
- **Actionable output**: Recommendations should tell the user *what* to do, *why*, and *how*. Generic advice ("follow best practices") adds no value.
- **Codebase awareness**: Instruct the agent to search for existing patterns and build on them rather than suggesting new approaches that conflict with the codebase.

### Triggers

- Be specific: `"rbac"` is better than `"access"`
- Include abbreviations: both `"kubernetes"` and `"k8s"`
- Include file patterns: `"*.go"`, `"_test.go"`
- Test trigger matching against real design specs

### Model Selection

All domain expert agents use Opus for consistent deep analysis quality. Since agents run in parallel, using a faster model on some agents doesn't improve overall analysis time (it's bottlenecked by the slowest agent).

---

## Common Agent Types

### Analysis Agents (Default)
- Focus on reviewing design specs and providing recommendations
- Tools: Read, Grep, Glob, WebSearch
- Examples: go-expert, k8s-expert, all current domain experts

### Research Agents
- Deep research and learning, may take longer
- Tools: WebSearch, Read, Grep
- Use case: Evaluating new technologies, comparing approaches

### Implementation Agents (Advanced)
- Can modify code (not just recommend)
- Tools: Read, Write, Edit, Bash
- Use case: Automated refactoring, migration scripts
- **Note**: These require more careful testing and review

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

## Troubleshooting

### Agent not triggered in smart mode
- Check triggers in `agents.yaml` match design spec content
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
- Registry helper (validation + discovery): `.claude/agents/tools/agent-registry-helper.md`

### Related Documents
- [User Guide](user-guide.md) — How to use the system
- [Developer Guide](developer-guide.md) — How the system works internally
- [Architecture](architecture.md) — System overview and design
- [Agent Registry](agent-registry.md) — Catalog of all agents
