Evaluate a domain expert agent by running it against a design spec and saving the raw output, or compare two previous eval results.

## Arguments

The user provided: $ARGUMENTS

## Instructions

This command has two modes: **run** and **compare**.

### Mode Detection

- If `--compare` is present → **compare mode**
- Otherwise → **run mode**

---

## Run Mode

```
/eval-agent <agent-name> <spec-filename>
```

### Step 1: Parse Arguments

Extract:
- **Agent name** (required) — e.g., `go-expert`, `k8s-expert`
- **Spec filename** (required) — a file in `specs/`

Strip any directory prefix from the spec filename — the file must be in `specs/`.

Validate the agent exists at `.claude/agents/domain-experts/<agent-name>.md`. If not found, list available agents from that directory.

Validate the spec exists at `specs/<filename>`. If not found, tell the user.

### Step 2: Run the Agent

Read the agent definition, the spec, and the prompt template at `.claude/agents/templates/agent-prompt-template.md`. Use the Agent tool to spawn the domain expert with the prompt template, replacing `{{DESIGN_SPEC_CONTENT}}` with the full design spec content.

### Step 3: Save Results

Generate a timestamp: `YYYY-MM-DD-HHMMSS`

Save the raw agent output to:
```
output/eval-results/<agent-name>/<timestamp>-<spec-slug>.md
```

The saved file should have a header:
```markdown
# Eval: <agent-name> on <spec-filename>
**Agent:** <agent-name>
**Spec:** <spec-filename>
**Date:** <timestamp>

---

<raw agent output>
```

### Step 4: Analyze and Present

Count:
- **Sections present** — check for: Summary, Key Findings, Recommendations, Risks & Concerns, Existing Patterns to Leverage, Questions for Clarification (6 total)
- **Recommendations by priority** — count High, Medium, Low priority items
- **Codebase references** — count mentions of specific file paths or pattern references
- **Code examples** — count code blocks in the output

Present:

```
Evaluated <agent-name> against <spec-filename>:

  Sections: X/6 complete [list any missing]
  Recommendations: X high, X medium, X low
  Codebase references: X
  Code examples: X

Saved to: output/eval-results/<agent-name>/<timestamp>-<spec-slug>.md
```

Flag concerns:
- Missing sections (agent didn't follow output format)
- Zero codebase references (agent may not be searching the codebase)
- Zero code examples (agent may not be providing actionable guidance)
- Very few recommendations (agent may lack depth for this spec)

---

## Compare Mode

```
/eval-agent --compare <file1> <file2>
```

### Step 1: Parse Arguments

Extract the two file paths from `output/eval-results/`. If paths are relative, look inside `output/eval-results/`.

Read both files. If either doesn't exist, tell the user and list available eval results for that agent.

### Step 2: Compare

For each file, count the same metrics (sections, recommendations, codebase references, code examples). Then show:

```
Comparing:
  A: <file1>
  B: <file2>

                     A       B       Change
  Sections:         X/6     X/6
  High priority:      X       X      +/-N
  Medium priority:    X       X      +/-N
  Low priority:       X       X      +/-N
  Codebase refs:      X       X      +/-N
  Code examples:      X       X      +/-N
```

Then summarize the content differences:
- New topics or recommendations that appeared in B but not A
- Topics or recommendations that were in A but dropped from B
- Shifted priorities (something moved from medium to high, etc.)
- Changes in specificity (more/fewer concrete file paths, code examples)

Present the comparison so the user can judge whether the changes improved output quality.

## Examples

```
/eval-agent go-expert my-feature.md
/eval-agent controller-expert api-change.md
/eval-agent --compare go-expert/2026-03-25-140000-my-feature.md go-expert/2026-03-26-150000-my-feature.md
```
