# Engineering Skills Repository

A modular, retrieval-optimized knowledge base for AI coding agents building production-quality Proofs of Concept.

## Skill Types

| Prefix | Type | Purpose |
|--------|------|---------|
| `knowledge-` | Knowledge | Principles, decision frameworks, mental models |
| `execution-` | Execution | Repeatable procedures, checklists, step-by-step workflows |
| `review-` | Reviewer | Evaluate work from a specific perspective, identify risks |

## Domain Map

### Core Principles (`principles/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-solid | Knowledge | SOLID principles and when to apply each |
| knowledge-dry-kiss-yagni | Knowledge | DRY, KISS, YAGNI trade-offs and decision framework |
| knowledge-separation-of-concerns | Knowledge | Boundaries, cohesion, coupling |
| knowledge-fail-fast | Knowledge | Fail-fast philosophy and error propagation |
| knowledge-defensive-programming | Knowledge | Input validation, invariants, assertions |

### Architecture (`architecture/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-clean-architecture | Knowledge | Dependency rule, layers, boundaries |
| knowledge-hexagonal-architecture | Knowledge | Ports and adapters, testability, pluggability |
| knowledge-ddd | Knowledge | Bounded contexts, aggregates, ubiquitous language |
| knowledge-design-patterns | Knowledge | GoF patterns, when to use, when to avoid |
| knowledge-event-driven | Knowledge | Event sourcing, CQRS, messaging patterns |
| execution-service-design | Execution | Design a new service from requirements to interfaces |

### Security (`security/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-owasp-top-10 | Knowledge | Top 10 vulnerabilities and mitigations |
| knowledge-secure-by-design | Knowledge | Security as architecture, not afterthought |
| knowledge-auth-patterns | Knowledge | Authentication and authorization patterns |
| knowledge-privacy-by-design | Knowledge | Data minimization, consent, GDPR/CCPA principles |
| execution-threat-modeling | Execution | STRIDE-based threat modeling workflow |

### API Design (`api/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-rest-principles | Knowledge | REST constraints, resource modeling, HATEOAS |
| knowledge-api-versioning | Knowledge | Versioning strategies and trade-offs |
| execution-rest-api-design | Execution | Design a REST API from requirements to OpenAPI spec |
| execution-graphql-schema | Execution | Design a GraphQL schema with best practices |
| execution-api-documentation | Execution | Write effective API documentation |

### Testing (`testing/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-test-strategy | Knowledge | Test pyramid, coverage strategy, what to test |
| execution-unit-testing | Execution | Write effective unit tests |
| execution-integration-testing | Execution | Design integration test suites |
| execution-contract-testing | Execution | Consumer-driven contract testing |
| execution-tdd-workflow | Execution | Red-green-refactor cycle |

### Frontend (`frontend/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-component-design | Knowledge | Component composition, props, state boundaries |
| knowledge-state-management | Knowledge | Local vs global state, state machines |
| knowledge-accessibility | Knowledge | WCAG, ARIA, semantic HTML, keyboard navigation |
| execution-responsive-layout | Execution | Build responsive layouts with modern CSS |
| execution-form-handling | Execution | Forms, validation, error states, submission |

### Backend (`backend/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-service-patterns | Knowledge | Repository, Unit of Work, CQRS, Saga |
| knowledge-error-handling | Knowledge | Error hierarchies, propagation, recovery |
| knowledge-concurrency | Knowledge | Threads, async, locks, race conditions |
| execution-background-jobs | Execution | Design reliable background job processing |
| execution-caching-strategy | Execution | Choose and implement caching layers |

### Database (`database/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-schema-design | Knowledge | Normalization, denormalization, trade-offs |
| knowledge-indexing-strategy | Knowledge | Index types, composite keys, query plans |
| knowledge-data-modeling | Knowledge | Conceptual, logical, physical modeling |
| execution-migration-workflow | Execution | Safe, reversible schema migrations |
| execution-query-optimization | Execution | Identify and fix slow queries |

### Infrastructure (`infrastructure/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-cloud-architecture | Knowledge | Multi-tier, multi-region, well-architected |
| knowledge-twelve-factor | Knowledge | Twelve-Factor App methodology |
| knowledge-networking | Knowledge | DNS, load balancing, CDN, TLS |
| execution-containerization | Execution | Dockerfile best practices, multi-stage builds |
| execution-iac | Execution | Infrastructure as Code workflows |

