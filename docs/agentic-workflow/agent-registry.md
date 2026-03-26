# Agent Registry

Catalog of all domain expert agents in the system. For the raw configuration, see `config/agents.yaml`.

---

## Domain Expert Agents

### go-expert

| | |
|---|---|
| **Domain** | Go language and idioms |
| **Priority** | High |
| **Model** | Opus |
| **Tools** | Read, Grep, Glob, WebSearch |

**Expertise:** Idiomatic patterns, error handling (`errors.Is`/`errors.As`, wrapping), concurrency (goroutines, channels, sync, context), project structure (`internal/`, `pkg/`, `cmd/`), testing (table-driven, testify, gomock, benchmarks, `synctest`), performance and memory management.

**Triggers:** `go`, `golang`, `*.go files`, `goroutines`, `channels`, `interfaces`

**When to use:** Any design spec involving Go code changes, new packages, concurrency concerns, or performance work.

---

### k8s-expert

| | |
|---|---|
| **Domain** | Kubernetes patterns and best practices |
| **Priority** | High |
| **Model** | Opus |
| **Tools** | Read, Grep, Glob, WebSearch, Bash |

**Expertise:** Resource patterns and manifests, deployment strategies, service discovery and networking, ConfigMaps and Secrets, RBAC and security, resource limits and requests, health checks (liveness, readiness, startup probes), labels, selectors, namespace organization.

**Triggers:** `kubernetes`, `k8s`, `deployment`, `service`, `configmap`, `secret`, `rbac`

**When to use:** Design specs involving Kubernetes resources, RBAC, networking, deployment configuration, or infrastructure changes.

---

### controller-expert

| | |
|---|---|
| **Domain** | Kubernetes controller patterns |
| **Priority** | High |
| **Model** | Opus |
| **Tools** | Read, Grep, Glob, WebSearch |

**Expertise:** controller-runtime patterns, reconciliation loop design, event filtering and predicates, error handling and retry logic, leader election, finalizers and garbage collection, status conditions, watches and indexers, owner references, performance and scalability.

**Triggers:** `controller`, `reconcile`, `controller-runtime`, `operator`, `watch`

**When to use:** Design specs involving controller logic, reconciliation loops, operator patterns, or controller performance.

---

### crd-expert

| | |
|---|---|
| **Domain** | Custom Resource Definitions |
| **Priority** | Medium |
| **Model** | Opus |
| **Tools** | Read, Grep, Glob, WebSearch |

**Expertise:** API design and versioning, kubebuilder markers and validation, OpenAPI schema design, CEL validation (GA in K8s 1.29), validation ratcheting, status subresource, printer columns, webhook validation, conversion webhooks, backward compatibility.

**Triggers:** `crd`, `custom resource`, `api`, `kubebuilder`, `webhook`

**When to use:** Design specs involving CRD/API changes, validation rules, API versioning, or webhook logic.

---

### unit-test-expert

| | |
|---|---|
| **Domain** | Unit testing patterns and practices |
| **Priority** | High |
| **Model** | Sonnet |
| **Tools** | Read, Grep, Glob, WebSearch |

**Expertise:** Table-driven tests, test organization and structure, mocking strategies (gomock, interfaces), test helpers and utilities, coverage analysis, test naming conventions, parallel test execution, benchmark testing (`b.Loop()`), `synctest` for time-dependent code, race detection.

**Triggers:** `unit test`, `_test.go`, `testing`, `mock`, `testify`

**When to use:** Any design spec that needs unit tests, or when evaluating test strategy.

---

### e2e-test-expert

| | |
|---|---|
| **Domain** | End-to-end testing with Ginkgo |
| **Priority** | Medium |
| **Model** | Sonnet |
| **Tools** | Read, Grep, Glob, WebSearch, Bash |

**Expertise:** Ginkgo v2 BDD testing, Gomega matchers, test environment setup (envtest, kind), test isolation and cleanup (`DeferCleanup`), flake reduction, parallel execution strategies, `DescribeTableSubtree`, `ReportEntries`, `SpecPriority`, `--gojson-report`, CI/CD integration.

**Triggers:** `e2e`, `ginkgo`, `integration test`, `gomega`

**When to use:** Design specs needing integration or e2e tests, or when improving test infrastructure.

---

### coding-expert

| | |
|---|---|
| **Domain** | General coding best practices |
| **Priority** | Medium |
| **Model** | Sonnet |
| **Tools** | Read, Grep, Glob |

**Expertise:** SOLID principles, DRY/KISS/YAGNI, code organization and modularity, naming conventions, documentation practices, refactoring patterns, technical debt management, security best practices, structured logging (`log/slog`), error handling patterns.

**Triggers:** `code quality`, `clean code`, `refactoring`, `solid`, `code organization`

**When to use:** General code quality concerns, refactoring, or as a complement to domain-specific agents.

---

## Orchestrator

### design-spec-orchestrator

| | |
|---|---|
| **Type** | Orchestrator |
| **Model** | Opus |
| **Location** | `.claude/agents/orchestrator/design-spec-orchestrator.md` |

Reads design specs, selects relevant agents, launches them in parallel, synthesizes their recommendations, resolves conflicts, and generates implementation plans.

Invoked via `/analyze-spec` command (`.claude/commands/analyze-spec.md`).

---

## Statistics

| Metric | Value |
|--------|-------|
| Total domain experts | 7 |
| High priority | 4 (go, k8s, controller, unit-test) |
| Medium priority | 3 (crd, e2e-test, coding) |
| Using Opus | 4 (go, k8s, controller, crd) |
| Using Sonnet | 3 (unit-test, e2e-test, coding) |
| Total unique triggers | 30 |

## Related Documents

- [User Guide](user-guide.md) — How to use the system
- [Developer Guide](developer-guide.md) — How the system works internally
- [Adding New Agents](adding-new-agents.md) — How to create new domain experts
- [Architecture](architecture.md) — System design and diagrams
