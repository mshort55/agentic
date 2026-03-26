# Design Spec: Fix Nil Pointer in Status Update

**Author:** Test Spec
**Date:** 2026-03-25
**Status:** Draft

---

## Overview

The reconciler in `internal/controller/backup_controller.go` crashes with a nil pointer dereference when a Backup resource has no `.status.conditions` initialized. This happens on the first reconciliation after the resource is created, before any status has been set.

The fix needs to safely initialize the conditions slice before appending to it.

---

## Goals

- Fix the nil pointer crash on first reconciliation
- Ensure all status update paths handle uninitialized conditions
- Add a regression test

---

## Non-Goals

- Refactoring the entire status update logic
- Adding new status conditions
- Changing the Backup CRD schema

---

## Proposed Changes

### Controller Changes

**Modified Controllers:**
- `BackupController`: Add nil check before appending to `status.conditions`

**Fix:**
```go
// Before (crashes)
backup.Status.Conditions = append(backup.Status.Conditions, newCondition)

// After (safe)
if backup.Status.Conditions == nil {
    backup.Status.Conditions = []metav1.Condition{}
}
meta.SetStatusCondition(&backup.Status.Conditions, newCondition)
```

---

## Testing Strategy

### Unit Tests

**Files to Create/Modify:**
- `internal/controller/backup_controller_test.go`: Add test for nil conditions

**Key Test Cases:**
1. Reconcile with nil conditions slice (regression test)
2. Reconcile with empty conditions slice
3. Reconcile with existing conditions

---

## Domain Experts to Consult

- [x] **Go expert** - Nil safety patterns, idiomatic error handling
- [ ] **Kubernetes expert**
- [ ] **Controller expert**
- [ ] **CRD expert**
- [x] **Unit testing expert** - Regression test design
- [ ] **E2E testing expert**
- [x] **General coding expert** - Code quality of the fix

---

## Success Criteria

- [ ] No nil pointer panic on first reconciliation
- [ ] Regression test passes
- [ ] Existing tests still pass
