Create an implementation brief, generate an execution plan, and offer to start implementation.

## Arguments

The user provided: $ARGUMENTS

## Prerequisites

This command requires the **superpowers** plugin for plan generation and execution.

Check if superpowers is available by looking for the `writing-plans` skill. If it is not installed, stop and tell the user:

```
The superpowers plugin is required but not installed.

Install it with:  /plugin install superpowers@claude-plugins-official
Then reload with: /reload-plugins

After that, run /implement again.
```

If superpowers is installed, proceed.

## Instructions

This command is the single entry point from analysis to execution. It bridges the analysis system (domain expert agents) to the execution system (superpowers) in one flow: create the brief, invoke plan generation, and offer to execute.

### Step 1: Locate the Analysis Report

The input can be a file path to a saved analysis report (e.g., `output/analysis-reports/2026-03-26-143052-my-feature.md`).

If no path is provided, look in `output/analysis-reports/` for the most recent file. If the directory is empty, tell the user to run `/analyze-spec` first.

### Step 2: Create the Implementation Brief

Read the full analysis report. Identify:
- The goal and executive summary
- Which recommendations are Critical vs Important vs Nice to Have
- Where agents reached consensus (multiple agents agree)
- Key risks and their severity
- Constraints that must not be violated
- Open questions that need human decisions
- The file path of the analysis report (for linking)

Write the brief to `output/briefs/YYYY-MM-DD-HHMMSS-<feature-slug>.md` using this format:

```markdown
# <Feature Name> Implementation Brief

**Analysis Report:** <relative path to the analysis report file>
**Agents consulted:** <comma-separated list>
**Date:** <date>

## Goal

<1-3 sentences: what is being built and the core problem it solves>

## Architecture Summary

<2-3 paragraphs: the key architecture decisions only. Consensus items and resolved conflicts with rationale. This is a distillation, not a rewrite — for full context see the analysis report.>

## Constraints (Do Not Ignore)

<Numbered list of non-negotiable requirements from Critical recommendations. Each constraint includes the source agent(s) and a one-line rationale. These are the rules the implementer must follow.>

1. <Constraint> (consensus: <agent-names>) — <why>
2. <Constraint> (from: <agent-name>) — <why>

## Implementation Priorities

Ordered by domain expert priority tiers. Preserve agent attribution.

### Critical
- <recommendation with agent attribution>

### Important
- <recommendation with agent attribution>

### Nice to Have
- <recommendation with agent attribution>

## Key Risks

<Top risks with severity and source agent. Reference the analysis report for full mitigation details.>

1. **<Risk name>** (<severity>, from <agent>) — <one-line description>. See analysis report for mitigation.
2. **<Risk name>** (<severity>, from <agent>) — <one-line description>.

## Open Questions

<Unresolved decisions that need human input before implementation can proceed. Flag clearly.>

1. <Question> (from <agent>)

## For Full Details

The complete analysis report at `<path>` contains:
- Full reports from each domain expert agent
- Detailed risk analysis with mitigation strategies
- Existing codebase patterns with file paths
- Complete testing strategy (unit and e2e)
- All recommendations with full rationale

**When generating the execution plan, read the full analysis report for any component that needs implementation detail beyond what this brief provides.**
```

**The brief is additive, not reductive.** The analysis report contains the complete domain expert output. The brief adds prioritization and structure on top — it never replaces or compresses the analysis.

Before saving, verify:
- The analysis report file path is correct and the file exists
- Constraints are specific and actionable (not vague)
- Agent attribution is preserved on all recommendations and risks
- Priority tiers match the analysis report (don't re-rank)
- Open questions are genuinely unresolved
- The brief does NOT duplicate full sections from the analysis report — it references them

### Step 3: Check for Blockers

Review the Open Questions section of the brief. If there are questions that would block implementation:

1. Present them to the user clearly
2. Ask the user to resolve them before proceeding
3. Once resolved, update the brief with the decisions

If there are no blocking questions, or the user resolves them, proceed to Step 4.

### Step 4: Generate the Execution Plan

Tell the user you are now generating the execution plan using superpowers' `writing-plans` skill.

Use the writing-plans skill with the following context. Read both documents and provide their content as the spec input:

1. **The implementation brief** you just created — this gives writing-plans the prioritized constraints, architecture decisions, and risk boundaries
2. **The full analysis report** referenced in the brief — this gives writing-plans the complete domain expert recommendations, testing strategies, existing codebase patterns, and detailed rationale

Tell writing-plans to save the execution plan to `output/plans/`.

### Step 5: Offer Execution

After the execution plan is generated, present the user with their options:

```
Implementation brief: output/briefs/<file>.md
Analysis report:      <analysis-report-path>
Execution plan:       output/plans/<file>.md

Ready to execute. Options:
1. Start subagent-driven-development (recommended for multi-file changes)
2. Start executing-plans (for simpler, sequential execution)
3. Review the execution plan first before starting
```

When the user chooses:
- **Option 1**: Use superpowers' `subagent-driven-development` skill with the execution plan. This dispatches a fresh subagent per task with two-stage review.
- **Option 2**: Use superpowers' `executing-plans` skill with the execution plan. This executes tasks sequentially in the current session.
- **Option 3**: Show the execution plan content and wait for further instructions.

Wait for the user to choose before proceeding.

## What This Command Does NOT Do

- Replace or compress the analysis report (the brief references it)
- Write implementation code directly (that's the executor)
- Skip plan generation and jump to coding

## Examples

```
/implement output/analysis-reports/2026-03-26-143052-scheduled-backup.md
/implement
```
