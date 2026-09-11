<!-- SPDX-License-Identifier: Apache-2.0 -->

# 🎓 System Design & Senior Engineering — Learning Path

> A from-first-principles curriculum that teaches system design, DevOps, and
> senior-engineering thinking **using Tazama as the running real-world example**.
> Every abstract idea here is anchored to actual code and config you already
> have on this machine, so you can read the lesson and then go *look at the
> thing*.

Written for someone who is comfortable coding but wants to understand **why
systems are built the way they are** — networking, Kubernetes, Helm, config
maps, messaging, databases, scaling, failure — and how a serious production
platform (Tazama, a real-time fraud/AML engine in The Linux Foundation)
actually implements those ideas.

---

## About the links in these lessons

This curriculum was written against a full local checkout of the Tazama
ecosystem, so lessons reference two kinds of things:

- **Links between lessons** (e.g. `1.2-...`, `../02-tazama-architecture/...`) are
  relative and work anywhere you clone this repo.
- **Links to Tazama docs** (design principles, NATS usage, DB schema, logging…)
  point to the upstream [`tazama-lf/docs`](https://github.com/tazama-lf/docs)
  repo on GitHub, so they resolve from here.
- **Code-path references** (e.g. `event-director/src/services/logic.service.ts`)
  are written as plain text, not links. They assume you have the Tazama source
  repos checked out locally (they live under the umbrella of
  [`tazama-lf`](https://github.com/tazama-lf) on GitHub). Open them there or in
  your local clone as you read.

---

## How to use this

1. Read in order the first time. The modules build on each other.
2. Every lesson ends with **"Go look at it"** — a real file path in this repo
   or a sibling repo. Open it. Reading about a NATS subscriber is worth 10%;
   seeing Tazama subscribe to a subject is worth the other 90%.
3. Do the exercises in [`exercises/`](exercises/). They are small and
   deliberately hands-on.
4. Keep a scratch file of questions. Come back to me with them.

There is no rush. Senior engineering is a *slow accumulation of mental
models*, not a fact dump.

---

## The map

### 📐 Module 1 — Foundations (think like a system designer)
Before Kubernetes, before NATS — the timeless ideas.

- [1.1 What "system design" even means](01-foundations/1.1-what-is-system-design.md)
- [1.2 Latency, throughput, and the numbers every engineer should know](01-foundations/1.2-latency-throughput-numbers.md)
- [1.3 Coupling, cohesion & why we split into services](01-foundations/1.3-coupling-and-services.md)
- [1.4 Statefulness, and why it's the hardest problem](01-foundations/1.4-state-the-hard-problem.md)
- [1.5 Design for failure (the mindset that separates senior from mid)](01-foundations/1.5-design-for-failure.md)

### 🏗️ Module 2 — Tazama's architecture, dissected
Now we apply Module 1 to a real system you can read line-by-line.

- [2.1 The 10,000ft view: what Tazama is and the shape of the pipeline](02-tazama-architecture/2.1-birds-eye-view.md)
- [2.2 The end-to-end transaction journey (follow one payment through every service)](02-tazama-architecture/2.2-transaction-journey.md)
- [2.3 The processor pattern: how every Tazama service is built the same way](02-tazama-architecture/2.3-processor-pattern.md)
- [2.4 Rules, Typologies & Scoring: the domain logic](02-tazama-architecture/2.4-rules-typologies-scoring.md)

### 📨 Module 3 — Messaging & data (the nervous system + memory)
- [3.1 Messaging models: request/response vs pub-sub vs queues](03-messaging-and-data/3.1-messaging-models.md)
- [3.2 NATS deep dive, as Tazama uses it](03-messaging-and-data/3.2-nats-deep-dive.md)
- [3.3 Databases: pick the right shape (ArangoDB, Redis, Postgres in Tazama)](03-messaging-and-data/3.3-databases.md)
- [3.4 Caching & why Redis is everywhere](03-messaging-and-data/3.4-caching-and-redis.md)

### ⚙️ Module 4 — DevOps & networking (the ground it runs on)
This is the "I don't know how networking / configmaps / k8s / helm work" module.

- [4.1 Networking from zero: ports, DNS, HTTP, load balancers](04-devops-networking/4.1-networking-from-zero.md)
- [4.2 Containers & Docker: what an image really is](04-devops-networking/4.2-containers-and-docker.md)
- [4.3 Kubernetes from first principles (pods, services, deployments, configmaps)](04-devops-networking/4.3-kubernetes-from-first-principles.md)
- [4.4 Helm: templating Kubernetes, and Tazama's charts](04-devops-networking/4.4-helm.md)
- [4.5 Configuration: env vars, config maps, secrets — done right](04-devops-networking/4.5-configuration-and-secrets.md)

### 🤖 Module 5 — AI / ML in production systems
- [5.1 Where ML fits in a system like Tazama](05-ai-and-ml/5.1-ml-in-systems.md)
- [5.2 Serving models, features, and the MLOps loop](05-ai-and-ml/5.2-serving-and-mlops.md)

### 🎖️ Module 6 — Becoming a senior engineer
The non-code skills that actually make the title.

- [6.1 The senior mindset: tradeoffs, not answers](06-senior-engineer/6.1-tradeoffs-not-answers.md)
- [6.2 Observability: logs, metrics, traces (and how Tazama does it)](06-senior-engineer/6.2-observability.md)
- [6.3 Reading a system you didn't build](06-senior-engineer/6.3-reading-unfamiliar-systems.md)
- [6.4 Reliability, SLOs, and on-call thinking](06-senior-engineer/6.4-reliability-and-slos.md)

### 🧪 Exercises
- [exercises/](exercises/) — hands-on tasks, one per module.

### 📚 Reference
- [reference/glossary.md](reference/glossary.md) — every term, one line each.
- [reference/tazama-repo-map.md](reference/tazama-repo-map.md) — which repo does what.

---

## A note on honesty

Where I state something about Tazama's code, I've read the actual files and
cite the path. Where I'm teaching a general concept, I'll say so. If a lesson
ever says "Tazama does X" and you open the file and it does Y — trust the file,
and tell me. The code is the ground truth.

*Now go to [Module 1.1](01-foundations/1.1-what-is-system-design.md).*
