---
name: agent-registry-helper
description: Helper for managing agent registry and discovering agents
type: tool
---

# Agent Registry Helper

This helper manages the agent registry and assists with agent discovery and validation.

## Capabilities

### 1. List Available Agents
Show all registered domain expert agents with their configuration.

### 2. Check Agent Status
Verify which agents are enabled, disabled, or have configuration issues.

### 3. Suggest Agents for Spec
Analyze a design spec and suggest which domain expert agents should be consulted based on triggers.

### 4. Validate Agent Config
Check `agents.yaml` for correctness, completeness, and consistency.

### 5. Generate Agent Report
Create a markdown summary of all agents and their capabilities.

### 6. Discover Agents
Scan filesystem for agent definitions and compare against registry.

---

## Usage

When invoked, determine which operation is needed based on the request:

### List Agents

**Request:** "List all agents" or "Show available agents"

**Action:**
1. Read `/UbuntuSync/agentic/config/agents.yaml`
2. Parse `domain_experts` section
3. Display formatted list with:
   - Agent name
   - Domain
   - Priority
   - Enabled status
   - Model
   - Trigger count

**Output Format:**
```markdown
# Domain Expert Agents

## Enabled Agents (7)

### High Priority (4)
1. **go-expert** - Go language and idioms
   - Model: opus
   - Triggers: 6 (go, golang, *.go files, ...)
   - Tools: Read, Grep, Glob, WebSearch

2. **k8s-expert** - Kubernetes patterns and best practices
   - Model: opus
   - Triggers: 7 (kubernetes, k8s, deployment, ...)
   - Tools: Read, Grep, Glob, WebSearch, Bash

[Continue for all high priority...]

### Medium Priority (3)
[Same format...]

## Disabled Agents (0)
None currently disabled.
```

---

### Check Agent Status

**Request:** "Check agent status" or "Validate agents"

**Action:**
1. Read `/UbuntuSync/agentic/config/agents.yaml`
2. For each agent, check:
   - Has required fields (name, type, domain, enabled, tools, triggers)
   - Type is valid (`domain_expert`, `orchestrator`, `custom`)
   - Tools are valid Claude Code tools
   - Triggers array is non-empty
   - Priority is valid (high, medium, low)
3. Report any issues

**Output Format:**
```markdown
# Agent Status Report

## Health Summary
- Total agents: 7
- Healthy: 7
- Issues: 0

## Validation Results

### ✅ go-expert
All checks passed

### ✅ k8s-expert
All checks passed

[Continue for all agents...]

## Issues Found
None
```

---

### Suggest Agents for Spec

**Request:** "Which agents for specs/my-feature.md?" or "Suggest agents for spec"

**Action:**
1. Read the design spec file
2. Read `/UbuntuSync/agentic/config/agents.yaml`
3. For each agent, check if any triggers match spec content:
   - Check section headers
   - Check "Domain Experts to Consult" checkboxes
   - Check body content for trigger keywords
4. Rank agents by:
   - Explicit checkbox selection (highest)
   - Trigger keyword matches (by count)
   - Priority level
5. Return suggested agents with reasoning

**Output Format:**
```markdown
# Suggested Agents for: specs/my-feature.md

## Strongly Recommended (3)

### go-expert
**Reason:** Explicitly selected in spec + 5 trigger matches
**Triggers matched:** go, golang, *.go files, interfaces, testing
**Confidence:** High

### k8s-expert
**Reason:** Explicitly selected in spec + 3 trigger matches
**Triggers matched:** kubernetes, deployment, service
**Confidence:** High

### controller-expert
**Reason:** 4 trigger matches found
**Triggers matched:** controller, reconcile, controller-runtime, operator
**Confidence:** Medium

## Optional (2)

### unit-test-expert
**Reason:** Testing section present, 2 trigger matches
**Triggers matched:** testing, _test.go
**Confidence:** Low

### coding-expert
**Reason:** General code changes detected
**Triggers matched:** code quality
**Confidence:** Low

## Not Recommended (2)
- crd-expert - No relevant triggers found
- e2e-test-expert - No relevant triggers found

## Recommendation
Invoke: go-expert, k8s-expert, controller-expert, unit-test-expert, coding-expert (5 agents)
```

---

### Validate Agent Config

**Request:** "Validate agent config" or "Check agents.yaml"

