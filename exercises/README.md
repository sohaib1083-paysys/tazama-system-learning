<!-- SPDX-License-Identifier: Apache-2.0 -->

# 🧪 Exercises

Hands-on tasks, one per module, plus a capstone. These are **doing** exercises —
open real files, run real commands, draw real diagrams. Reading the lessons gives
you 30% understanding; doing these gives you the other 70%.

No answer keys — the point is the struggle. But everything you need is in the
lessons and the repos. Bring me your answers or your stuck points.

---

## Module 1 — Foundations

**1a. The numbers.** Without looking, order these from fastest to slowest: RAM
read, SSD read, cross-ocean network round trip, L1 cache, same-datacenter round
trip. Then check against [1.2](../01-foundations/1.2-latency-throughput-numbers.md).
Now: a rule takes 25ms to process one message. Using Little's Law, how much
concurrency do you need to sustain **2,000 messages/second**?

**1b. Tradeoff hunt.** Open [`../../Guides/tazama-design-principles.md`](https://github.com/tazama-lf/docs/blob/main/Guides/tazama-design-principles.md).
For **each** of the 6 principles, write one line: "gives up ___ to gain ___."
(This is the [6.1](../06-senior-engineer/6.1-tradeoffs-not-answers.md) exercise —
do it now, redo it after Module 6, compare.)

**1c. Failure math.** A request passes through 5 services, each 99.95% available.
What's the end-to-end availability? Now the CMS adds a 6th at 99.9%. Recompute.
What does this tell you about adding hops?

---

## Module 2 — Tazama architecture

**2a. The trace.** From memory, write the ordered path of a `pacs.008` from HTTP
arrival to investigator alert. Include the **four NATS subjects** between hops.
Check against [2.2](../02-tazama-architecture/2.2-transaction-journey.md).

**2b. Find the pattern.** Open the entry files of **two** services:
`/home/sohaib/Documents/frms/event-director/src/index.ts` and
`/home/sohaib/Documents/frms/rule-executer/src/index.ts`. For each, find and
write down: (1) the `server.init(...)` consume + produce subjects, (2) which DB
stores it declares. Confirm they share the skeleton from
[2.3](../02-tazama-architecture/2.3-processor-pattern.md).

**2c. Read a real rule.** Open `/home/sohaib/Documents/frms/rule-011/src/rule-011.ts`.
Find: (a) the exit conditions and what each `subRuleRef` means, (b) the
`_eventHistory.query(...)` SQL, (c) the final `determineOutcome(...)` call. In one
paragraph, explain *what question rule-011 answers*.

**2d. Trace a new rule.** Open `/home/sohaib/Documents/frms/rule-074/src/rule-074.ts`.
What's its independent variable? (Hint: haversine.) Confirm it has the *same
skeleton* as rule-011 but a different question. What real-world fraud does
"impossible travel" catch?

---

## Module 3 — Messaging & data

**3a. Subject sleuthing.** Using grep, find every place the subject
`event-director` appears across the repos:
```bash
grep -rn "event-director" /home/sohaib/Documents/frms/event-director /home/sohaib/Documents/frms/event-monitoring-service --include=*.ts --include=*.env
```
Identify: who **publishes** to it, who **consumes** it. You've just traced a
contract by yourself.

**3b. Core NATS vs JetStream.** In `frms-coe-startup-lib`, find `startupFactory.ts`
and the two service files (`natsService.ts`, `jetstreamService.ts`). Write two
sentences: what does a service gain by choosing `jetstream` over `nats`, and what
does it cost?

**3c. Cache layers.** In `/home/sohaib/Documents/frms/event-director/src/services/logic.service.ts`,
find the network-map lookup. List the layers it checks in order (node-cache →
Redis → DB). What's the cache **key**, and why must it include the tenant?

**3d. The DB migration.** Find one file mentioning ArangoDB and one env var
pointing at PostgreSQL. In two sentences, explain the migration and why
abstracting DB access behind `frms-coe-lib` made it cheaper.

---

## Module 4 — DevOps & networking

**4a. Read a Dockerfile.** Open `/home/sohaib/Documents/frms/tazama-personal/rule-executer/Dockerfile`.
Identify the three stages, and for each say what it does and what it *leaves out*
of the final image. Why `USER nonroot`? Why distroless?

**4b. Read the compose stack.** In `/home/sohaib/Documents/frms/tazama-stack/core/`,
open `docker-compose.base.infrastructure.yaml`. List the three infra services and
map each to a Module 3 concept. Then in `docker-compose.dev.core.yaml`, find how
`tms-service` learns where Postgres is — is it an IP or a name?

**4c. Decode a config file.** Open one file in `.../tazama-stack/core/env/` (e.g.
`event-director.env`). For every line, say what it configures. Which lines are
**where things are** (hosts/ports), which are **who I am** (identity), which are
**secrets that should NOT be here**?

**4d. K8s resource bingo.** In `/home/sohaib/Documents/frms/FST-performance/tazama-lf/EKS-helm/charts/`,
open one file each of: a Deployment, a Service, a ConfigMap, a StatefulSet. For
each, find the `kind:` line and write one sentence on what that resource does and
why *that* component uses *that* kind.

**4e. (If you have Helm) Render the chart.**
```bash
helm template /home/sohaib/Documents/frms/FST-performance/tazama-lf/EKS-helm | head -200
```
Find one `{{ .Values.x }}` placeholder in a template and trace it to its value in
`values.yaml`. This is the single best way to "get" Helm.

---

## Module 5 — AI / ML

**5a. Anatomy of a served model.** Open `/home/sohaib/Documents/frms/ATM-AI-API/main.py`
and `README.md`. Identify: the `/predict`, `/health`, `/model-info` endpoints;
where the model is loaded; and what the confidence score enables downstream.
Explain why this counts as a *system component*, not just a model.

**5b. Two worlds.** Open `/home/sohaib/Documents/frms/data_collection/fraud_detection_ML.ipynb`.
Which features does training use? Now ask: does `ATM-AI-API` build the same
features the same way? Write down why a mismatch would be a (silent) bug.

**5c. Advise vs decide.** In the CMS, open
`.../case-management-system/backend/src/modules/triage/triage.service.ts`. Where
does AI *assist* vs where does a human/rule *decide*? Why is that the right split
for fraud/AML?

---

## Module 6 — Senior engineering

**6a. Observability thread.** Find `traceParent` in the event-director code and
the `id` parameter in `frms-coe-lib`'s `logger.ts`. Explain, in your own words,
how these two let you reconstruct one transaction's journey across all services.

**6b. Design an SLO.** For Tazama's transaction path, propose one SLI, one SLO
(stricter), and one SLA. Justify the numbers using the fraud/real-time context.
What would you alert on — and why *not* alert on CPU?

**6c. Read a service cold.** Pick a service you've never opened (`admin-service`,
`event-flow`, or `event-adjudicator`). Apply the six-step method from
[6.3](../06-senior-engineer/6.3-reading-unfamiliar-systems.md). Produce: its
edges, its subjects, its state, whether it fits the skeleton, and a one-sentence
"its one job."

