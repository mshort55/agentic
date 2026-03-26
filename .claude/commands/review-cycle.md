Review a completed implementation cycle and propose improvements to domain expert agent prompts.

## Arguments

The user provided: $ARGUMENTS

## Instructions

This command runs after an implementation is complete. It compares what the analysis report recommended against what was actually implemented, captures lessons learned, and proposes concrete edits to agent files.

### Step 1: Parse Arguments

Extract:
- **Analysis report path** (required) — a file in `output/analysis-reports/`
- **Commit range or branch** (required) — the git diff to compare against (e.g., `main..feature-branch`, `abc123..def456`, or a branch name)

If either is missing, ask the user.

Validate the analysis report exists. Validate the commit range produces a diff (run `git diff --stat <range>` to check).

### Step 2: Load Context

Read:
1. The full analysis report
2. The git diff (`git diff <range>`) to see what was actually implemented
3. The git log (`git log --oneline <range>`) to see commit messages

### Step 3: Map Recommendations to Changes

Analyze the analysis report's recommendations (Critical, Important, Nice to Have) and compare against the actual code changes. Categorize each recommendation:

- **Followed** — the code changes clearly implement this recommendation
- **Partially followed** — some aspects implemented, others not
- **Skipped** — no evidence of this recommendation in the code changes
- **Can't determine** — not enough information to tell

Also identify:
- **Unpredicted changes** — significant code changes that no agent recommended (new files, architectural decisions, patterns not mentioned in the report)

### Step 4: Ask Structured Questions

Present the mapping to the user, then ask these questions. Wait for answers before proceeding.

```
## Recommendations Review

### Followed
- [list followed recommendations with agent attribution]

### Skipped
- [list skipped recommendations with agent attribution]

### Unpredicted Changes
- [list significant changes not covered by any recommendation]

## Questions

1. For each skipped recommendation: Was it skipped because it was **wrong**, **irrelevant to this case**, or **deferred to later**?

2. For each unpredicted change: Should an agent have caught this? If so, which domain?

3. Which agent provided the **most valuable** recommendations? What made them useful?

4. Which agent provided the **least valuable** recommendations? What was the problem — too generic, wrong advice, missing context?

5. Were there any recurring patterns or conventions in the codebase that agents should know about for future analysis?
```

### Step 5: Generate Improvement Proposals

Based on the user's answers, generate specific, actionable proposals for agent prompt edits. Each proposal should be:

```markdown
### Proposal N: <title>

**Agent:** <agent-name>
**File:** .claude/agents/domain-experts/<agent-name>.md
**Section:** <which section of the agent prompt to edit>
**Action:** Add / Remove / Modify

**What to change:**
<specific text to add, remove, or modify in the agent's prompt>

**Why:**
<based on which review finding — e.g., "Agent missed X pattern that appeared in the implementation">
```

Types of proposals to generate:
- **Add missing knowledge** — agent missed a pattern or convention that should be in its expertise
- **Fix wrong advice** — agent recommended something that was incorrect for this codebase
- **Add trigger** — agent should have been activated but wasn't (smart mode)
- **Improve specificity** — agent gave generic advice where codebase-specific guidance would help
- **Update best practices** — new patterns emerged that should be codified

Do NOT propose changes for:
- Recommendations that were skipped because they were deferred (not wrong, just not now)
- Stylistic preferences that don't affect correctness
- One-off situations unlikely to recur

### Step 6: Apply Proposals

Present all proposals to the user. For each one, ask: **Apply, Skip, or Modify?**

- **Apply** — make the edit to the agent file immediately
- **Skip** — don't apply this proposal
- **Modify** — user provides adjusted text, then apply

After applying, show a summary of what was changed.

### Step 7: Save Review

Save the review to `output/review-cycles/YYYY-MM-DD-HHMMSS-<feature-slug>.md` with:

```markdown
# Review: <feature name>

**Analysis Report:** <path>
**Commit Range:** <range>
**Date:** <date>

## Recommendations Mapping
[followed/skipped/unpredicted from Step 3]

## User Feedback
[answers from Step 4]

## Proposals
[all proposals with their status: applied/skipped/modified]

## Changes Made
[list of agent files modified and what was changed]
```

Tell the user:
```
Review saved to: output/review-cycles/<filename>.md

Tip: Run /eval-agent on modified agents to verify the changes improved output quality.
```

## Examples

```
/review-cycle output/analysis-reports/2026-03-26-143052-scheduled-backup.md main..feature/scheduled-backup
/review-cycle output/analysis-reports/2026-03-27-091500-api-change.md abc123..def456
```
