# Telemetry & APM (Snowplow)

This repository captures notes, problems, and process learnings around **client telemetry** and **APM**, with the goal of evolving a **complete, practical playbook** from real work. Delivery is **incremental**: small steps rather than a big-bang rollout.

---

## Context: telemetry, APM, and why this exists

### Telemetry

**Telemetry** (here: **client / product telemetry**) is the **automated collection of structured signals** from applications and devices: what users or systems did, in what order, with what context (feature usage, funnels, errors surfaced to the client, experiments, and business-relevant events). It answers questions such as *“Is this feature adopted?”*, *“Where do users drop off?”*, and *“Does behavior change after a release?”*  

It is distinct from—but complementary to—raw infrastructure metrics (CPU, pods). Product telemetry is usually **event-centric**, schema-driven, and feeds analytics, warehouses, and downstream BI.

### APM

**APM** (**Application Performance Monitoring**) focuses on **how well software performs and where it fails**: latency, throughput, error rates, traces across services, dependencies (DB, APIs, queues), and often user-impacting symptoms (e.g. slow screens correlated to backend spans). It answers *“What is slow?”*, *“Which dependency broke?”*, and *“Did this deploy degrade SLOs?”*  

Modern stacks often blend **traces**, **metrics**, and **logs**; overlaps with telemetry exist when the same pipeline carries both business events and operational signals—this repo stays explicit about **purpose** (product insight vs reliability) even when tooling touches both.

### Why we invest in this

- **Observable products**: decisions need evidence—telemetry ties releases, features, and experiments to measurable outcomes.  
- **Reliability and speed**: APM and strong validation shorten mean time to detect and repair (MTTD/MTTR) when behavior or performance regresses.  
- **Governance by design**: classification and security review **before** wide ingestion reduce privacy risk and rework.  
- **Repeatable workflow**: from YAML/spec → codegen → engineering → validation → dashboards and alerts, the path stays **auditable** and teachable for new teams and tools (e.g. Snowplow).  

This repository exists to **document that workflow**, capture **failure modes and fixes**, and grow a **complete solution** one step at a time—without relying on tribal knowledge alone.

---

## Current stack (work environment)

- **Pipeline**: Snowplow (open source)
- **Approach**: structured definitions (e.g. YAML) drive codegen and security review, then development and validation close the loop.

---

## End-to-end flow (current backbone)

Use these stages as the **top-level index** for deeper docs and checklists. Each stage can grow its own pages (specs, templates, examples, FAQs).

| Stage | Description |
|-------|-------------|
| **1. Tracking requirements** | Business goals, event/metric scope, prioritization, alignment with product milestones. |
| **2. YAML design for events** | Event names, properties, constraints, versioning; aligned with the Snowplow model (or internal standards). |
| **3. Security review & data classification** | Submission for review; sensitivity tiers driving collection, storage, retention, and access. |
| **4. Snowplow codegen from YAML** | Framework generates pipeline or schema-related artifacts from YAML (exact toolchain may vary). |
| **5. Instrumentation code** | Engineers integrate generated outputs on client/server and wire into the app. |
| **6. Data validation** | Sampling, field completeness, traceability to requirements; staging and gradual rollout when needed. |
| **7. Visualization & reporting** | Dashboards, self-serve queries; agreements with stakeholders on readability. |
| **8. Alerting** | Thresholds, anomaly detection, SLAs; hooks into on-call and escalation. |

---

## How to evolve incrementally (recommended)

1. **Freeze the backbone**: treat the eight stages as the outline and milestones; skip deep specs at first, but name owners and deliverables per stage.  
2. **Deepen one or two stages per iteration**: e.g. turn “YAML design” and “validation” into executable checklists before expanding alerting and BI.  
3. **Treat issue write-ups as assets**: link each lesson learned back to a stage so fixes do not live only in chat.  
4. **Align with Snowplow versions/components**: later, document Loader, Enrich, Iglu / schema repo, etc., with version pins where relevant.

---

## Optional repo layout (add when needed)

- `docs/` — per-stage detail, templates, short summaries of reviews  
- `docs/recorded-problems.md` — curated problem statements and evolving answers  
- `schemas/` or `examples/` — YAML / schema samples (sanitize secrets and PII)  
- `runbooks/` — alert response and data-incident triage  

---

## Recorded problems & questions

Practice-driven issues are tracked in **[`docs/recorded-problems.md`](docs/recorded-problems.md)** (stable IDs **RP-001**–**RP-004** and onward).

---

## Next steps (placeholders)

- [ ] Expand stages 1–2 with a requirements template and minimal YAML field guide  
- [ ] Expand stage 6 with a validation checklist (environment, fields, volume)  
- [ ] Document the real path in your environment: generated artifacts → code → warehouse, plus a simple diagram  

---

*Commits can evolve one stage at a time so each step stays shippable and reviewable.*