### Observability (`observability/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-observability-pillars | Knowledge | Logs, metrics, traces and how they interrelate |
| execution-logging-strategy | Execution | Structured logging, levels, correlation |
| execution-metrics-alerting | Execution | Define SLIs/SLOs, set up alerting |
| execution-distributed-tracing | Execution | Instrument services for distributed tracing |

### Deployment (`deployment/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-cicd-principles | Knowledge | Continuous integration and delivery philosophy |
| knowledge-release-strategies | Knowledge | Blue-green, canary, rolling, feature flags |
| execution-cicd-pipeline | Execution | Design a CI/CD pipeline from scratch |
| execution-git-workflow | Execution | Branch strategy, PR workflow, release process |
| execution-feature-flags | Execution | Implement feature flags safely |

### Product (`product/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-product-thinking | Knowledge | Jobs-to-be-done, outcome over output |
| knowledge-mvp-strategy | Knowledge | Minimum viable product scoping |
| knowledge-prioritization | Knowledge | RICE, ICE, MoSCoW, opportunity cost |
| execution-user-story-mapping | Execution | Map user journeys to development slices |

### Documentation (`documentation/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-technical-writing | Knowledge | Clarity, audience, structure |
| execution-api-docs | Execution | Write API reference documentation |
| execution-runbooks | Execution | Create operational runbooks |
| execution-decision-records | Execution | Write ADRs and RFCs |

### Performance (`performance/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-optimization-principles | Knowledge | Measure first, Amdahl's law, premature optimization |
| knowledge-caching-patterns | Knowledge | Cache-aside, write-through, TTL strategies |
| execution-profiling | Execution | Profile and identify bottlenecks |
| execution-load-testing | Execution | Design and run load tests |

### AI Workflows (`ai-workflows/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-agent-design | Knowledge | Agent architectures, tool use, planning |
| knowledge-prompt-engineering | Knowledge | Prompt patterns, few-shot, chain-of-thought |
| knowledge-guardrails | Knowledge | Safety, validation, fallbacks |
| execution-rag-pipeline | Execution | Build a retrieval-augmented generation pipeline |

### Proof of Concept (`poc/`)
| Skill | Type | Summary |
|-------|------|---------|
| knowledge-poc-methodology | Knowledge | PoC philosophy, success criteria, timebox |
| execution-poc-scaffold | Execution | Bootstrap a new PoC project |
| execution-spike-design | Execution | Design a technical spike |
| execution-evaluation-criteria | Execution | Define and score evaluation criteria |

### Reviewers (`reviewers/`)
| Skill | Type | Summary |
|-------|------|---------|
| review-architecture | Reviewer | Evaluate architectural decisions and structure |
| review-security | Reviewer | Identify security vulnerabilities and risks |
| review-performance | Reviewer | Find performance issues and optimization opportunities |
| review-accessibility | Reviewer | Audit for WCAG compliance and usability |
| review-api | Reviewer | Evaluate API design quality and consistency |
| review-database | Reviewer | Review schema design, queries, migrations |
| review-ux | Reviewer | Critique user experience and interaction design |
| review-production-readiness | Reviewer | Assess readiness for production deployment |

### Intern Productivity (`engineering-journal/`, `code-explainer/`, `question-formatter/`, `one-on-one-prep/`)
| Skill | Type | Summary |
|-------|------|---------|
| engineering-journal | Execution | Log debug sessions, learnings, and end-of-day reflections into a weekly journal |
| code-explainer | Execution | Explain code at any scope — file, directory, or full repo — with multiple depth modes |
| question-formatter | Execution | Turn vague problems into structured, answerable technical questions |
| one-on-one-prep | Execution | Prepare for 1:1 meetings with auto-pulled activity and guided reflection |

## Usage

Skills are loaded individually by AI agents based on task context. Each skill is self-contained with cross-references to related skills where deeper context is needed.

**Loading a skill**: Reference the path `cursor-skills/{domain}/{type-name}/SKILL.md`

**Combining skills**: For complex tasks, load multiple skills. For example, building a new API might combine:
- `api/execution-rest-api-design` (the procedure)
- `architecture/knowledge-clean-architecture` (structural guidance)
- `security/knowledge-owasp-top-10` (security considerations)
- `testing/execution-integration-testing` (test approach)
- `reviewers/review-api` (self-review checklist)
