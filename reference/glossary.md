<!-- SPDX-License-Identifier: Apache-2.0 -->

# 📚 Glossary

One line each. Grouped by area. When a term links to a lesson, that's where it's
taught properly.

## System design fundamentals

- **Latency** — time for *one* request. ([1.2](../01-foundations/1.2-latency-throughput-numbers.md))
- **Throughput** — requests handled per second. ([1.2](../01-foundations/1.2-latency-throughput-numbers.md))
- **Percentile (p50/p99/p99.9)** — "X% of requests are faster than this"; optimize p99, not the average.
- **Little's Law** — concurrency = throughput × latency; the formula linking the three.
- **Vertical scaling** — a bigger machine. **Horizontal scaling** — more machines/copies.
- **Cohesion** — how much a module's parts belong together (want: high). ([1.3](../01-foundations/1.3-coupling-and-services.md))
- **Coupling** — how much one module depends on another's internals (want: low). ([1.3](../01-foundations/1.3-coupling-and-services.md))
- **Conway's Law** — systems mirror the org chart of the teams that build them.
- **Stateless** — remembers nothing between requests; scales trivially. **Stateful** — keeps data; hard to scale. ([1.4](../01-foundations/1.4-state-the-hard-problem.md))
- **CAP theorem** — during a network partition you must choose Consistency **or** Availability. ([1.4](../01-foundations/1.4-state-the-hard-problem.md))
- **Idempotency** — doing an operation twice = doing it once; essential under retries. ([1.4](../01-foundations/1.4-state-the-hard-problem.md))
- **SPOF** — single point of failure; a thing whose death stops everything. ([1.5](../01-foundations/1.5-design-for-failure.md))
- **ADR** — Architecture Decision Record; a written "we chose X because, trading away Y." ([6.1](../06-senior-engineer/6.1-tradeoffs-not-answers.md))

## Failure-design patterns ([1.5](../01-foundations/1.5-design-for-failure.md))

- **Timeout** — never wait forever on a call across a boundary.
- **Retry with backoff + jitter** — retry transient failures, spacing them out to avoid storms.
- **Circuit breaker** — stop calling a dependency that keeps failing; fail fast, retest later.
- **Bulkhead** — isolate resources so one overloaded part can't sink the rest.
- **Graceful degradation** — do the reduced-but-useful thing instead of erroring out.
- **Dead-letter** — where messages go after retries are exhausted.

## Messaging ([Module 3](../03-messaging-and-data/))

- **Synchronous (request/response)** — call and wait (HTTP, gRPC). Simple; couples in time.
- **Asynchronous (messaging)** — drop a message and move on. Resilient; harder to reason about.
- **Pub-sub** — every subscriber to a subject gets its own copy. (Fan-out.)
- **Queue / queue group** — a message goes to exactly *one* of a pool of workers. (Load-sharing.)
- **Stream / log** — messages retained and replayable (Kafka, JetStream).
- **NATS** — Tazama's lightweight messaging system; addresses by **subject** strings.
- **Subject** — a string address in NATS (e.g. `sub-rule-011@1.0.0`); wildcards `*` and `>`.
- **Core NATS** — fast, at-most-once, lost if no listener. **JetStream** — durable, ack'd, replayable. ([3.2](../03-messaging-and-data/3.2-nats-deep-dive.md))
- **Ack** — a consumer's "I've safely handled this"; until then JetStream may redeliver.
- **Protobuf** — compact binary serialization Tazama uses on the wire (vs bulky JSON).
- **Relay service** — Tazama adapter bridging NATS to other systems (REST/Kafka/RabbitMQ/BigQuery).

## Data & caching ([Module 3](../03-messaging-and-data/))

