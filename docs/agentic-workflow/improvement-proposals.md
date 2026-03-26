# Improvement Proposals

**Date:** 2026-03-26
**Status:** Draft — awaiting prioritization

---

## How the system actually works

This is a **prompt-only system** — there is no code. The entire system is Claude reading markdown files and following instructions. `agents.yaml` is not parsed by a program; Claude reads it as text and interprets the values. The orchestrator is a prompt, not a runtime.

This is a key insight because it means certain patterns borrowed from software systems (config files, registries, validation settings, timeouts) are **decorative** — Claude can't enforce a 120-second timeout or validate-on-load. It reads those values but has no mechanism to act on them.

---

## Options

### A. Remove config theater from `agents.yaml` — DONE

**What:** Strip `agents.yaml` down to what Claude can actually act on. Remove fields that have no effect:
- `agent_timeout`, `orchestration_timeout` (Claude can't enforce timeouts)
- `max_parallel_agents` (Claude launches what it launches)
- `parallel_execution: true` (always true — the orchestrator prompt says "launch in parallel")
- `validate_on_load`, `strict_mode` (no validation code exists)
- `synthesis_format` (the orchestrator prompt hardcodes the format)
- `continue_on_agent_failure`, `min_required_agents` (orchestrator prompt already says "continue if agent fails")
- `allow_dynamic_agents`, `auto_discover_agents` (no discovery code exists — the orchestrator just reads the YAML list)

**Keep:** The agent list (name, enabled, model, tools), `default_mode`, `agent_template_path`, `agent_directories`.

**Pros:**
- Honest — config matches reality
- Less confusion about what's configurable vs. aspirational
- Simpler file, easier to maintain

**Cons:**
- If you later build actual tooling (a CLI, a wrapper script), you'd re-add these fields
- Removes the "north star" documentation of desired behavior

---

### B. Eliminate `agents.yaml` entirely — make agents self-contained

**What:** Move all config into each agent's `.md` frontmatter. The orchestrator would discover agents by scanning `.claude/agents/domain-experts/*.md` and reading their frontmatter. No central registry file.

```yaml
---
name: go-expert
description: Go language and idioms expert
enabled: true
model: opus
tools: [Read, Grep, Glob, WebSearch, WebFetch]
triggers:
  - go
  - golang
  - "*.go files"
---
```

The orchestrator prompt would say: "Scan `.claude/agents/domain-experts/` for all `.md` files, read their frontmatter, and use enabled agents."

**Pros:**
- True single source of truth — one file per agent, everything in one place
- Adding an agent is literally just dropping a file in a directory — zero registration step
- Eliminates the entire "split ownership" complexity that caused most of the inconsistencies we've been fixing
- Removes `config/` directory entirely

**Cons:**
- No global view of all agents at a glance (you'd need to open each file or use the registry helper)
- Harder to do bulk operations (e.g., disable all agents, change all models)
- The orchestrator prompt becomes slightly more complex (scan + parse vs. read one file)
- Loses the `default_mode` and other orchestrator-level settings (would need a new home)

---

### C. Reduce documentation to 2 files

**What:** Currently there are 5 docs + CLAUDE.md + README with heavy overlap. Consolidate to:
1. **README.md** — Quick start, pipeline overview, extending (what exists today, mostly fine)
2. **CLAUDE.md** — Project structure, conventions, key files (what exists today, mostly fine)
3. **docs/agentic-workflow/guide.md** — Single guide merging user-guide, developer-guide, adding-new-agents, and agent-registry into one document with clear sections
4. **Delete**: architecture.md (fold the diagrams into the guide), agent-registry.md (just read agents.yaml or the agent files), user-guide.md, developer-guide.md, adding-new-agents.md

**Pros:**
- Dramatically less surface area to keep consistent (this was the source of nearly every bug we fixed)
- One place to look instead of five
- Easier for new users — no "which doc do I read?" question

**Cons:**
- One large file vs. several focused ones
- Loses the separation of audience (user vs. developer)
- Architecture diagrams may feel out of place in a combined guide

---

### D. Add a `/validate-spec` command

**What:** New slash command that checks a design spec for completeness *before* running the full analysis. Checks:
- All required sections present (Overview, Goals, Proposed Changes, Testing Strategy)
- "Domain Experts to Consult" checkboxes are filled in
- No empty placeholder text remaining from the template
- Warns if no testing strategy is defined
- Suggests which mode to use based on spec content

**Pros:**
- Cheap to build (just a prompt file)
- Catches incomplete specs before spending tokens on 7 parallel agents
- Good UX — fast feedback loop

**Cons:**
- Adds another command to maintain
- Users could just run `/analyze-spec` and let agents flag gaps (they already do)
- Risk of being annoying ("your spec is missing Section X") when users intentionally write minimal specs

---

### E. Make the system project-agnostic

**What:** Currently all 7 agents are Kubernetes/Go operator specific. Refactor to support different project profiles:
- Extract K8s-specific agents into a "profile" or "preset"
- Create a mechanism to swap agent sets (e.g., a `web-fullstack` profile with React, Node, SQL, CSS, API agents)
- Keep the orchestrator, commands, and pipeline generic

**Pros:**
- Makes the system reusable across completely different projects
- The orchestrator, commands, and pipeline are already generic — only agents need to change
- Could become a reusable open-source framework

**Cons:**
- Significant scope — need to design the profile mechanism, create new agent sets
- Current agents are very deep and specific; creating equivalent depth for other domains is a lot of work
- May be premature if the system is primarily used for one K8s operator project
- Adds complexity (profile selection, profile management) to a currently simple system

---

### F. Retire the `agent-registry-helper` tool

**What:** The registry helper (540 lines) describes 6 operations but is never invoked by any command, workflow, or other file. It's an orphaned document. Either:
- **F1:** Delete it entirely — the orchestrator and agents.yaml are simple enough to manage manually
- **F2:** Wire it into a `/registry` slash command so it's actually usable

**Pros (F1 — delete):**
- Removes 540 lines of content that needs maintenance but provides no value
- One less file to keep consistent (it was the source of many audit findings)

**Pros (F2 — wire up):**
- The operations it describes (list agents, validate config, discover agents) are genuinely useful
- Small effort — just create `.claude/commands/registry.md` that invokes it

**Cons (F1):** Loses the validation/discovery logic if you need it later
**Cons (F2):** Another command to maintain; the operations it describes are simple enough to do manually

---

### G. Simplify the `domain` field duplication

**What:** In `agents.yaml`, `domain` (e.g., "Go language and idioms") duplicates `description` in the agent's `.md` file (e.g., "Go language and idioms expert agent for design spec analysis"). They serve the same purpose — telling the orchestrator what the agent does. Remove `domain` from agents.yaml and have the orchestrator read descriptions from the `.md` frontmatter.

**Pros:**
- Eliminates one more source of truth split
- agents.yaml entries shrink to just: name, type, enabled, model, tools

**Cons:**
- Orchestrator needs to read each agent's `.md` file to get descriptions (slightly more work per run)
- Minor — easy to implement

---

## Recommendation

If prioritizing: **A + F2 + G** are low-effort, high-value cleanups. **C** is high-value but more work. **B** is the boldest simplification and could be combined with A+G into one move. **D** is a nice quick feature. **E** is a big vision change — only worth it if you plan to use this system beyond K8s.
