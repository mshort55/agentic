# skill-creator Integration

**Status:** Planned
**Priority:** Phase 4-5
**Last Updated:** 2026-03-25

---

## Overview

This document outlines how to integrate the `skill-creator` skill with the agentic workflow system to streamline domain expert agent creation, optimization, and maintenance.

**skill-creator** is a built-in Claude Code skill for creating, modifying, and evaluating skills. While our domain expert agents are more complex than typical skills, skill-creator's capabilities can be leveraged for agent lifecycle management.

---

## Current Status

### Available Now
- Manual agent creation using templates
- `domain-expert-template.md` for consistency
- `agent-registry-helper` for validation

### Planned Integration
- Automated agent generation with skill-creator
- Agent quality evaluation and optimization
- Performance benchmarking
- Prompt refinement based on metrics

---

## Integration Approach

### Phase 1: Manual Template Usage (Current - Phase 1)

**Status:** ✅ Complete

**Approach:**
Use existing templates to create new domain expert agents manually.

**Workflow:**
1. Copy `domain-expert-template.md`
2. Replace placeholders manually
3. Embed comprehensive expertise in agent prompt
4. Register in `agents.yaml`
5. Test with design specs

**Tools:**
- Template files
- Manual editing
- agent-registry-helper for validation

**Pros:**
- Full control over agent definition
- Deep understanding of agent structure
- No dependencies

**Cons:**
- Time-consuming for multiple agents
- Manual consistency enforcement
- No built-in validation during creation

---

### Phase 2: skill-creator for Agent Generation (Planned - Phase 4)

**Status:** 📋 Planned

**Approach:**
Enhance skill-creator workflow to understand domain expert agent pattern and automate creation.

#### 2.1 Prepare Agent-Specific Templates

**Goal:** Make domain expert template skill-creator compatible

**Action Items:**
1. **Create skill-creator-compatible template**
   - Convert `domain-expert-template.md` to skill-creator format
   - Add parameter placeholders skill-creator understands
   - Include validation rules

2. **Define agent schema**
   - Document required fields
   - Define validation constraints
   - Specify allowed values for enums (priority, model, etc.)

3. **Create generation script**
   - Prompt for agent metadata
   - Validate inputs
   - Generate files from template
   - Auto-register in `agents.yaml`

#### 2.2 Add Domain Expert Creation Workflow

**Proposed Command:**
```bash
/skill-creator create-agent
```

**Interactive Prompts:**
```
Creating new domain expert agent...

Agent Name (e.g., security-expert): security-expert
Domain (e.g., Security and Compliance): Security and Compliance
Description: Expert in security best practices, authentication, authorization, and compliance

Expertise Areas (comma-separated):
- Authentication and authorization
- Secrets management
- RBAC design
- Security scanning
- Compliance requirements
- Vulnerability remediation

Triggers (comma-separated): security, auth, secrets, rbac, compliance, cve

Tools needed (select):
  [x] Read
  [x] Grep
  [x] Glob
  [x] WebSearch
  [ ] Bash
  [ ] Write
  [ ] Edit

Priority (high/medium/low): high
Model (opus/sonnet): opus
```

**Generated Files:**
1. `.claude/agents/domain-experts/security-expert.md` (with comprehensive expertise)
2. Updates `config/agents.yaml`

**Post-Generation Actions:**
- Run agent-registry-helper validation
- Create test design spec template
- Provide next steps for customization

#### 2.3 Auto-Registration

**Goal:** Automatically update `agents.yaml` with new agent

**Approach:**
1. Read current `agents.yaml`
2. Parse YAML structure
3. Insert new agent entry in correct section
4. Preserve formatting and comments
5. Write updated file
6. Validate with agent-registry-helper

**Safety:**
- Backup `agents.yaml` before modification
- Validate YAML syntax after update
- Rollback on validation failure

---

### Phase 3: Agent Optimization with skill-creator (Planned - Phase 5)

**Status:** 📋 Planned

**Approach:**
Use skill-creator's evaluation and optimization features to improve agent quality.

#### 3.1 Agent Quality Evaluation

**Goal:** Measure agent performance and output quality

**Command:**
```bash
/skill-creator eval-agent go-expert --test-specs design-specs/examples/
```

**Evaluation Process:**
1. Load agent definition
2. Run agent against test design specs
3. Measure:
   - Response completeness
   - Recommendation specificity
   - Example quality
   - Response time
   - Token usage
4. Generate quality score and report

**Output:**
```markdown
# Evaluation Report: go-expert

## Overall Score: 8.5/10

### Metrics
- Completeness: 9/10 (all sections present)
- Specificity: 8/10 (recommendations are actionable)
- Examples: 9/10 (code snippets provided)
- Performance: 7/10 (avg 45s response time)
- Token Efficiency: 8/10 (avg 8K tokens)

### Strengths
- Excellent code examples
- Clear prioritization
- Good risk assessment

### Areas for Improvement
- Add more codebase references
- Reduce response time for simple specs
- Include more "how-to" implementation steps

### Recommendations
1. Optimize prompt to encourage codebase references
2. Add examples of common patterns to agent knowledge
3. Consider streaming responses for faster TTFR
```