**Action:**
1. Read `/UbuntuSync/agentic/config/agents.yaml`
2. Validate YAML syntax
3. Validate schema:
   - version field exists
   - agent_registry exists
   - domain_experts is array
   - Each agent has required fields
4. Check for duplicates:
   - Duplicate agent names
   - Overlapping triggers (warning)
5. Validate file references:
   - Agent template path exists
   - Agent directories exist
6. Check consistency:
   - Agent files exist for registered agents
   - No orphaned agent files

**Output Format:**
```markdown
# Agent Registry Validation Report

## Schema Validation
✅ Valid YAML syntax
✅ Schema structure correct
✅ All required fields present

## Agents Configuration
✅ 7 domain experts defined
✅ 1 orchestrator defined
✅ No duplicate names
⚠️  Potential trigger overlap: "testing" in both unit-test-expert and e2e-test-expert

## File References
✅ Template path exists: .claude/agents/templates/domain-expert-template.md
✅ Agent directories exist
✅ All registered agents have definition files
✅ No orphaned agent files

## Recommendations
- Consider making triggers more specific to avoid overlap
- All checks passed, configuration is valid

## Summary
**Status:** ✅ Valid
**Warnings:** 1
**Errors:** 0
```

---

### Generate Agent Report

**Request:** "Generate agent report" or "Document all agents"

**Action:**
1. Read `/UbuntuSync/agentic/config/agents.yaml`
2. For each agent, read its definition file (if exists)
3. Extract metadata:
   - Domain and expertise areas
   - Triggers and tools
   - Purpose and capabilities
4. Generate comprehensive markdown report

**Output Format:**
```markdown
# Domain Expert Agent Registry

**Generated:** 2026-03-25
**Total Agents:** 7
**Status:** All Operational

---

## Agent Index

1. [go-expert](#go-expert) - Go language and idioms
2. [k8s-expert](#k8s-expert) - Kubernetes patterns
3. [controller-expert](#controller-expert) - Controller patterns
4. [crd-expert](#crd-expert) - Custom Resource Definitions
5. [unit-test-expert](#unit-test-expert) - Unit testing
6. [e2e-test-expert](#e2e-test-expert) - E2E testing
7. [coding-expert](#coding-expert) - General coding practices

---

## go-expert

**Domain:** Go language and idioms
**Priority:** High
**Model:** Opus
**Status:** Enabled

### Purpose
Expert in Go best practices, patterns, error handling, concurrency, and testing.

### Expertise Areas
- Idiomatic Go patterns
- Error handling (errors.Is, errors.As, wrapping)
- Concurrency (goroutines, channels, sync, context)
- Project structure (internal/, pkg/, cmd/)
- Testing (table-driven, testify, gomock)
- Performance and memory management

### Triggers
- go
- golang
- *.go files
- goroutines
- channels
- interfaces

### Tools
- Read
- Grep
- Glob
- WebSearch

### When to Use
- Design specs involving Go code changes
- New Go packages or modules
- Concurrency or error handling concerns
- Performance optimization needs

### Definition File
`.claude/agents/domain-experts/go-expert.md` (Not yet created - Phase 2)

---

[Continue for all agents...]

---

## Agent Statistics

### By Priority
- High: 4 agents (57%)
- Medium: 3 agents (43%)
- Low: 0 agents (0%)

### By Model
- Opus: 4 agents
- Sonnet: 3 agents

### By Tool Usage
- Read: 7 agents
- Grep: 7 agents
- Glob: 7 agents
- WebSearch: 6 agents
- Bash: 2 agents

### Trigger Coverage
Total unique triggers: 35
Average triggers per agent: 5

---

**Report generated by agent-registry-helper**
```

---

### Discover Agents

**Request:** "Discover agents" or "Scan for agents"

**Action:**
1. Scan directories defined in `extensibility.agent_directories`:
   - `.claude/agents/domain-experts/`
   - `.claude/agents/custom/`
2. For each `.md` file found:
   - Parse YAML frontmatter
   - Extract agent metadata
   - Validate structure
3. Compare with `config/agents.yaml`:
   - Identify registered agents
   - Identify unregistered agents (in filesystem but not in config)
   - Identify missing agents (in config but file not found)
4. Report findings