---

## 🏆 Capstone

Pull the whole curriculum together. Produce a **one-page design document** (a
real senior deliverable) for the following:

> *"Tazama needs a new rule that flags a transaction when the same account sends
> to 5+ new beneficiaries within 10 minutes (a 'fan-out' / mule pattern). Design
> it end to end."*

Your one-pager must cover:

1. **Where it fits** — which NATS subjects does it consume/produce? What existing
   pattern does it follow? (Modules 2–3)
2. **The rule contract** — what does its `handleTransaction` compute, what's the
   `indpdntVarbl`, what bands/`subRuleRef`s, what exit conditions? (2.4)
3. **State & data** — what does it query, from which store, and does it need a
   new index? What could you cache? (3.3, 3.4)
4. **Failure & scale** — how does it behave if history is missing or the DB is
   slow? How does it scale to 3,000 tx/s? (1.4, 1.5)
5. **Deployment** — how is it built (Dockerfile pattern), configured (env vars),
   and deployed (its own rule-executer instance, subject)? (Module 4)
6. **Observability** — what would you log, what metric would you emit, how would
   you know it's working? (6.2)
7. **The tradeoff** — state one thing you optimized for and what you gave up.
   (6.1)

Bring it to me and we'll review it like a real design review. If you can write
this page well, you've genuinely absorbed the material — this is the deliverable
that proves it.
