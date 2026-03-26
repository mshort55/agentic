Analyze a design spec using domain expert agents.

## Arguments

The user provided: $ARGUMENTS

## Instructions

1. **Parse the arguments.** Extract:
   - The spec filename (required, first argument) — this is the name of a file in `specs/`
   - Options (optional): `--smart`, `--focus=<areas>`
   - If no filename was provided, ask the user for one
   - Strip any directory prefix — the file must be in `specs/`

2. **Validate the spec file exists.** Read `specs/<filename>`. If it doesn't exist, tell the user the file was not found and that all design specs must be placed in the `specs/` directory.

3. **Determine the analysis mode** from the options:
   - No options → **full** mode (all enabled agents)
   - `--smart` → **smart** mode (only agents whose triggers match the spec content)
   - `--focus=<areas>` → **focused** mode (only the specified agents)
     - Agent short names: `go`, `k8s`, `controller`, `crd`, `unit-test`, `e2e-test`, `coding`
     - These map to: `go-expert`, `k8s-expert`, `controller-expert`, `crd-expert`, `unit-test-expert`, `e2e-test-expert`, `coding-expert`

4. **Follow the orchestrator instructions** in `.claude/agents/orchestrator/design-spec-orchestrator.md` to:
   - Load the agent registry from `config/agents.yaml`
   - Select agents based on the analysis mode
   - Launch all selected agents in parallel using the Agent tool (all in one message)
   - Each agent should receive the full design spec and analyze it from their domain perspective
   - Synthesize all agent recommendations into a unified analysis report
   - Save the report to `output/analysis-reports/YYYY-MM-DD-HHMMSS-<feature-slug>.md`
   - Present the report to the user

## Examples

```
/analyze-spec add-webhook-support.md
/analyze-spec api-change.md --focus=crd,controller
/analyze-spec new-feature.md --smart
```
