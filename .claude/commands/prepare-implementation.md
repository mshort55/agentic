Create an implementation brief that orients superpowers toward the analysis report.

## Arguments

The user provided: $ARGUMENTS

## Instructions

This command bridges the analysis system (domain expert agents) to the execution system (superpowers). It produces a thin routing document — an implementation brief — that tells superpowers' `writing-plans` skill what to prioritize and where to find the full details.

**The brief is additive, not reductive.** The analysis report contains the complete domain expert output. The brief adds prioritization and structure on top of it — it never replaces or compresses the analysis. `writing-plans` reads the brief for orientation, then digs into the full analysis report when it needs detail.

### Step 1: Locate the Analysis Report

The input is a file path to a saved analysis report (e.g., `analysis-reports/2026-03-26-143052-my-feature.md`).

If no path is provided, look in `analysis-reports/` for the most recent file. If the directory is empty, ask the user to run `/analyze-spec` first.

### Step 2: Read and Understand the Analysis Report

Read the full analysis report. Identify:
- The goal and executive summary
- Which recommendations are Critical vs Important vs Nice to Have
- Where agents reached consensus (multiple agents agree)
- Key risks and their severity
- Constraints that must not be violated
- Open questions that need human decisions
- The file path of the analysis report (for linking)

### Step 3: Generate the Implementation Brief

Write the brief to `docs/superpowers/briefs/YYYY-MM-DD-HHMMSS-<feature-slug>.md` using this format:

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

1. **<Risk name>** (<severity>, from <agent>) — <one-line description>. See analysis report §Risks for mitigation.
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

### Step 4: Quality Check

Before saving, verify:
- The analysis report file path is correct and the file exists
- Constraints are specific and actionable (not vague)
- Agent attribution is preserved on all recommendations and risks
- Priority tiers match the analysis report (don't re-rank)
- Open questions are genuinely unresolved
- The brief does NOT duplicate full sections from the analysis report — it references them
- Nothing from the analysis report is lost, because the brief points back to it

### Step 5: Present to User

After writing the brief:
1. Show the file path and a brief summary
2. Flag any open questions that need answers before proceeding
3. Tell the user their next step:

```
Implementation brief: docs/superpowers/briefs/<file>.md
Analysis report:      <analysis-report-path>

Next steps:
1. Review the brief and resolve any open questions
2. Use superpowers' `writing-plans` skill to generate the execution plan
   (writing-plans should read both the brief AND the full analysis report)
3. Execute via `subagent-driven-development` or `executing-plans`
```

## What This Command Does NOT Do

- Replace or compress the analysis report (the brief references it)
- Generate task-level execution plans (that's `writing-plans`)
- Write implementation code (that's the implementer subagent)
- Decompose into 2-5 minute tasks (that's `writing-plans`)

This command's value is adding a prioritized routing layer on top of the analysis — helping `writing-plans` navigate the depth without losing any of it.

## Examples

```
/prepare-implementation analysis-reports/2026-03-26-143052-scheduled-backup.md
/prepare-implementation
```