**Output Format:**
```markdown
# Agent Discovery Report

**Scan Date:** 2026-03-25
**Directories Scanned:**
- .claude/agents/domain-experts/
- .claude/agents/custom/

---

## Summary
- Registered agents: 7
- Agent files found: 7
- Unregistered agents: 0
- Missing agent files: 0
- Total issues: 0

---

## Registered Agents (7)

### ✅ go-expert
- **Status:** Registered and file exists
- **Location:** .claude/agents/domain-experts/go-expert.md
- **Config:** Enabled, priority: high

### ✅ k8s-expert
- **Status:** Registered and file exists
- **Location:** .claude/agents/domain-experts/k8s-expert.md
- **Config:** Enabled, priority: high

[Continue for all registered agents...]

---

## Unregistered Agents (0)

None found. All agent definition files are properly registered.

---

## Missing Agent Files (0)

None. All registered agents have corresponding definition files.

---

## Recommendations

✅ All agents properly configured and discovered
✅ No action needed

---

**Discovery Status:** ✅ Healthy
```

---

## Implementation Notes

### Source of Truth
Always reference: `/UbuntuSync/agentic/config/agents.yaml`

### Reading YAML
Use the Read tool to load the configuration:
```
Read file_path=/UbuntuSync/agentic/config/agents.yaml
```

### Parsing Design Specs
When analyzing specs for agent suggestions:
1. Read the spec file
2. Check "Domain Experts to Consult" section for explicit selections
3. Scan entire content for trigger keywords
4. Weight explicit selections higher than keyword matches

### Validation Rules

**Required Fields:**
- `name`: Must be unique, lowercase with hyphens
- `type`: Must be `domain_expert`, `orchestrator`, or `custom`
- `domain`: Descriptive string
- `description`: Clear purpose statement
- `enabled`: Boolean
- `priority`: `high`, `medium`, or `low`
- `model`: `opus` or `sonnet`
- `tools`: Array of valid tools
- `triggers`: Non-empty array of strings

**Valid Tools:**
- Read
- Write
- Edit
- Grep
- Glob
- Bash
- WebSearch
- WebFetch
- Agent
- (Any other official Claude Code tools)

### Error Handling

If `agents.yaml` is malformed:
- Report YAML syntax error
- Provide line number if possible
- Suggest validation with YAML linter

If required fields are missing:
- List all missing fields
- Indicate which agent has the issue
- Suggest fix based on template

If file references are broken:
- List all broken references
- Suggest creating missing files or fixing paths
- Provide template reference

---

## Usage Examples

### Example 1: Quick Status Check
```
User: "Are all agents working?"

Helper:
1. Reads agents.yaml
2. Validates all configurations
3. Returns health summary

Output: "All 7 agents healthy and operational"
```

### Example 2: Spec Analysis
```
User: "Which agents should review specs/add-auth.md?"

Helper:
1. Reads design spec
2. Finds keywords: "authentication", "rbac", "security", "go"
3. Matches triggers
4. Suggests: security-expert (if exists), go-expert, k8s-expert

Output: Detailed suggestion with reasoning
```

### Example 3: Adding New Agent
```
User: "I added a new agent but it's not showing up"

Helper:
1. Runs discovery scan
2. Finds new agent file
3. Checks if registered in agents.yaml
4. Reports: "Found security-expert.md but not in registry"
5. Suggests: "Add to config/agents.yaml under domain_experts"
```

---

## Future Enhancements

### Planned Features
- Auto-register discovered agents (with user confirmation)
- Suggest agent creation based on design spec gaps
- Track agent usage metrics
- Recommend agent archival for unused agents
- Detect trigger conflicts automatically
- Generate agent definitions from natural language descriptions

### Integration with skill-creator
Once integrated with skill-creator:
- Generate new agents on-demand
- Optimize agent prompts based on usage
- Run evals on agent quality
- Benchmark agent performance

---

## Maintenance

### When to Use
- Before running design spec analysis (validate config)
- After adding new agents (discover and validate)
- During quarterly reviews (generate report)
- When troubleshooting agent issues (check status)

### Keeping Updated
- Review validation rules quarterly
- Update for new Claude Code tools
- Enhance suggestion algorithm based on feedback
- Add new operations as needed

---

**Tool Version:** 1.0
**Last Updated:** 2026-03-25
**Maintainer:** Engineering Team