#### 3.2 Prompt Optimization

**Goal:** Refine agent prompts based on evaluation results

**Command:**
```bash
/skill-creator optimize-agent go-expert
```

**Optimization Process:**
1. Analyze evaluation results
2. Identify prompt weaknesses
3. Suggest prompt improvements
4. A/B test variations
5. Select best performing version

**Example Improvements:**
- Add "Always reference existing code in `pkg/` directory"
- Include "Provide step-by-step implementation in 'How' section"
- Emphasize "Include at least one code example per recommendation"

#### 3.3 Performance Benchmarking

**Goal:** Compare agent performance across versions

**Command:**
```bash
/skill-creator benchmark-agent go-expert --baseline v1.0 --candidate v1.1
```

**Benchmark Metrics:**
- Response time (p50, p95, p99)
- Token usage
- Quality scores
- Consistency (variance across runs)
- Trigger accuracy

**Output:**
```markdown
# Benchmark: go-expert v1.0 vs v1.1

## Performance
| Metric | v1.0 | v1.1 | Change |
|--------|------|------|--------|
| Avg Response Time | 45s | 38s | -16% ✅ |
| Avg Tokens | 8.2K | 7.8K | -5% ✅ |
| Quality Score | 8.5 | 8.9 | +5% ✅ |

## Recommendation
✅ Deploy v1.1 - Improved performance without quality loss
```

#### 3.4 Variance Analysis

**Goal:** Ensure agent provides consistent recommendations

**Command:**
```bash
/skill-creator analyze-variance go-expert --runs 10 --spec design-specs/test-spec.md
```

**Analysis:**
- Run agent 10 times on same spec
- Compare recommendations across runs
- Measure consistency scores
- Identify areas of high variance

**Output:**
```markdown
# Variance Analysis: go-expert

## Consistency Score: 92%

### High Consistency (95%+)
- Core recommendations (always suggests same key practices)
- Risk assessment (identifies same risks)
- Priority levels (consistent prioritization)

### Moderate Variance (80-95%)
- Example selection (varies which code examples chosen)
- Wording (different phrasing of same ideas)

### High Variance (< 80%)
- None detected

## Recommendation
✅ Agent is highly consistent
```

---

## Implementation Workflow

### Workflow 1: Creating a New Agent

**Traditional Approach (Current):**
```bash
# Manual process
1. Copy template: cp templates/domain-expert-template.md domain-experts/security-expert.md
2. Edit file: vim domain-experts/security-expert.md
3. Replace all placeholders
4. Embed comprehensive domain expertise in prompt
5. Register: vim config/agents.yaml (add entry)
6. Validate: agent-registry-helper validate
7. Test: create test spec and run
```
**Time:** ~1.5-2 hours

**skill-creator Approach (Future):**
```bash
# Automated process
1. Run: /skill-creator create-agent
2. Answer prompts (5 minutes)
3. Review generated files
4. Customize domain-specific expertise sections (30 minutes)
5. Test with provided test spec template

# Generated files:
# - .claude/agents/domain-experts/security-expert.md (with expertise scaffold)
# - config/agents.yaml (auto-updated)
# - design-specs/examples/test-security-expert.md (template)
```
**Time:** ~40 minutes

---

### Workflow 2: Optimizing an Agent

**Traditional Approach (Current):**
```bash
# Manual optimization
1. Run agent on multiple specs
2. Manually review outputs
3. Identify patterns in weaknesses
4. Edit agent prompt
5. Re-test
6. Repeat until satisfied
```
**Time:** ~4-6 hours per iteration

**skill-creator Approach (Future):**
```bash
# Data-driven optimization
1. Run: /skill-creator eval-agent go-expert --test-specs design-specs/examples/
2. Review evaluation report (highlights specific weaknesses)
3. Run: /skill-creator optimize-agent go-expert
4. Review suggested improvements
5. Accept or customize changes
6. Run: /skill-creator benchmark-agent go-expert --baseline v1.0 --candidate v1.1
7. Deploy if improved
```
**Time:** ~1-2 hours with data-driven insights

---

### Workflow 3: Maintaining Agents

**Traditional Approach (Current):**
```bash
# Quarterly review
1. Manually review each agent definition
2. Check for outdated practices
3. Update based on new standards
4. Test each updated agent
5. Document changes
```
**Time:** ~1 hour per agent × 7 agents = ~7 hours

**skill-creator Approach (Future):**
```bash
# Automated maintenance
1. Run: /skill-creator audit-agents --check-staleness
2. Review flagged agents
3. For each flagged:
   - /skill-creator eval-agent {{agent}}
   - Review recommendations
   - Update as needed
4. Bulk benchmark: /skill-creator benchmark-all-agents
```
**Time:** ~3-4 hours with targeted updates

