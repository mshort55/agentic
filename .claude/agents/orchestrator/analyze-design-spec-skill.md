---
name: analyze-design-spec
description: User-facing skill that invokes the design spec orchestrator to analyze a design spec using domain expert agents
type: skill
triggers:
  - "/analyze-spec"
  - "/spec-analysis"
---

# Analyze Design Spec

User-friendly interface to the design spec orchestrator.

## Usage

```
/analyze-spec <path-to-spec> [options]
```

## Options

- `--full`: Comprehensive analysis with all enabled agents (default)
- `--fast`: Quick analysis with high-priority agents only
- `--focus=<areas>`: Focus on specific domain areas
  - Areas: `go`, `k8s`, `controller`, `crd`, `unit-test`, `e2e-test`, `coding`
  - Multiple areas: `--focus=go,unit-test,coding`
- `--smart`: Only invoke agents whose triggers match the spec content

## Examples

```bash
# Full analysis (all agents)
/analyze-spec design-specs/add-new-feature.md

# Quick analysis (high-priority agents only)
/analyze-spec design-specs/bug-fix.md --fast

# Focus on testing
/analyze-spec design-specs/add-feature.md --focus=unit-test,e2e-test

# Focus on Kubernetes and controllers
/analyze-spec design-specs/operator-enhancement.md --focus=k8s,controller

# Smart mode (auto-detect relevant agents)
/analyze-spec design-specs/api-change.md --smart
```

## Implementation

When invoked, this skill:

1. **Parse arguments**: Extract the spec path and any options
2. **Validate spec exists**: Check that the design spec file exists at the given path
3. **Determine mode**: Map options to orchestrator mode:
   - No options or `--full` → mode: full
   - `--fast` → mode: quick
   - `--focus=<areas>` → mode: focused, with specified agents
   - `--smart` → mode: smart
4. **Invoke orchestrator**: Call the design-spec-orchestrator with the spec path and determined mode
5. **Present results**: The orchestrator handles synthesis and output

## Agent Name Mapping

When using `--focus`, these short names map to agents:

| Short Name    | Agent              |
|---------------|--------------------|
| `go`          | go-expert          |
| `k8s`         | k8s-expert         |
| `controller`  | controller-expert  |
| `crd`         | crd-expert         |
| `unit-test`   | unit-test-expert   |
| `e2e-test`    | e2e-test-expert    |
| `coding`      | coding-expert      |

## Error Handling

- **Missing spec path**: Prompt user for the path
- **Spec file not found**: Report error with suggestion to check the path
- **Invalid option**: Show usage help
- **No agents available**: Report that no agents are enabled in `config/agents.yaml`
