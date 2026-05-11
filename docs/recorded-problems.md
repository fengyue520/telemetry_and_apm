# Recorded problems & questions

Curated notes from practice: **problem statement**, **impact**, and **directions** toward resolution. Entries evolve as we learn.

**Index:** RP-001 · RP-002 · RP-003 · RP-004 *(scroll or search by ID).*

---

## RP-001 — Shared meaning for events and fields (avoid duplicate instrumentation)

**Related stages**: [1 Tracking requirements](../README.md#end-to-end-flow-current-backbone), [2 YAML design for events](../README.md#end-to-end-flow-current-backbone)

### Problem

When designing tracking fields and events, teams need a **shared understanding** of:

- what each **event** represents (business intent, trigger, scope), and  
- what each **field** means (semantics, units, allowed values, PII sensitivity).

Without that alignment:

- **Different people or teams** may instrument the **same real-world behavior** independently → **duplicate work**, conflicting definitions, and harder warehouse modeling.  
- **Naming drift** (different conventions or synonyms for the same concept) creates **ambiguity** in analysis and in downstream dashboards (“which event is canonical?”).

### Impact

- Duplicated implementation and review effort.  
- Fragmented data: two “similar” events that are never reconciled.  
- Higher cost for analysts and poor comparability across squads or apps.

### Directions (non-prescriptive)

These are **options to evaluate**, not a final design:

| Direction | Role |
|-----------|------|
| **Catalog / registry** | Single place listing **approved events and properties** with descriptions, owners, and links to YAML or schema versions (e.g. Iglu / internal repo). |
| **Discovery before implement** | Lightweight step in stage 1–2: search the catalog + ask domain owner **before** adding a new event name. |
| **Naming & taxonomy guide** | Published rules: prefixes/namespaces per domain, stable verbs (`view`, `submit`), casing, versioning policy when semantics change. |
| **Ownership** | Named **steward** per domain or event family for conflicts and deprecations. |
| **Deprecation path** | Process for superseding duplicates (alias period, dual-write window, sunset date). |

### Status

Open — awaiting concrete decisions and tooling fit (Snowplow / YAML codegen / internal catalog).

### Raw note (author language)

> 设计埋点字段时，如何做到字段意义以及埋点事件意义的共享；不同人/team 有可能会对同一件事进行埋点，造成重复劳动；命名规则等不同可能造成歧义。

---

## RP-002 — Tracking data quality; distinguishing implementation bugs from real client behavior

**Related stages**: [5 Instrumentation code](../README.md#end-to-end-flow-current-backbone), [6 Data validation](../README.md#end-to-end-flow-current-backbone), [8 Alerting](../README.md#end-to-end-flow-current-backbone)

### Problem

Teams need **confidence in event quality** and a **repeatable way to triage** when metrics look wrong: is the gap due to **faulty instrumentation** (wrong event, missing property, sampling bug, wrong environment), or due to **genuine client-side behavior** (users blocked, network failures, feature flags, OS differences)?

Without a clear split, incidents waste time debating “data vs reality,” and fixes land in the wrong layer.

### Impact

- Slow root-cause analysis; mistrust in dashboards.  
- Risk of “fixing” product or infra when the pipeline or SDK usage was wrong (or vice versa).

### Directions (non-prescriptive)

| Direction | Role |
|-----------|------|
| **Validation gates** | Stage 6: contract checks (schema vs emitted payload), volume sanity, known seeds / test users in non-prod. |
| **Golden scenarios** | Scripted flows that **must** produce a predictable event sequence; regression after SDK or codegen changes. |
| **Environment & version tagging** | Always attach build, SDK, schema version, sampling key—supports comparing cohorts and spotting partial rollout bugs. |
| **Side-by-side signals** | Where possible, corroborate with server logs, APM traces, or support tickets for the same funnel step. |
| **Runbooks** | Decision tree: “null spike → check schema migration,” “drop after release → diff codegen,” etc. |

### Status

Open.

### Raw note (author language)

> 埋点数据质量怎么保证；出现问题后怎么判断是代码问题还是真的 client 出现的问题？

---

## RP-003 — Visualization ownership, discoverability, and “does this metric exist?”

**Related stages**: [1 Tracking requirements](../README.md#end-to-end-flow-current-backbone), [2 YAML design for events](../README.md#end-to-end-flow-current-backbone), [7 Visualization & reporting](../README.md#end-to-end-flow-current-backbone)

### Problem

**Visualization** (dashboards, reports, self-serve queries) raises organizational questions:

- Should **engineers** own building and maintaining all viz, or should **PM / analytics / data roles** self-serve from documented events?  
- How does **anyone** (not only the original developer) **confirm** that a behavior is already instrumented and whether a **dashboard or report already exists**—without a scavenger hunt through Slack or tickets?

### Impact

- Duplicated dashboards; orphan charts nobody trusts.  
- Non-engineers blocked or dependent on developers for every ad hoc question.  
- No single place to answer “instrumented? where is it used?”

### Directions (non-prescriptive)

| Direction | Role |
|-----------|------|
| **RACI by artifact** | Engineers own **correct emission** and schema; analytics/BI may own **canonical dashboards** per KPI with engineering review. |
| **Catalog links to assets** | Registry entry points to **dashboard URLs**, Looker/Mode/MetaBase links, or “none yet.” |
| **Searchable metadata** | Tags: owner squad, feature area, related product doc. |
| **Lightweight “coverage” view** | List events with last-seen date and linked assets—helps spot dead or undocumented viz. |

### Status

Open.

### Raw note (author language)

> 可视化如何做，是否应该由 engineer 本身来完成；其他人能否做；某个人如何确认某个数据是否有埋点，以及是否已有可视化或报告。

---

## RP-004 — Composable YAML fields: what is a “complete” event, and how do non-developers understand bundles?

**Related stages**: [2 YAML design for events](../README.md#end-to-end-flow-current-backbone), [7 Visualization & reporting](../README.md#end-to-end-flow-current-backbone)

### Problem

YAML (or similar) often defines **discrete fields** that can be **combined freely**. Flexibility helps evolution but obscures **which combinations are valid, complete, and intended** for a given product moment.

If only the implementer holds that mental model, **others cannot navigate** the model when they need to query or request reporting—they must **find the developer**, who then **routes them to the right viz**. That does not scale.

### Impact

- “Incomplete” events in the warehouse (required context missing for analysis).  
- Analysis paralysis: too many optional dimensions with no documented bundles.  
- Self-service analytics blocked without engineering intermediaries.

### Directions (non-prescriptive)

| Direction | Role |
|-----------|------|
| **Event “profiles” or bundles** | Document **recommended field sets** per event family (required vs optional) and **use-case recipes** (“funnel X needs A+B+C”). |
| **Catalog-level examples** | One **golden row** or JSON example per event in human-readable docs. |
| **Linting / CI on YAML** | Rules that reject combinations that violate invariants (e.g. mutually exclusive flags both set). |
| **Consumer-oriented docs** | Short paragraphs per event: *why*, *when fired*, *minimum fields for trust*, *common mistakes*. |

### Status

Open — overlaps **RP-001** (shared semantics); consider cross-linking solutions.

### Raw note (author language)

> YML 中字段离散、可自由组合，但哪些组合是合理或完整的埋点不清晰；除开发者外他人应较容易理解，否则查数据要先找开发者再要可视化，很麻烦。

---

*Add new entries as **RP-###** to keep references stable.*
