# Design Spec: Add ScheduledBackup CRD

**Author:** Test Spec
**Date:** 2026-03-25
**Status:** Draft

---

## Overview

Add a new `ScheduledBackup` Custom Resource that allows users to define cron-based backup schedules for their applications. The controller will create `Backup` resources at the scheduled times and manage retention policies (keeping the last N backups).

This builds on the existing `Backup` CRD and controller infrastructure.

---

## Goals

- Define a `ScheduledBackup` CRD with cron schedule, retention count, and target reference
- Implement a controller that creates `Backup` resources on schedule
- Support pause/resume of schedules
- Enforce retention by deleting old `Backup` resources
- Full unit and e2e test coverage

---

## Non-Goals

- Cross-namespace backup scheduling
- Backup verification or restore functionality
- Custom backup storage backends
- Webhook-based schedule validation (use CEL validation)

---

## Proposed Changes

### API Changes

**New CRDs:**
- `ScheduledBackup`: Defines a recurring backup schedule

**Example API:**
```yaml
apiVersion: backup.example.io/v1alpha1
kind: ScheduledBackup
metadata:
  name: daily-db-backup
  namespace: production
spec:
  schedule: "0 2 * * *"        # Daily at 2 AM
  timezone: "America/New_York"  # Optional, defaults to UTC
  retentionCount: 7             # Keep last 7 backups
  paused: false                 # Pause/resume toggle
  backupTemplate:
    spec:
      target:
        apiGroup: apps/v1
        kind: StatefulSet
        name: postgres
      storageClass: fast-ssd
status:
  lastScheduleTime: "2026-03-25T02:00:00Z"
  nextScheduleTime: "2026-03-26T02:00:00Z"
  activeBackups: 5
  conditions:
    - type: Ready
      status: "True"
    - type: Paused
      status: "False"
```

**Kubebuilder Markers:**
```go
// +kubebuilder:validation:Pattern=`^(\d+|\*)(\/\d+)?(\s+(\d+|\*)(\/\d+)?){4}$`
Schedule string `json:"schedule"`

// +kubebuilder:validation:Minimum=1
// +kubebuilder:validation:Maximum=100
// +kubebuilder:default=5
RetentionCount int32 `json:"retentionCount,omitempty"`

// +kubebuilder:default=false
Paused bool `json:"paused,omitempty"`
```

### Controller Changes

**New Controllers:**
- `ScheduledBackupController`: Manages scheduled backup lifecycle

**Reconciliation Logic:**
1. Check if schedule is paused — if paused, set Paused condition and return
2. Parse cron schedule and determine if a backup is due
3. If due, create a new `Backup` resource from the template with owner reference
4. Update `status.lastScheduleTime` and calculate `status.nextScheduleTime`
5. List owned `Backup` resources, sorted by creation time
6. If count exceeds `retentionCount`, delete the oldest backups
7. Update `status.activeBackups` count
8. Requeue at next schedule time

**Error Handling:**
- Invalid cron expression: Set `Ready=False` with reason `InvalidSchedule`
- Backup creation failure: Requeue with backoff, set condition `BackupFailed`
- Retention cleanup failure: Log error, don't block next backup cycle

### Infrastructure/Deployment Changes

**RBAC Changes:**
- Controller needs `create`, `list`, `delete` permissions on `Backup` resources
- Controller needs `get`, `list`, `watch`, `update`, `patch` on `ScheduledBackup`

---

## Testing Strategy

### Unit Tests

**Files to Create/Modify:**
- `internal/controller/scheduledbackup_controller_test.go`

**Key Test Cases:**
1. Create backup when schedule is due
2. Skip backup when paused
3. Enforce retention count (delete oldest)
4. Handle invalid cron expression
5. Set correct next schedule time
6. Owner references set correctly on created Backups

### Integration/E2E Tests

**Test Scenarios:**
1. Happy path: Create ScheduledBackup, wait for Backup to be created
2. Pause and resume: Verify no backups created while paused
3. Retention: Create more backups than retention count, verify oldest deleted
4. Update schedule: Change cron expression, verify next time updates
5. Delete ScheduledBackup: Verify owned Backups are garbage collected

---

## Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Clock skew causes missed schedules | Low | Medium | Use `RequeueAfter` with buffer time |
| Retention deletion removes in-progress backup | Medium | High | Check backup status before deleting |
| Cron parsing library inconsistency | Low | Low | Use well-tested `robfig/cron` library |

---

## Open Questions

1. **Should we support multiple backup targets per schedule?**
   - Adds complexity but may be useful
   - Start with single target, extend later

2. **How should timezone handling work with DST transitions?**
   - Cron libraries handle this differently
   - Need to pick consistent behavior

---

## Domain Experts to Consult

- [x] **Go expert** - Cron parsing, time handling, concurrency
- [x] **Kubernetes expert** - RBAC, owner references, garbage collection
- [x] **Controller expert** - Reconciliation loop design, requeue strategy
- [x] **CRD expert** - API design, validation markers, status subresource
- [x] **Unit testing expert** - Table-driven tests for schedule logic
- [x] **E2E testing expert** - Time-based testing strategies
- [x] **General coding expert** - Code organization, error handling patterns

---

## Success Criteria

- [ ] ScheduledBackup CRD passes validation
- [ ] Backups created on schedule
- [ ] Retention policy enforced
- [ ] Pause/resume works correctly
- [ ] Unit test coverage > 80%
- [ ] E2E tests pass reliably
