<!-- SPDX-License-Identifier: Apache-2.0 -->

# 🗺️ Tazama repo map

You have the whole Tazama ecosystem checked out under
`/home/sohaib/Documents/frms/`. This is your index: which repo does what, and
which lesson it belongs to. All paths are relative to `/home/sohaib/Documents/frms/`.

> **Two eras to keep straight** (see [3.3](../03-messaging-and-data/3.3-databases.md), [4.4](../04-devops-networking/4.4-helm.md)):
> the **current** stack runs on **PostgreSQL + Valkey + NATS** (docker-compose);
> the **older** stack runs on **ArangoDB + Redis + NATS** (Helm charts). Both are
> on disk. Code is ground truth ([6.3](../06-senior-engineer/6.3-reading-unfamiliar-systems.md)).

---

## The transaction pipeline (data plane) — Module 2

| Repo | Role | Consumes | Produces | Read first |
|---|---|---|---|---|
| `event-monitoring-service` | HTTP front door (TMS/DEMS): validate, normalize, persist, publish | HTTP `POST /dems-engine/...` | NATS `event-director` | `src/main.ts`, `src/dems-engine/dems-engine.service.ts` |
| `event-director` | Router: load network map, dedupe, fan out to rules | NATS `event-director` | NATS `sub-rule-<id>` (per rule) | `src/index.ts`, `src/services/logic.service.ts` |
| `rule-executer` | Generic host that runs one rule | NATS `sub-rule-<name>@<ver>` | NATS `pub-rule-<name>@<ver>` | `src/index.ts`, `src/controllers/execute.ts` |
| `rule-011`, `rule-074`, `rule-template` | Individual rule packages (the domain logic) | (loaded by rule-executer) | a `RuleResult` | `rule-011/src/rule-011.ts` |
| `case-management-system` | Investigators work alerts as cases (dual-ingress: NATS + REST/React) | NATS (alerts) + HTTP | — | `backend/src/modules/nats/nats.service.ts` |

> The **Typology Processor** and **TADProc/Event-Adjudicator/Event-Flow** sit
> between the rules and the CMS. They may be partial or absent in this checkout;
> the rule-executer's code names the next hop ("send.to.typroc"). Treat them as
> "downstream" ([2.4](../02-tazama-architecture/2.4-rules-typologies-scoring.md)).

Other pipeline-adjacent repos you may see: `event-director`, `event-flow`,
`event-monitoring-service`, `admin-service` (admin/config API), `auth-service`
(identity), `rule-executer` variants.

---

## Shared libraries (the glue) — Modules 2–3

| Repo | Role | Read first |
|---|---|---|
| `frms-coe-lib` | Shared core: DB managers (`CreateDatabaseManager`/`CreateStorageManager`), Redis client (`ioredis`), `LoggerService` (pino), config loaders, APM, and **all the interfaces/contracts** (`NetworkMap`, `RuleResult`, `DataCache`, `Pacs.002`…) | `src/index.ts`, `src/interfaces/NetworkMap.ts`, `src/services/dbManager.ts` |
| `frms-coe-startup-lib` | Messaging bootstrap: `StartupFactory`, `IStartupService`, `server.init`/`handleResponse` over NATS or JetStream | `src/interfaces/iStartupService.ts`, `src/services/jetstreamService.ts` |
| `tcs-lib` | Connection Studio library: **data mapping / message transformation** (ISO 20022 → canonical), JSON-schema conversion, XML→JS. *Not* scoring — the ingest/mapping layer. | `src/index.ts`, `README.md` |

---

## Infrastructure & deployment — Module 4

| Repo / path | Role | Teaches |
|---|---|---|
| `tazama-stack/core/` | **Current** deployment: docker-compose (Valkey + Postgres + NATS + app services), split across many files via `include:` + profiles | Docker, Compose, config/env ([4.2](../04-devops-networking/4.2-containers-and-docker.md), [4.5](../04-devops-networking/4.5-configuration-and-secrets.md)) |
| `Full-Stack-Docker-Tazama/` | Mirror/variant of the compose stack | same |
| `tazama-personal/*` | Per-service repos with **Dockerfiles** (multi-stage, distroless, nonroot) and `.env.template`s | Dockerfiles, secret-free templates ([4.2](../04-devops-networking/4.2-containers-and-docker.md)) |
| `FST-performance/tazama-lf/EKS-helm` (+ `AKS-helm`, `GKE-helm`) | **Older** deployment: Helm umbrella charts (Arango + Redis + NATS + full observability), rich set of K8s resources | Kubernetes, Helm, ConfigMaps/Secrets, RBAC ([4.3](../04-devops-networking/4.3-kubernetes-from-first-principles.md), [4.4](../04-devops-networking/4.4-helm.md)) |
| `tms-configuration` | Config bundle (Postman collections) that POSTs rule/typology/network-map config into the platform; references ArangoDB constraints | Config-as-data, the network map |
| `nats-utilities` | NATS helper tooling | NATS ops ([3.2](../03-messaging-and-data/3.2-nats-deep-dive.md)) |

