# Design Spec: Add Multi-Tenant RBAC Support

**Author:** Test Spec
**Date:** 2026-03-25
**Status:** Draft

---

## Overview

Add multi-tenant RBAC support to the backup operator so that different teams can manage their own backups without accessing other teams' resources. Currently, anyone with access to the namespace can view and modify all Backup resources. We need to add a `BackupPolicy` CRD that defines per-team access rules, and a validating webhook (or CEL validation) that enforces them.

---

## Goals

- Define a `BackupPolicy` CRD for per-team access control
- Enforce policies so users can only manage backups matching their team label
- Integrate with Kubernetes RBAC (ClusterRoles, RoleBindings)
- Audit log all policy enforcement decisions
- Support policy inheritance (namespace-level defaults with resource-level overrides)

---

## Non-Goals

- Authentication (relies on existing cluster auth)
- Network policy enforcement
- Cross-cluster RBAC
- UI for policy management

---

## Proposed Changes

### API Changes

**New CRDs:**
- `BackupPolicy`: Defines who can manage which backups

**Example API:**
```yaml
apiVersion: backup.example.io/v1alpha1
kind: BackupPolicy
metadata:
  name: team-alpha-policy
  namespace: production
spec:
  teamSelector:
    matchLabels:
      team: alpha
  rules:
    - resources: ["backups", "scheduledbackups"]
      verbs: ["get", "list", "create", "update", "delete"]
      resourceSelector:
        matchLabels:
          team: alpha
    - resources: ["backups"]
      verbs: ["get", "list"]
      resourceSelector: {}  # Can view all backups
  enforcement: Enforce  # Enforce | Audit | Disabled
status:
  enforcedRules: 2
  lastAuditTime: "2026-03-25T10:00:00Z"
  conditions:
    - type: Ready
      status: "True"
```

**Validation:**
- CEL validation for `enforcement` enum: `self.enforcement in ['Enforce', 'Audit', 'Disabled']`
- CEL validation for verbs: `self.rules.all(r, r.verbs.all(v, v in ['get','list','create','update','delete','patch','watch']))`
- Immutable `teamSelector` after creation (use CEL with `oldSelf`)

**Modified CRDs:**
- `Backup`: Add required `team` label via CEL validation
- `ScheduledBackup`: Add required `team` label via CEL validation

### Controller Changes

**New Controllers:**
- `BackupPolicyController`: Watches BackupPolicy resources, maintains policy cache, generates ClusterRoles and RoleBindings

**Reconciliation Logic:**
1. Load BackupPolicy
2. Validate policy rules are well-formed
3. Generate ClusterRole with matching permissions
4. Generate RoleBinding for team subjects
5. Update policy status with enforced rule count
6. Log audit entry for policy changes

**Modified Controllers:**
- `BackupController`: Check BackupPolicy before mutations
- `ScheduledBackupController`: Check BackupPolicy before creating Backups

### Infrastructure/Deployment Changes

**RBAC Changes:**
- New ClusterRole: `backup-policy-manager` with BackupPolicy CRUD
- New ClusterRole: `backup-policy-viewer` with BackupPolicy read-only
- Controller needs permissions to create/manage ClusterRoles and RoleBindings

**Deployment Changes:**
- Add `--enable-rbac-enforcement` flag (default: false for safe rollout)
- Add audit log volume mount

---

## Testing Strategy

### Unit Tests

**Key Test Cases:**
1. Policy evaluation: team matches label selector
2. Policy evaluation: verb checking
3. Policy evaluation: resource selector matching
4. Policy inheritance: namespace defaults with overrides
5. ClusterRole generation from policy rules
6. RoleBinding generation for team subjects
7. CEL validation rules compile and evaluate correctly

### Integration/E2E Tests

**Test Scenarios:**
1. Create BackupPolicy, verify ClusterRole/RoleBinding created
2. Attempt backup creation without matching policy — should be denied
3. Attempt backup creation with matching policy — should succeed
4. Audit mode: allow but log violations
5. Update policy, verify RBAC resources updated
6. Delete policy, verify RBAC cleanup
7. Multi-team isolation: team A cannot modify team B's backups

---

## Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Policy enforcement breaks existing workflows | High | High | Ship with `Audit` mode first, `Enforce` after validation |
| RBAC resource sprawl (many generated roles) | Medium | Medium | Aggregate common permissions, add cleanup controller |
| CEL validation performance with complex policies | Low | Low | Benchmark CEL rules, set maxItems constraints |
| Policy bypass via direct API access | Medium | High | Use admission webhook as enforcement point |

---

## Open Questions

1. **Should policies be namespace-scoped or cluster-scoped?**
   - Namespace-scoped is simpler but requires per-namespace config
   - Cluster-scoped enables global policies but adds complexity

2. **How to handle policy conflicts?**
   - Most restrictive wins? Most permissive wins?
   - Need clear conflict resolution strategy

---

## Domain Experts to Consult

- [x] **Go expert** - Interface design for policy evaluation
- [x] **Kubernetes expert** - RBAC patterns, ClusterRole management, admission control
- [x] **Controller expert** - Policy controller design, caching strategies
- [x] **CRD expert** - CEL validation, API versioning, immutable fields
- [x] **Unit testing expert** - Testing policy evaluation logic
- [x] **E2E testing expert** - RBAC testing, multi-user test scenarios
- [x] **General coding expert** - Security patterns, audit logging

---

## Security Considerations

- Policy enforcement must be fail-closed (deny by default if policy evaluation errors)
- Audit logs must be tamper-resistant
- Controller service account should not have cluster-admin
- Test for privilege escalation via policy manipulation

---

## Success Criteria

- [ ] BackupPolicy CRD validated and accepted
- [ ] Policies correctly enforced (deny unauthorized access)
- [ ] Audit mode captures all violations
- [ ] Existing workflows work with policies disabled
- [ ] RBAC resources auto-generated and cleaned up
- [ ] No privilege escalation vectors identified
