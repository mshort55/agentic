# Terminology Update: "Practice Agents" → "Domain Experts"

**Date:** 2026-03-25
**Reason:** "Practice agents" was ambiguous; "domain experts" better describes their role as specialized consultants in specific domains.

---

## Changes Made

### 1. Configuration (`config/agents.yaml`)

**Before:**
- `practice_agents:` → **After:** `domain_experts:`
- `type: practice` → **After:** `type: domain_expert`
- "practice expert agents" → **After:** "domain expert agents"
- "practice agents" → **After:** "domain experts"
- Template path: `practice-agent-template.md` → **After:** `domain-expert-template.md`
- Agent directory: `.claude/agents/practices` → **After:** `.claude/agents/domain-experts`
- Validation: `require_practice_docs` → **After:** `require_domain_docs`
- Docs path: `docs/practices/` → **After:** `docs/domain-expertise/`
- Section: `practice_docs:` → **After:** `domain_docs:`

### 2. Directory Structure

**Renamed:**
- `.claude/agents/practices/` → `.claude/agents/domain-experts/`
- `docs/practices/` → `docs/domain-expertise/`

### 3. Documentation (`README.md`)

**Updated:**
- "practice expert agents" → "domain expert agents"
- "Practice expert agent definitions" → "Domain expert agent definitions"
- "Practice area documentation" → "Domain expertise documentation"
- "7 Practice Experts" → "7 Domain Experts"
- "7 practice experts" → "7 domain experts"
- "practice-agent-template.md" → "domain-expert-template.md"
- "practice documentation template" → "domain expertise documentation template"
- "Create Initial Practice Agents" → "Create Initial Domain Expert Agents"
- "practice expert agents" → "domain expert agents"
- "Adding new practice areas" → "Adding new domain experts"
- "Create practice documentation" → "Create domain expertise documentation"

### 4. Design Spec Template (`design-specs/template.md`)

**Updated:**
- "Practice Areas to Consult" → "Domain Experts to Consult"
- "practice agents" → "domain expert agents"
- "Go practices" → "Go expert"
- "Kubernetes practices" → "Kubernetes expert"
- "Controller practices" → "Controller expert"
- "CRD practices" → "CRD expert"
- "Unit testing practices" → "Unit testing expert"
- "E2E testing practices" → "E2E testing expert"
- "General coding practices" → "General coding expert"
- "Additional Notes for Agents" → "Additional Notes for Experts"
- "practice consultation" → "domain consultation"

---

## Terminology Mapping

| Old Term | New Term | Rationale |
|----------|----------|-----------|
| practice_agents | domain_experts | More accurate - they provide domain expertise |
| practice agent | domain expert agent | Clearer role description |
| type: practice | type: domain_expert | Matches new naming |
| Practice Areas | Domain Experts | Focus on expertise not "practices" |
| Go practices | Go expert | Emphasizes expert knowledge |
| practice consultation | domain consultation | Clearer what's being consulted |
| practices/ directory | domain-experts/ directory | Matches agent type |
| docs/practices/ | docs/domain-expertise/ | More descriptive |

---

## Why "Domain Expert" is Better

1. **Clarity**: "Domain expert" clearly indicates specialized knowledge in a specific area
2. **Accuracy**: These agents ARE experts in their domains (Go, K8s, Controllers, etc.)
3. **Extensibility**: Any new domain (security, observability, performance) fits naturally
4. **Industry Standard**: Aligns with "subject matter expert" (SME) terminology
5. **Avoids Ambiguity**: "Practice" could mean practicing/rehearsal vs. best practices

---

## Impact

### Files Modified
- ✅ `config/agents.yaml`
- ✅ `README.md`
- ✅ `design-specs/template.md`

### Directories Renamed
- ✅ `.claude/agents/practices` → `.claude/agents/domain-experts`
- ✅ `docs/practices` → `docs/domain-expertise`

### Files to Update in Future Phases
- [ ] `.claude/agents/templates/domain-expert-template.md` (Phase 1 - will be created with new name)
- [ ] `docs/domain-expertise/*.md` (Phase 2 - will be created in new location)
- [ ] `docs/agentic-workflow/adding-new-agents.md` (Phase 4 - will reference domain experts)
- [ ] Architecture documentation (if any references exist)

---

## Backward Compatibility

**None needed** - Phase 0 just completed, no existing implementation to migrate.

---

**Status:** ✅ Complete
**All terminology updated consistently across the project.**
