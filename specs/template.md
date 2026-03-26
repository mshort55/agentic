# Design Spec: [Feature Name]

**Author:** [Your Name]
**Date:** [YYYY-MM-DD]
**Status:** [Draft | In Review | Approved | Implemented]

---

## Overview

Brief description of the feature or change. What problem does this solve? Why is this needed?

*(2-3 paragraphs maximum)*

---

## Goals

What are we trying to achieve with this implementation?

- Goal 1: Specific, measurable outcome
- Goal 2: Specific, measurable outcome
- Goal 3: Specific, measurable outcome

---

## Non-Goals

What is explicitly out of scope for this implementation?

- Non-goal 1: What we're NOT doing
- Non-goal 2: What we're NOT doing
- Non-goal 3: What we're NOT doing

---

## Proposed Changes

### API Changes

Describe any Custom Resource Definition (CRD) or API modifications.

**New CRDs:**
- `ResourceName`: Description of the resource

**Modified CRDs:**
- `ExistingResource`: What fields/behaviors are changing

**Example API:**
```yaml
apiVersion: example.io/v1
kind: MyResource
metadata:
  name: example
spec:
  # Spec fields
  fieldName: value
status:
  # Status fields
  conditions:
    - type: Ready
      status: "True"
```

### Controller Changes

Describe controller logic modifications.

**New Controllers:**
- `ControllerName`: What it reconciles and why

**Modified Controllers:**
- `ExistingController`: What reconciliation logic changes

**Reconciliation Logic:**
- Step 1: What happens
- Step 2: What happens
- Step 3: What happens

**Error Handling:**
- How errors are handled
- Retry strategy
- Status updates

### Infrastructure/Deployment Changes

Any changes to deployment, services, RBAC, etc.

**RBAC Changes:**
- New permissions required
- Role modifications

**Deployment Changes:**
- Resource limit adjustments
- New environment variables
- Configuration changes

### Other Components

List other affected areas:
- CLI changes
- Webhook changes
- Documentation updates
- Migration requirements

---

## Testing Strategy

### Unit Tests

What unit tests are needed?

**Files to Create/Modify:**
- `path/to/file_test.go`: What to test

**Key Test Cases:**
1. Test case 1: Description
2. Test case 2: Description
3. Test case 3: Description

**Coverage Target:** X%

### Integration/E2E Tests

What end-to-end tests are needed?

**Test Scenarios:**
1. Scenario 1: Happy path
2. Scenario 2: Error handling
3. Scenario 3: Edge cases

**Test Environment:**
- Requirements for test environment
- Test data needs

**Ginkgo Test Structure:**
```go
Describe("Feature Name", func() {
    Context("When condition", func() {
        It("should do something", func() {
            // Test implementation
        })
    })
})
```

---

## Implementation Plan

High-level phases/steps for implementation.

### Phase 1: [Phase Name]
**Estimated Effort:** Small | Medium | Large

**Tasks:**
1. Task 1
2. Task 2
3. Task 3

**Files Modified:**
- `path/to/file1.go`
- `path/to/file2.go`

### Phase 2: [Phase Name]
*(Same structure as Phase 1)*

### Phase 3: [Phase Name]
*(Same structure as Phase 1)*

---

## Dependencies

**Upstream Dependencies:**
- Dependency 1: Why needed
- Dependency 2: Why needed

**Downstream Impact:**
- System/Team 1: How they're affected
- System/Team 2: How they're affected

---

## Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Risk 1 description | High/Med/Low | High/Med/Low | How we'll mitigate |
| Risk 2 description | High/Med/Low | High/Med/Low | How we'll mitigate |

---

## Open Questions

Questions that need answers before or during implementation:

1. **Question 1?**
   - Context and why it matters
   - Who should answer this

2. **Question 2?**
   - Context and why it matters
   - Who should answer this

---

## Domain Experts to Consult

<!-- This section helps the orchestrator determine which domain expert agents to invoke -->
<!-- Check the boxes for areas relevant to this design spec -->

- [ ] **Go expert** - Go code changes, patterns, idioms
- [ ] **Kubernetes expert** - K8s resources, deployments, services
- [ ] **Controller expert** - Reconciliation logic, controller-runtime
- [ ] **CRD expert** - API design, validation, versioning
- [ ] **Unit testing expert** - Unit test strategy and implementation
- [ ] **E2E testing expert** - Integration and end-to-end tests
- [ ] **General coding expert** - Code quality, organization, refactoring

**Additional Notes for Experts:**
- Specific concerns or focus areas for domain consultation
- Known constraints or requirements

---

## Success Criteria

How do we know this implementation is successful?

- [ ] Criterion 1: Measurable success indicator
- [ ] Criterion 2: Measurable success indicator
- [ ] Criterion 3: Measurable success indicator

---

## References

- [Related Design Doc](link)
- [Issue/Ticket](link)
- [External Documentation](link)

---

## Changelog

| Date | Author | Change |
|------|--------|--------|
| YYYY-MM-DD | Name | Initial draft |
| YYYY-MM-DD | Name | Updated after review |

---

## Appendix

### Alternative Approaches Considered

**Approach 1:**
- Description
- Pros
- Cons
- Why not chosen

**Approach 2:**
- Description
- Pros
- Cons
- Why not chosen

### Performance Considerations

- Expected performance characteristics
- Scalability limits
- Resource usage

### Security Considerations

- Authentication/authorization changes
- Secrets management
- RBAC implications
- Security review requirements
