# Design Spec: Improve E2E Test Infrastructure

**Author:** Test Spec
**Date:** 2026-03-25
**Status:** Draft

---

## Overview

Our e2e test suite has reliability problems: 15% flake rate in CI, slow test execution (45 minutes for 120 tests), and poor test isolation causing cascading failures. This spec addresses the test infrastructure to make the suite faster, more reliable, and easier to maintain.

---

## Goals

- Reduce flake rate from 15% to under 2%
- Reduce total e2e execution time from 45 to under 20 minutes
- Improve test isolation so one failure doesn't cascade
- Add structured test reporting for CI dashboards
- Document testing patterns for the team

---

## Non-Goals

- Rewriting all existing tests (only modify flaky ones)
- Changing the test framework (staying with Ginkgo/Gomega)
- Adding new feature test coverage (separate effort)

---

## Proposed Changes

### Test Environment Setup

**Current problems:**
- Tests share a single namespace, causing resource name collisions
- No cleanup between test suites
- envtest binary not pinned, causing version drift

**Changes:**
- Each `Describe` block creates a unique namespace with `GenerateName`
- `AfterEach` cleanup using `DeferCleanup` for guaranteed resource deletion
- Pin envtest binary version in `Makefile` using `setup-envtest`
- Add `DownloadBinaryAssets` for CI reproducibility

### Flake Reduction

**Common flake patterns identified:**
1. Hardcoded `time.Sleep` instead of `Eventually`
2. Missing `Eventually` timeout tuning (defaults too short)
3. Race conditions in parallel test execution
4. Stale client cache reads after writes

**Fixes:**
- Replace all `time.Sleep` with `Eventually`/`Consistently`
- Set consistent timeouts: `Eventually(...).WithTimeout(30 * time.Second).WithPolling(250 * time.Millisecond)`
- Use `ObjectKeyFromObject` for cache-safe reads after creation
- Add `--race` flag to test execution

### Parallel Execution

**Current:** All tests run sequentially
**Proposed:** Enable Ginkgo parallel execution with process-level isolation

```go
// suite_test.go
func TestE2E(t *testing.T) {
    RegisterFailHandler(Fail)
    RunSpecs(t, "E2E Suite", Label("e2e"))
}

// Run with: ginkgo -p --label-filter="e2e" ./test/e2e/...
```

- Tests that can't run in parallel get `Serial` decorator
- Shared resources use unique names with `GinkgoParallelProcess()` suffix

### Test Reporting

- Add `--gojson-report=report.json` for machine-readable output
- Add `ReportEntries` for attaching debug info to failed tests
- Configure CI to upload reports as artifacts

---

## Testing Strategy

### Unit Tests

**Key Test Cases:**
1. Test helper functions (namespace creation, cleanup)
2. Test retry/polling utilities
3. Test report formatting

### Integration/E2E Tests

**Validation:**
1. Run full suite 10 times, measure flake rate
2. Compare execution time before/after parallelization
3. Verify no test interference across parallel processes
4. Validate JSON reports are parseable

---

## Domain Experts to Consult

- [ ] **Go expert**
- [ ] **Kubernetes expert**
- [ ] **Controller expert**
- [ ] **CRD expert**
- [x] **Unit testing expert** - Test helper design, race detection
- [x] **E2E testing expert** - Ginkgo parallel, flake patterns, envtest
- [x] **General coding expert** - Code quality of test infrastructure

---

## Success Criteria

- [ ] Flake rate < 2% over 50 CI runs
- [ ] Total execution time < 20 minutes
- [ ] JSON test reports generated in CI
- [ ] Testing patterns documented
