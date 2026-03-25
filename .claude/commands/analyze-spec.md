Analyze a design spec using domain expert agents.

## Arguments

The user provided: $ARGUMENTS

## Instructions

1. **Parse the arguments.** Extract:
   - The spec file path (required, first argument)
   - Options (optional): `--fast`, `--smart`, `--focus=<areas>`
   - If no path was provided, ask the user for one

2. **Validate the spec file exists.** Read the file at the given path. If it doesn't exist, tell the user and stop.

3. **Determine the analysis mode** from the options:
   - No options → **full** mode (all enabled agents)
   - `--fast` → **quick** mode (high-priority agents only)
   - `--smart` → **smart** mode (only agents whose triggers match the spec content)
   - `--focus=<areas>` → **focused** mode (only the specified agents)
     - Agent short names: `go`, `k8s`, `controller`, `crd`, `unit-test`, `e2e-test`, `coding`
     - These map to: `go-expert`, `k8s-expert`, `controller-expert`, `crd-expert`, `unit-test-expert`, `e2e-test-expert`, `coding-expert`

4. **Follow the orchestrator instructions** in `.claude/agents/orchestrator/design-spec-orchestrator.md` to:
   - Load the agent registry from `config/agents.yaml`
   - Select agents based on the analysis mode
   - Launch all selected agents in parallel using the Agent tool (all in one message)
   - Each agent should receive the full design spec and analyze it from their domain perspective
   - Synthesize all agent recommendations into a unified implementation plan
   - Present the plan to the user

## Examples

```
/analyze-spec design-specs/add-webhook-support.md
/analyze-spec design-specs/bug-fix.md --fast
/analyze-spec design-specs/api-change.md --focus=crd,controller
/analyze-spec design-specs/new-feature.md --smart
```