---

## AI / ML & data — Module 5

| Repo | Role | Teaches |
|---|---|---|
| `ATM-AI-API` | FastAPI service serving a trained **stacking ensemble** (RF + GB + AdaBoost) with confidence scores; dockerized | Serving a model as a service ([5.1](../05-ai-and-ml/5.1-ml-in-systems.md), [5.2](../05-ai-and-ml/5.2-serving-and-mlops.md)) |
| `data_collection` | The training side: `fraud_detection_ML.ipynb`, `ml_training_dataset.csv`, a fraud-detection API | Training vs serving; features ([5.2](../05-ai-and-ml/5.2-serving-and-mlops.md)) |
| `biar` | Data lakehouse & analytics: PySpark + Apache Hudi ETL, NiFi, JupyterHub dashboards, query API | The data platform that feeds ML ([5.1](../05-ai-and-ml/5.1-ml-in-systems.md)) |
| `case-management-system` (triage module) | AI-assisted triage — ML *advises*, humans *decide* | Advise-don't-decide pattern ([5.1](../05-ai-and-ml/5.1-ml-in-systems.md)) |
| `dataset-gen`, `simulation-env`, `tazama-stress`, `FST-performance` | Data generation, simulation, and load/stress testing | Benchmarking & performance ([1.2](../01-foundations/1.2-latency-throughput-numbers.md), [6.4](../06-senior-engineer/6.4-reliability-and-slos.md)) |

---

## Tooling / UI / studios (context, not core)

`config-ui`, `connection-studio`, `rule-studio`, `rule-builder`,
`simulation-env` — authoring and configuration UIs for rules, connections, and
simulations. `postman` — API collections. `debug` — scratch. These are how humans
*author* the config-as-data that the pipeline consumes; peek at them when a lesson
mentions the network map or rule config.

---

## The docs repo itself (where this curriculum lives)

You're in `docs/`. Beyond `Learning/`, the richest sources this curriculum draws
on:

- `Guides/tazama-design-principles.md` — the tradeoff philosophy (Modules 1, 6).
- `Technical/nats-usage.md` — messaging (Module 3).
- `Technical/Database/` — schema, indexes, object explanations (Module 3).
- `Technical/Logging/` — the whole observability story (Module 6).
- `Technical/Environment-Setup/Infrastructure/` — infra spec, scaling tiers (Modules 4, 6).
- `Technical/Performance-Benchmarking/` — measured tradeoffs (Modules 1, 6).
- `Technical/Processors/Rule-Processors/` — rule exit/error conditions (Modules 1.5, 2.4).
- `Guides/dev-coding-practices.md`, `definition-of-done.md` — team standards (Module 6).

---

## Quick "where do I look for…" table

| I want to understand… | Go to |
|---|---|
| How a transaction flows | `event-monitoring-service` → `event-director` → `rule-executer` |
| The message contracts | `frms-coe-lib/src/interfaces/` |
| How services talk (NATS) | `frms-coe-startup-lib/src/services/jetstreamService.ts` |
| How a rule is written | `rule-011/src/rule-011.ts`, `rule-template/` |
| How it's containerized | `tazama-personal/*/Dockerfile` |
| How it runs locally | `tazama-stack/core/docker-compose.*.yaml` |
| How it runs on K8s | `FST-performance/tazama-lf/*-helm/` |
| Config & secrets | `tazama-stack/core/env/`, `*/.env.template` |
| ML in the system | `ATM-AI-API/`, `data_collection/`, `biar/` |
| Logging/observability | `frms-coe-lib/src/services/logger.ts`, `docs/Technical/Logging/` |