- **Relational DB (SQL)** — rows, relationships, transactions; Tazama uses **PostgreSQL** (current).
- **Document DB** — flexible nested JSON; Tazama uses **CouchDB** for case evidence.
- **Key-value / cache** — fast "value for this key"; **Redis** / **Valkey**.
- **Search engine** — full-text/analytical search; Tazama uses **OpenSearch** for audit logs.
- **Multi-model DB** — documents + graph; **ArangoDB** (Tazama's older store).
- **Index** — sorted lookup structure so the DB finds rows without scanning all of them.
- **Prepared statement** — parameterized SQL (`text` + `values`); injection-safe and plan-cached.
- **Polyglot persistence** — using several database types, each for the job it fits.
- **Cache** — a small fast copy of data kept close; trades memory + staleness for speed. ([3.4](../03-messaging-and-data/3.4-caching-and-redis.md))
- **TTL** — time-to-live; how long a cached copy is trusted before expiring.
- **Cache invalidation** — keeping cached copies from going stale; famously hard.
- **Valkey** — open-source fork of Redis (permissive license); Redis-compatible protocol.

## Containers, K8s, DevOps ([Module 4](../04-devops-networking/))

- **IP address** — a machine's network address. **Port** — a specific program on that machine.
- **DNS** — resolves a *name* to an IP; in containers, **service names are DNS names**.
- **TCP** — reliable ordered byte stream. **UDP** — fast lossy datagrams.
- **HTTP** — request/response protocol over TCP; status codes 2xx/4xx/5xx.
- **Load balancer** — spreads connections across copies of a service.
- **Reverse proxy / Ingress** — single front door routing to internal services (e.g. nginx).
- **Image** — frozen blueprint of a container's filesystem + startup. **Container** — a running instance of an image.
- **Dockerfile** — recipe to build an image. **Layer** — a cached step in that build.
- **Multi-stage build** — build with a big toolchain image, ship only a tiny final stage.
- **Distroless** — a minimal runtime image with no shell/package manager; small attack surface.
- **Registry** — where images are pushed/pulled (Docker Hub, cloud registries).
- **Docker Compose** — describe & run a multi-container app on one machine (Tazama's dev stack).
- **Healthcheck** — how the orchestrator knows a container is *ready*, not just started.
- **Volume** — storage mounted into a container; how ephemeral containers keep durable data.
- **Kubernetes (K8s)** — runs containers across many machines to a declared **desired state**.
- **Declarative / desired state** — you say what should be true; K8s reconciles reality to it.
- **Pod** — smallest K8s unit; one (or few) containers sharing a network identity.
- **Deployment** — keeps N identical **stateless** Pods running; rolling updates.
- **StatefulSet** — for **stateful** Pods needing stable identity + own disk (DBs, NATS).
- **Service (K8s)** — stable name + virtual IP fronting a changing set of Pods (in-cluster LB + DNS).
- **ConfigMap** — non-secret config injected into Pods (K8s equivalent of a `.env` file).
- **Secret (K8s)** — sensitive config (passwords, tokens), access-restricted.
- **PersistentVolumeClaim (PVC)** — a request for durable disk.
- **HorizontalPodAutoscaler (HPA)** — adds/removes Pods automatically based on load.
- **NetworkPolicy** — firewall rules for which Pods may talk to which.
- **PodDisruptionBudget (PDB)** — limits how many Pods can be down at once (protects availability).
- **RBAC (ServiceAccount/Role/RoleBinding)** — who/what is allowed to do what.
- **Namespace** — a logical partition of a cluster (e.g. `development`, `processor`).
- **Helm** — package manager + templating for K8s; **chart** = a packaged app. ([4.4](../04-devops-networking/4.4-helm.md))
- **Chart / values.yaml / templates/** — the package / its configurable knobs / its templated YAML.
- **Umbrella chart** — a parent chart that composes sub-charts via dependencies.
- **Twelve-Factor** — app guidelines; notably "config in the environment, not the code." ([4.5](../04-devops-networking/4.5-configuration-and-secrets.md))
- **Vault / KMS** — dedicated encrypted secrets manager for production.

## AI / ML ([Module 5](../05-ai-and-ml/))

- **Rule-based vs ML** — humans write logic vs a model learns it from labeled data.
- **Training vs serving** — build the model offline (slow, batch) vs run it per request (fast, API).
- **Feature** — an engineered number a model consumes as input.
- **Training/serving skew** — features computed differently in training vs serving → silent bad predictions.
- **Feature store** — shared pipeline so training and serving build features identically.
- **Model artifact** — the trained model file (weights) shipped from training to serving.
- **Model drift** — a deployed model getting worse as the world changes.
- **MLOps** — DevOps for ML: version, validate, deploy, monitor, retrain, loop.
- **Shadow mode** — a new model predicts on real traffic but its output isn't used yet (safe compare).
- **Confidence score** — a model's stated certainty; lets low-confidence cases defer to a human.
- **LLM** — large language model; a slow, non-deterministic, expensive service you design around.

## Reliability & operations ([6.2](../06-senior-engineer/6.2-observability.md), [6.4](../06-senior-engineer/6.4-reliability-and-slos.md))

- **Observability** — being able to answer "what/why is it doing this?" from emitted telemetry.
- **Logs / Metrics / Traces** — what happened (detail) / how much over time / follow one request across services.
- **Structured logging** — machine-parseable (JSON) logs with consistent fields.
- **Correlation / trace id** — an id stamped on every hop so one request can be reassembled (Tazama: `traceParent`, log `id`).
- **RED** — the per-service metrics trio: Rate, Errors, Duration.
- **APM** — Application Performance Monitoring; tracing + perf (Tazama uses Elastic APM).
- **Availability ("nines")** — fraction of time up; each extra nine costs exponentially more.
- **SLI / SLO / SLA** — measured indicator / internal target / contractual promise (SLO stricter than SLA).
- **Error budget** — the allowed unavailability under your SLO; spend it on shipping, freeze when it's gone.
- **MTBF / MTTR** — mean time *between* failures / mean time *to* recovery; invest in both, especially MTTR.
- **Runbook** — pre-written response steps for an alert.
- **Blameless postmortem** — incident write-up focused on systemic fixes, not blaming a person.

## Tazama domain ([Module 2](../02-tazama-architecture/))

- **ISO 20022** — the financial messaging standard Tazama ingests (`pain.001`, `pacs.008`, `pacs.002`).
- **PPA (Payment Platform Adapter)** — turns non-ISO20022 traffic into an ISO20022 equivalent.
- **TMS / Event Monitoring Service (DEMS)** — the HTTP front door; validates, normalizes, persists, publishes.
- **Event Director** — the router; looks up the network map and fans out to applicable rules.
- **Rule Executer** — generic host that runs one rule and emits a `RuleResult`.
- **Rule** — one narrow question about a transaction + history → one categorical outcome (`subRuleRef`).
- **Typology** — a named fraud/AML scenario = a **weighted** grouping of rules.
- **Typology Processor** — combines/weights rule results into a typology score.
- **TADProc** — Transaction Aggregation & Decisioning Processor; thresholds → alert/block.
- **Interdiction** — blocking a transaction in real time.
- **EFRuP / Event Flow** — configurable override of blocks; operational controls.
- **Network Map** — per-tenant config wiring messages → typologies → rules; loaded as data.
- **DataCache** — light denormalized transaction context rules read instead of re-parsing the message.
- **RuleResult** — a rule's output: `subRuleRef`, `indpdntVarbl`, `wght`, `prcgTm`, ids.
- **CMS (Case Management System)** — end of pipeline; investigators work alerts as cases.
- **Sidecar** — a helper container beside a service (Tazama: ships logs over NATS to Lumberjack).
- **frms-coe-lib** — shared core: DB managers, Redis client, logger, config, the interfaces/contracts.
- **frms-coe-startup-lib** — the messaging bootstrap: `StartupFactory`, `server.init`, `handleResponse`.
