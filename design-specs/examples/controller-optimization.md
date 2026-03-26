# Design Spec: Optimize Backup Controller Performance

**Author:** Test Spec
**Date:** 2026-03-25
**Status:** Draft

---

## Overview

The `BackupController` has performance issues at scale. With 500+ Backup resources, reconciliation takes over 10 seconds per resource due to unbounded list operations and redundant API calls. The controller also triggers unnecessary reconciliations from its own status updates, creating a feedback loop that increases API server load.

---

## Goals

- Reduce per-resource reconciliation time from 10s to under 1s
- Eliminate self-triggered reconciliation loops
- Support 2000+ Backup resources without degradation
- Reduce API server load by 50%

---

## Non-Goals

- Changing the Backup CRD schema
- Adding new controller functionality
- Horizontal scaling / sharding (future work)

---

## Proposed Changes

### Controller Changes

**Modified Controllers:**
- `BackupController`: Performance optimizations

**Optimizations:**

1. **Add field indexers for filtered listing**
```go
// Index Backups by phase for efficient filtered listing
mgr.GetFieldIndexer().IndexField(ctx, &v1.Backup{}, ".status.phase", func(obj client.Object) []string {
    backup := obj.(*v1.Backup)
    return []string{string(backup.Status.Phase)}
})

// Use indexed list instead of listing all + filtering
var activeBackups v1.BackupList
c.List(ctx, &activeBackups, client.MatchingFields{".status.phase": "Active"})
```

2. **Filter self-triggered reconciliations with predicates**
```go
ctrl.NewControllerManagedBy(mgr).
    For(&v1.Backup{}).
    WithEventFilter(predicate.Or(
        predicate.GenerationChangedPredicate{},
        predicate.LabelChangedPredicate{},
    )).
    Complete(r)
```

3. **Cache frequently accessed resources**
   - Use `mgr.GetCache()` instead of direct API calls
   - Add watches for dependent resources instead of polling

4. **Batch status updates**
   - Collect status changes during reconciliation
   - Apply single status update at end of reconcile loop
   - Use `StatusClient.Patch` instead of `StatusClient.Update` to reduce conflicts

**Error Handling:**
- Rate limit requeues with exponential backoff
- Use `controller.Options{MaxConcurrentReconciles: 5}` for parallelism

### Infrastructure/Deployment Changes

**Resource limit adjustments:**
- Increase controller memory limit from 128Mi to 256Mi (indexer memory)
- Add `--concurrent` flag to controller manager for configurable parallelism

---

## Testing Strategy

### Unit Tests

**Key Test Cases:**
1. Verify field indexer returns correct results
2. Verify predicate filters out status-only updates
3. Verify batch status update correctness
4. Benchmark reconciliation with 100/500/2000 resources

### Integration/E2E Tests

**Test Scenarios:**
1. Create 500 Backup resources, measure reconciliation time
2. Verify no reconciliation loop from status updates
3. Verify concurrent reconciliation correctness

---

## Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Predicate filter drops legitimate events | Medium | High | Integration test with all event types |
| Indexer memory overhead at scale | Low | Medium | Monitor memory, set appropriate limits |
| Batch status update loses intermediate state | Low | Medium | Log intermediate states for debugging |

---

## Domain Experts to Consult

- [x] **Go expert** - Profiling, benchmarking, concurrency patterns
- [x] **Kubernetes expert** - API server load, resource limits
- [x] **Controller expert** - Predicates, indexers, caching, reconcile patterns
- [ ] **CRD expert**
- [x] **Unit testing expert** - Benchmark tests
- [ ] **E2E testing expert**
- [x] **General coding expert** - Performance patterns, profiling approach

---

## Success Criteria

- [ ] Reconciliation time < 1s per resource (p95)
- [ ] No self-triggered reconciliation loops
- [ ] 2000 resources handled without degradation
- [ ] API server request rate reduced by 50%