---

## Benefits

### Time Savings
- **Agent Creation:** 60% faster (3 hours → 45 minutes)
- **Optimization:** 70% faster (6 hours → 2 hours)
- **Maintenance:** 50% faster (7 hours → 3-4 hours)

### Quality Improvements
- **Consistency:** Templates ensure uniform structure
- **Data-Driven:** Optimization based on metrics, not gut feel
- **Testing:** Built-in evaluation framework
- **Documentation:** Auto-generated documentation stubs

### Developer Experience
- **Lower Barrier:** Easier for team members to create agents
- **Faster Iteration:** Quick feedback loop on changes
- **Confidence:** Benchmarking shows impact of changes
- **Scalability:** Easy to add new agents as needs grow

---

## Risks & Mitigations

### Risk 1: Over-Automation
**Concern:** Auto-generated agents lack domain depth

**Mitigation:**
- skill-creator generates scaffolding, not full implementation
- Domain experts still customize expertise sections
- Evaluation ensures quality before deployment
- Hybrid approach: automate structure, humans add depth

### Risk 2: Prompt Drift
**Concern:** Optimization changes agent behavior unexpectedly

**Mitigation:**
- Always benchmark against baseline
- Require quality score improvement
- Keep version history
- Easy rollback mechanism

### Risk 3: Evaluation Bias
**Concern:** Test specs don't represent real usage

**Mitigation:**
- Use diverse test specs (simple to complex)
- Include real historical design specs
- Update test suite quarterly
- Collect user feedback on agent quality

### Risk 4: Complexity Creep
**Concern:** skill-creator integration adds complexity

**Mitigation:**
- Keep manual workflow as fallback
- Document both approaches
- Gradual rollout (one agent at a time)
- Train team before full adoption

---

## Rollout Plan

### Phase 4.1: Prototype Integration (Week 1)
- Create skill-creator-compatible template
- Test agent generation workflow
- Validate auto-registration
- Document findings

### Phase 4.2: Pilot with One Agent (Week 2)
- Use skill-creator to create new agent (e.g., security-expert)
- Compare to manual approach
- Gather metrics on time savings
- Refine workflow based on feedback

### Phase 4.3: Evaluation Framework (Week 3)
- Set up test design specs
- Define quality metrics
- Create evaluation baseline for existing agents
- Document evaluation process

### Phase 4.4: Optimization Pilot (Week 4)
- Select one agent for optimization
- Run eval and benchmark
- Apply optimizations
- Measure improvements

### Phase 5.1: Team Training (Week 5)
- Train team on skill-creator workflows
- Create quickstart guide
- Set up office hours for questions
- Collect feedback

### Phase 5.2: Full Rollout (Week 6+)
- Make skill-creator approach default
- Update documentation
- Monitor adoption
- Iterate based on feedback

---

## Success Metrics

### Adoption
- 80% of new agents created with skill-creator
- 50% of agent updates use optimization workflow
- All agents evaluated quarterly

### Efficiency
- Agent creation time reduced by 50%+
- Optimization cycle time reduced by 60%+
- Maintenance overhead reduced by 40%+

### Quality
- Average agent quality score > 8.5/10
- Agent consistency score > 90%
- Zero config validation failures

---

## Future Enhancements

### Long-Term Vision

1. **Natural Language Agent Creation**
   - Describe agent in plain English
   - skill-creator generates full definition
   - Minimal manual editing needed

2. **Continuous Optimization**
   - Agents self-improve based on usage metrics
   - A/B testing of prompt variations
   - Automatic prompt refinement

3. **Agent Analytics Dashboard**
   - Real-time usage metrics
   - Quality trends over time
   - Performance comparisons
   - ROI tracking

4. **Community Agent Library**
   - Share agent definitions
   - Rate and review agents
   - Import proven agents
   - Best practices sharing

---

## Resources

### skill-creator Documentation
- Run `/help skill-creator` for usage
- See skill-creator skill description for capabilities
- Check Claude Code docs for latest features

### Related Documents
- [Agent Template](../../.claude/agents/templates/domain-expert-template.md)
- [Agent Registry Helper](../../.claude/agents/tools/agent-registry-helper.md)
- [Adding New Agents Guide](adding-new-agents.md) (Phase 4)

---

## Questions & Feedback

### Open Questions
1. Should skill-creator integration be optional or default?
2. What quality threshold should trigger re-evaluation?
3. How often should agents be benchmarked?
4. Who owns agent optimization (platform team vs domain experts)?

### Provide Feedback
- Create issue with label `skill-creator-integration`
- Discuss in team sync
- Add suggestions to this document

---

**Document Status:** Living Document
**Review Cycle:** Monthly during Phase 4-5, Quarterly after
**Owner:** Platform Team
**Last Reviewed:** 2026-03-25
