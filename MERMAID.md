# datadex architecture — Mermaid (single source)

The **single source** for datadex's architecture diagrams. Author here in Mermaid, then
paste any block into an AI/diagram tool that renders Mermaid (Claude, ChatGPT, Eraser,
Napkin, Excalidraw, GitHub, Notion, mermaid.live) to get a visual.

- The earlier **D2 / C4 + PNG** set is archived under [`_archive/`](./_archive/) (frozen — not maintained).
- Sanity-check any edit instantly at <https://mermaid.live>.

---

## Scope today (decided 2026-06-24)

datadex's job **right now** is two things:

1. **Auto-build a good, evidence-driven ontology** — Objects · Properties · Links, earned from evidence and human-certified.
2. **Give AI the perfect READ context** over it — the Context packet ("company brain").

**Ontology Actions / write-backs are deferred.** The AI *reads and reasons*; it does not
yet write back to your systems. Every diagram below reflects that read-only scope.

---

## The brand palette (paste into any block)

Mermaid blocks don't share definitions, so each diagram repeats the `classDef` lines it
needs. The full set, matching the datadex diagram brand:

```text
classDef person    fill:#08427b,stroke:#052e56,color:#fff;
classDef container fill:#1168bd,stroke:#0b4884,color:#fff;
classDef store     fill:#1168bd,stroke:#0b4884,color:#fff;
classDef external  fill:#8a8a8a,stroke:#5f5f5f,color:#fff;
classDef state     fill:#1168bd,stroke:#0b4884,color:#fff;
classDef ok        fill:#2e7d32,stroke:#1b5e20,color:#fff;
classDef bad       fill:#b23b3b,stroke:#7f2626,color:#fff;
classDef done      fill:#2e7d32,stroke:#1b5e20,color:#fff;
classDef prog      fill:#1168bd,stroke:#0b4884,color:#fff;
classDef early     fill:#e8a33d,stroke:#b97e22,color:#2b2b2b;
classDef none      fill:#d9dde2,stroke:#aab2bd,color:#2b2b2b;
classDef deferred  fill:#eef1f4,stroke:#aab2bd,stroke-dasharray:4 4,color:#5f6b78;
classDef proposed  fill:#6a4c93,stroke:#4a2c73,color:#fff;   # net-new in the target state
```

Zone (subgraph) styling — control plane vs data plane:
```text
style control  fill:#eef4fb,stroke:#0b4884,color:#0b4884
style datazone fill:#fbf2e9,stroke:#cc6600,stroke-dasharray:5 5,color:#cc6600
```

## Cheatsheet (define your own structures)

```text
flowchart TB | LR        direction: top-bottom or left-right
A[Box]                   rectangle        A([Pill])     rounded
A[(Cylinder)]            store/db          A{Diamond}    decision
A:::container            apply a class     class A,B done   apply to many
A --> B                  solid edge        A -->|label| B   labelled
A ==> B                  thick edge        A -.-> B         dashed
A -.->|label| B          dashed + label    "<br/>"          line break in a label
subgraph id["Title"] ... end               group (quote titles with spaces/punctuation)
%% a comment
```
> Tip: if a label has `()` `/` `·` `:`, wrap the whole label in double quotes — `A["a/b (c)"]`.

---

## 1 · Where we sit

```mermaid
flowchart TB
  consumers["Business users · Analysts · AI agents"]:::person
  subgraph datadex["datadex — automatic semantic + context layer"]
    tagline["Builds a business data model automatically · keeps it correct with evidence · gives AI the perfect context to reason over your business"]:::container
  end
  subgraph stack["Your existing data stack — stays exactly as it is"]
    a[Informatica]:::external
    b[Databricks]:::external
    c[("Snowflake / Oracle / SQL Server")]:::external
    d["dbt · files · APIs"]:::external
  end
  consumers -->|"ask · get grounded insight"| datadex
  datadex -->|"reads metadata & data — read-only, via the secure agent"| stack

  style datadex fill:#0b4884,stroke:#052e56,color:#fff
  style stack fill:#fbf2e9,stroke:#cc6600,stroke-dasharray:5 5,color:#cc6600
  classDef person fill:#08427b,stroke:#052e56,color:#fff;
  classDef container fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef external fill:#8a8a8a,stroke:#5f5f5f,color:#fff;
```
> We do **not** replace Informatica / Databricks / Snowflake — we add the brain they don't have: an always-current model + perfect context for AI.

## 2 · How it works

```mermaid
flowchart LR
  connect["1 · Connect<br/>(read-only, secure agent)"]:::state
  observe["2 · Observe<br/>profiles · lineage · quality · usage"]:::state
  propose["3 · Propose<br/>auto-built ontology model"]:::state
  certify["4 · Human certifies<br/>nothing goes live unreviewed"]:::ok
  brain["5 · Company brain<br/>assembles the context packet"]:::state
  reason["6 · AI reasons<br/>answers & analyses with<br/>perfect context (read-only)"]:::state
  connect --> observe --> propose --> certify --> brain --> reason
  reason -.->|"usage becomes new evidence"| observe

  classDef state fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef ok fill:#2e7d32,stroke:#1b5e20,color:#fff;
```
> Your data never leaves your environment; the model is earned from evidence and approved by a human. **Governed write-back Actions are deferred** — today the AI reads and reasons.

## 3 · The living loop (the drift answer)

```mermaid
flowchart LR
  drift["Source schema or data DRIFTS<br/>(a column changes, a feed breaks)"]:::bad
  observe["Observe<br/>re-profile on every run"]:::state
  propose["Propose<br/>update the model"]:::state
  certify["Certify<br/>human approves the change"]:::ok
  serve["Serve<br/>fresh context to AI"]:::state
  use["Use<br/>AI answers & monitors<br/>with grounded context"]:::state
  drift -->|"detected automatically"| observe
  observe -->|"evidence changes → confidence shifts"| propose
  propose -->|"proposed diff"| certify
  certify -->|"approved"| serve
  serve -->|"grounded context"| use
  use -.->|"usage = new evidence"| observe

  classDef state fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef ok fill:#2e7d32,stroke:#1b5e20,color:#fff;
  classDef bad fill:#b23b3b,stroke:#7f2626,color:#fff;
```
> Nothing is ever silently wrong: drift lowers confidence → forces a fresh proposal → human re-certifies. Today the loop serves **READ** context to AI; governed write-back Actions are deferred.

## 4 · datadex architecture — current state

```mermaid
flowchart TB
  op[Operator]:::person
  ai["AI client (Claude)"]:::person
  subgraph datadex["datadex — control plane (metadata only; never touches client data)"]
    direction TB
    surf["Surfaces<br/>Web console · AI chat · MCP gateway"]:::container
    agent["AI agent<br/>builds · operates · self-heals"]:::container
    ctx["Context layer — the company brain<br/>a context packet per business object"]:::container
    onto["Semantic model / ontology<br/>Objects · Properties · Links · certify + confidence"]:::container
    ev["Evidence & metadata<br/>catalog · profiling · lineage · quality · audit"]:::container
    plat["Data-engineering platform<br/>tasks · pipelines (DAG) · runs · promotion"]:::container
    surf --> agent --> ctx --> onto --> ev --> plat
  end
  subgraph dataplane["Data plane — the ONLY tier that touches client data"]
    sec["Secure agent (worker pool)<br/>extract · load · transform · quality"]:::container
  end
  store[("Metadata store<br/>PostgreSQL (encrypted)")]:::store
  client[("Client systems<br/>Oracle · SQL Server · Snowflake · Databricks · files")]:::external
  llm["LLM API (Claude)"]:::external
  op --> surf
  ai --> surf
  agent -->|"drafts · chat · reasoning"| llm
  datadex -->|"all control-plane state"| store
  plat -->|"dispatches work · pull/RPC"| sec
  sec ==>|"extract · load · transform — the only data path"| client

  style datadex fill:#eef4fb,stroke:#0b4884,color:#0b4884
  style dataplane fill:#fbf2e9,stroke:#cc6600,stroke-dasharray:5 5,color:#cc6600
  classDef person fill:#08427b,stroke:#052e56,color:#fff;
  classDef container fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef store fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef external fill:#8a8a8a,stroke:#5f5f5f,color:#fff;
```
> Reads top-to-bottom: platform earns evidence → evidence builds the model → model becomes the brain → **AI reads grounded context**. The control plane sees only metadata. *(Governed write-back Actions are deferred.)*

## 5 · Deployment topology (data residency)

```mermaid
flowchart TB
  op[Operator]:::person
  ai["AI client (MCP)"]:::person

  subgraph customer["Customer environment — your network / cloud"]
    direction TB
    sec["Secure agent (worker pool)<br/>the only tier that touches bulk data"]:::container
    client[("Client systems<br/>Oracle · SQL Server · Snowflake · Databricks · files")]:::external
    sec ==>|"extract · load · transform (native drivers)"| client
  end

  subgraph control["datadex control plane — hosted (metadata only)"]
    direction TB
    web["Web console"]:::container
    api["Control-plane API"]:::container
    mcp["MCP gateway"]:::container
    store[("Metadata store<br/>Postgres · encrypted secrets")]:::store
    web --> api
    mcp --> api
    api --> store
  end

  llm["LLM provider (external)<br/>provider-pluggable seam"]:::external

  op --> web
  ai --> mcp
  sec -->|"pull work · heartbeat · report (HTTPS, OUTBOUND only)"| api
  api -->|"prompts: schema · metadata · bounded samples (never full datasets)"| llm

  style customer fill:#fbf2e9,stroke:#cc6600,stroke-dasharray:5 5,color:#cc6600
  style control fill:#eef4fb,stroke:#0b4884,color:#0b4884
  classDef person fill:#08427b,stroke:#052e56,color:#fff;
  classDef container fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef store fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef external fill:#8a8a8a,stroke:#5f5f5f,color:#fff;
```
> **Bulk client data never leaves your environment** — only the secure agent reads it. The agent reaches **out** to the control plane (HTTPS outbound; no inbound holes in your firewall). The control plane holds metadata + encrypted secrets; the LLM sees prompts only.

## 6 · Semantic-model shape (the brain, made concrete)

```mermaid
flowchart LR
  ev["Evidence behind every element<br/>joins · column lineage · profiling · usage"]:::ev
  subgraph model["Semantic model (ontology) — earned from evidence, human-certified"]
    direction LR
    member["Member ✔ certified<br/>memberId · name · plan · status"]:::obj
    claim["Claim ✔ certified<br/>claimId · amount · status · serviceDate"]:::obj
    provider["Provider ✔ certified<br/>providerId · name · network"]:::obj
    member -->|"files · link conf 0.95*"| claim
    provider -->|"renders · link conf 0.88*"| claim
  end
  ev ==>|"earns + scores every Object, Link & Property"| model

  classDef obj fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef ev fill:#e8a33d,stroke:#b97e22,color:#2b2b2b;
  style model fill:#eef4fb,stroke:#0b4884,color:#0b4884
```
> **Objects** (business entities) carry certified **Properties**; **Links** are certified and confidence-scored (governed traversal, ADR 0006/0024). The whole model is *earned* from evidence, not hand-declared. Ontology **Actions / write-backs** (e.g. AdjudicateClaim) are **deferred**. *(confidence numbers illustrative)*

## 7 · Context-packet anatomy ("perfect context for AI")

```mermaid
flowchart LR
  subgraph packet["Context packet — one Object (e.g. Claim) · ontology.get_context (ADR 0027)"]
    direction TB
    props["Properties + provenance<br/>the semantic meaning of each field"]:::part
    backing["Backing Task(s) + column lineage<br/>how the table is produced & kept fresh"]:::part
    gov["Governance<br/>certification · connection capabilities"]:::part
    fresh["Data freshness<br/>latest DQ score + profile time"]:::part
    links["Certified Links<br/>Member · Provider (governed traversal)"]:::part
    sample["(optional) live sample<br/>bounded real rows via secure agent"]:::partopt
    conf["Packet confidence<br/>reinforcement-aware (EO-0, in progress)"]:::conf
    traces["Action decision-traces<br/>(part of the design — inactive while Actions deferred)"]:::deferred
  end
  agent["AI agent / LLM"]:::person
  packet ==>|"ONE read, not ~8 stitched calls"| agent
  agent -->|"reasons grounded, not guessing"| out["Trustworthy answer / analysis"]:::ok

  classDef part fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef partopt fill:#1168bd,stroke:#0b4884,color:#fff,stroke-dasharray:4 4;
  classDef conf fill:#e8a33d,stroke:#b97e22,color:#2b2b2b;
  classDef deferred fill:#eef1f4,stroke:#aab2bd,stroke-dasharray:4 4,color:#5f6b78;
  classDef person fill:#08427b,stroke:#052e56,color:#fff;
  classDef ok fill:#2e7d32,stroke:#1b5e20,color:#fff;
  style packet fill:#eef4fb,stroke:#0b4884,color:#0b4884
```
> What "give AI the perfect context" concretely means: a single governed read that hands the agent everything to reason about one business Object — assembled deterministically from existing evidence (built, ADR 0027). **Packet confidence** is the in-flight piece (EO-0). **Action decision-traces** are part of the packet design but inactive while Actions are deferred. `ontology.find_context` does the same from a natural-language query (deterministic ranking, no LLM).

---

## Target state (proposed) — 2026-06-24

> **Proposed, NOT built.** Derived from 2026-06-24 market research (`/last30days` + web; see [ADR 0029](../adr/0029-the-target-state-context-layer-is-hybrid-retrieval-plus-cited-answers-read-only.md)). The market converged on datadex's exact category ("active ontology" + "context layer"); these diagrams show the four read-side gaps to become best-in-class.
> **Purple = net-new in the target state.** Scope unchanged: read-only (ontology Actions / write-backs stay deferred). Wedge to keep legible on every frame: **evidence-built + human-certified + agent-only**.

### 8 · Retrieval architecture — current vs proposed

**8a — current (graph-only)**

```mermaid
flowchart TB
  q["Question / agent intent"]:::person
  subgraph surface["Context layer TODAY (over MCP)"]
    direction TB
    onto["Active ontology<br/>evidence · drift · confidence · human-certified"]:::container
    find["find_context (deterministic rank)<br/>+ get_context (assemble packet)"]:::container
    gtrav["Graph traversal<br/>certified ontology Links"]:::container
    find --> gtrav
    onto --> gtrav
  end
  q --> find
  gtrav ==>|"context packet (metadata)"| out["AI reasons over the packet"]:::ok

  classDef person fill:#08427b,stroke:#052e56,color:#fff;
  classDef container fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef ok fill:#2e7d32,stroke:#1b5e20,color:#fff;
  style surface fill:#eef4fb,stroke:#0b4884,color:#0b4884
```

**8b — proposed (hybrid retrieval + cited answer)**

```mermaid
flowchart TB
  q["Question / agent intent"]:::person
  subgraph surface["Governed context surface PROPOSED (over MCP)"]
    direction TB
    onto["Active ontology<br/>evidence · drift · confidence · human-certified"]:::container
    router["Adaptive router<br/>pick strategy per query"]:::proposed
    subgraph retr["Hybrid retrieval core"]
      direction LR
      gtrav["Graph traversal<br/>ontology Links (today)"]:::container
      vector["Semantic / vector retrieval<br/>over backing tables & docs"]:::proposed
      epis["Episodic memory<br/>past answers & decisions"]:::proposed
    end
    answer["context.answer<br/>retrieve → graph-expand → answer + MANDATORY citations, as-of date"]:::proposed
  end
  q --> router
  onto --> gtrav
  router --> gtrav
  router --> vector
  router --> epis
  gtrav --> answer
  vector --> answer
  epis --> answer
  answer ==>|"grounded · cited · point-in-time"| out["Trustworthy answer"]:::ok
  answer -.->|"answer becomes new evidence"| epis

  classDef person fill:#08427b,stroke:#052e56,color:#fff;
  classDef container fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef proposed fill:#6a4c93,stroke:#4a2c73,color:#fff;
  classDef ok fill:#2e7d32,stroke:#1b5e20,color:#fff;
  style surface fill:#eef4fb,stroke:#0b4884,color:#0b4884
  style retr fill:#f3eefb,stroke:#6a4c93,color:#6a4c93
```

### 9 · Context packet — current vs proposed

**9a — current**

```mermaid
flowchart LR
  subgraph packet["Context packet TODAY — one Object"]
    direction TB
    props["Properties + provenance"]:::part
    backing["Backing Task(s) + column lineage"]:::part
    gov["Governance · certification · capabilities"]:::part
    fresh["Data freshness · DQ + profile time"]:::part
    links["Certified Links (graph traversal)"]:::part
    conf["Packet confidence (EO-0, in progress)"]:::conf
    traces["Action decision-traces (deferred)"]:::deferred
  end
  packet ==>|"assembled, metadata-only"| out["AI reasons over the packet"]:::ok

  classDef part fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef conf fill:#e8a33d,stroke:#b97e22,color:#2b2b2b;
  classDef deferred fill:#eef1f4,stroke:#aab2bd,stroke-dasharray:4 4,color:#5f6b78;
  classDef ok fill:#2e7d32,stroke:#1b5e20,color:#fff;
  style packet fill:#eef4fb,stroke:#0b4884,color:#0b4884
```

**9b — proposed (context packet++)**

```mermaid
flowchart LR
  subgraph packet["Context packet++ PROPOSED — one Object, as-of a date"]
    direction TB
    props["Properties + provenance"]:::part
    backing["Backing Task(s) + column lineage"]:::part
    gov["Governance · certification · capabilities"]:::part
    fresh["Data freshness · DQ + profile time"]:::part
    links["Certified Links (graph traversal)"]:::part
    vecs["Semantic neighbours (vector retrieval)"]:::proposed
    epis["Episodic memory (prior answers / decisions)"]:::proposed
    temporal["Point-in-time as-of (event + ingestion time)"]:::proposed
    conf["Packet confidence (reinforcement-aware, EO-0)"]:::conf
    traces["Action decision-traces (deferred)"]:::deferred
  end
  packet ==>|"context.answer → cited, grounded"| out["Trustworthy answer / analysis"]:::ok

  classDef part fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef proposed fill:#6a4c93,stroke:#4a2c73,color:#fff;
  classDef conf fill:#e8a33d,stroke:#b97e22,color:#2b2b2b;
  classDef deferred fill:#eef1f4,stroke:#aab2bd,stroke-dasharray:4 4,color:#5f6b78;
  classDef ok fill:#2e7d32,stroke:#1b5e20,color:#fff;
  style packet fill:#eef4fb,stroke:#0b4884,color:#0b4884
```

> **The four gaps, sequenced value-first (ADR 0029):** (1) `context.answer` cited synthesis - BUILD, first; (2) hybrid vector retrieval + adaptive router - vector store ADOPT-at-boundary; (3) temporal / as-of packet; (4) episodic / decision memory - where the deferred Action-traces later plug in.

---

## Deep-dive diagrams — 2026-06-25 (code-verified current · market-researched target)

> The **current** halves below are verified against the running code (file refs inline), not the pitch deck. The **proposed** halves are derived from 2026 market research — agent zero-trust / fine-grained authz, the "active ontology + context layer" category, and self-healing-pipeline patterns. **Purple = net-new in the target state.** The wedge stays legible on every frame: **evidence-built + human-certified + agent-only**, and the human-only governance gates stay human-only even as autonomy grows.
> Sources: [Cerbos — MCP & zero-trust](https://www.cerbos.dev/blog/mcp-and-zero-trust-securing-ai-agents-with-identity-and-policy) · [Zero-Trust Identity for Agentic AI (arXiv 2505.19301)](https://arxiv.org/html/2505.19301v2) · [Atlan — Active Ontology, the 2026 default](https://atlan.com/know/what-is-active-ontology/) · [Ontologies, Context Graphs & Semantic Layers](https://contextandchaos.substack.com/p/ontologies-context-graphs-and-semantic) · [Self-healing data pipelines (6-agent pattern)](https://medium.com/codetodeploy/agentic-data-infrastructure-i-built-a-self-healing-data-pipeline-system-8eb87f16f30e).

### 10 · Trust & security model — current vs proposed

**10a — current (code-verified)**

```mermaid
flowchart TB
  ai["AI client / agent (MCP)"]:::person
  op["Operator (human)"]:::person
  subgraph control["Control plane — metadata only, never touches client data"]
    direction TB
    gw["MCP gateway + API<br/>RBAC permission check on every call"]:::container
    rbac["Roles → permissions<br/>owner · admin · editor · viewer"]:::container
    humanonly["HUMAN-ONLY grants<br/>promotions:approve · production:write_direct<br/>stripped from every service account — no agent/key can hold them"]:::gate
    sec["Secrets encrypted at rest<br/>Fernet whole-blob · k1: versioned · write-only in flight"]:::container
    audit["Append-only audit<br/>every governed action · secrets redacted"]:::container
    store[("Metadata store<br/>Postgres, encrypted")]:::store
    gw --> rbac --> humanonly
    gw --> audit
    rbac --> store
    sec --> store
  end
  subgraph cust["Customer environment"]
    worker["Secure agent (worker pool)<br/>token ddx_agent_… stored hashed · rotatable"]:::container
    client[("Client systems")]:::external
    worker ==>|"the ONLY path to bulk data"| client
  end
  llm["LLM provider"]:::external
  op --> gw
  ai --> gw
  worker -->|"OUTBOUND HTTPS only — pull queue + heartbeat<br/>no inbound firewall holes"| gw
  gw -->|"schema + bounded samples — never full datasets"| llm

  classDef person fill:#08427b,stroke:#052e56,color:#fff;
  classDef container fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef gate fill:#e8a33d,stroke:#b97e22,color:#2b2b2b;
  classDef store fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef external fill:#8a8a8a,stroke:#5f5f5f,color:#fff;
  style control fill:#eef4fb,stroke:#0b4884,color:#0b4884
  style cust fill:#fbf2e9,stroke:#cc6600,stroke-dasharray:5 5,color:#cc6600
```
> The trust story in one read: **bulk data never leaves the customer env** (only the secure agent touches it, reaching *out* over HTTPS — no inbound holes); the **control plane is metadata-only**; secrets are **encrypted write-only**; and the two grants that admit logic to production (`promotions:approve`, `production:write_direct`) are **human-only by construction** — stripped from every service account, so no agent or API key can cross them (`store_primitives.py` · `secrets.py` · `store_mixins/agents.py`).

**10b — proposed (agentic zero-trust target state)**

```mermaid
flowchart TB
  ai["AI client / agent (MCP)"]:::person
  subgraph control["Governed control plane (target state)"]
    direction TB
    pdp["Policy decision point — policy-as-code<br/>authorize tool + PARAMETERS + context, not just role"]:::proposed
    jit["Just-in-time agent credentials<br/>scope = action · data-handle · minutes-TTL · job-tied revoke"]:::proposed
    ident["First-class agent identity + delegation<br/>subject = user · actor = agent · spawned-by provenance"]:::proposed
    humanonly["HUMAN-ONLY gates — UNCHANGED<br/>promotions:approve · production:write_direct"]:::gate
    behav["Behavioral monitoring<br/>scope-deviation · toolset-violation (extends agent-trace scorer)"]:::proposed
    kms["External KMS / vault at the secrets seam<br/>+ BYOC / confidential-compute residency"]:::proposed
    audit["Verifiable, attributable audit<br/>who did what, on whose behalf — full delegation chain"]:::proposed
    pdp --> humanonly
    jit --> ident
    ident --> behav
  end
  worker["Secure agent — still OUTBOUND-only, still the only data path (UNCHANGED)"]:::container
  ai --> pdp
  worker --> jit
  pdp -.->|"every decision logged"| audit
  behav -.->|"anomaly → revoke session"| jit
  kms --> audit

  classDef person fill:#08427b,stroke:#052e56,color:#fff;
  classDef container fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef gate fill:#e8a33d,stroke:#b97e22,color:#2b2b2b;
  classDef proposed fill:#6a4c93,stroke:#4a2c73,color:#fff;
  style control fill:#eef4fb,stroke:#0b4884,color:#0b4884
```
> Where the market is going (Cerbos / Microsoft Entra Agent ID / arXiv 2505.19301): coarse role-strings give way to a **policy decision point** that authorizes the *parameters* of a tool call, **short-lived job-scoped credentials** replace the long-lived bearer token, agents get a **first-class identity with subject↔actor delegation provenance**, and **behavioral monitoring** (the agent-trace scorer is the seed) revokes a misbehaving session. Note what does **not** change: outbound-only data path, metadata-only control plane, and the human-only production gates.

### 11 · Where datadex sits — competitive map + the wedge

**11a — the 2026 landscape (positioning)**

```mermaid
quadrantChart
    title Enterprise data-to-AI landscape 2026 - where datadex sits
    x-axis Hand-declared model --> Auto-built from evidence
    y-axis Serves BI dashboards --> Serves AI agents
    quadrant-1 Active ontology for agents
    quadrant-2 Ontology for agents, hand-built
    quadrant-3 Classic semantic layer
    quadrant-4 Auto catalog and warehouse AI
    Palantir Foundry: [0.18, 0.82]
    Atlan: [0.55, 0.64]
    Glean: [0.42, 0.74]
    dbt and Cube: [0.18, 0.22]
    Snowflake and Databricks: [0.5, 0.34]
    Informatica and Collibra: [0.36, 0.44]
    datadex: [0.88, 0.9]
```
> The prize quadrant is **top-right: an active ontology that serves agents AND is built automatically.** Palantir owns the agent-ontology idea but builds it by hand (forward-deployed engineers — doesn't scale to a normal data team); Atlan/Collibra automate but stay catalog-first; dbt/Cube standardise *metrics*, not entity *meaning*. datadex is the only one earning the model from evidence while keeping it agent-first.

**11b — the wedge (why datadex wins its corner)**

```mermaid
flowchart TB
  subgraph gaps["What each neighbour is missing"]
    direction TB
    g1["Palantir Foundry<br/>ontology for agents — but FDE-built, proprietary, doesn't scale"]:::external
    g2["Atlan / Collibra<br/>active ontology — but catalog-first, declared not evidence-earned"]:::external
    g3["dbt / Cube<br/>semantic layer — standardises metrics, not entity meaning"]:::external
    g4["Snowflake / Databricks<br/>warehouse-native AI — strong, but locked to one engine"]:::external
  end
  subgraph wedge["datadex wedge — three things, together"]
    direction TB
    w1["EVIDENCE-BUILT<br/>earned from joins · lineage · profiling · usage — scales without FDEs"]:::ok
    w2["HUMAN-CERTIFIED<br/>nothing goes live unreviewed — trustworthy, not pure-LLM auto"]:::ok
    w3["AGENT-ONLY · ENGINE-AGNOSTIC<br/>sits read-only over your existing stack via the secure agent"]:::ok
  end
  market["Market converged on this exact category<br/>'active ontology' + 'context layer' = the 2026 default"]:::proposed
  osi["Open Semantic Interchange (OSI v1.0, Jan 2026)<br/>CONFORM at the boundary — interop, not lock-in"]:::proposed
  gaps ==>|"the gap datadex fills"| wedge
  market -.->|"validates the bet"| wedge
  wedge --> osi

  classDef external fill:#8a8a8a,stroke:#5f5f5f,color:#fff;
  classDef ok fill:#2e7d32,stroke:#1b5e20,color:#fff;
  classDef proposed fill:#6a4c93,stroke:#4a2c73,color:#fff;
  style gaps fill:#f4f4f4,stroke:#8a8a8a,color:#5f5f5f
  style wedge fill:#eef9ef,stroke:#1b5e20,color:#1b5e20
```
> No single competitor is doing all three at once. Each leg alone is matchable; **the combination is the moat** — and OSI gives us a standards-based on-ramp (conform at the boundary) instead of a lock-in fight. (Atlan publicly reports up to 5× AI-analyst accuracy from a context layer — the category's value is already market-proven.)
>
> **→ Full competitive deep dive** (5-camp map, capability matrix, per-competitor breakdown, honest gaps, cross-question bank): [`COMPETITIVE.md`](./COMPETITIVE.md).

### 12 · The agent's build → operate → self-heal loop — current vs proposed

**12a — current (code-verified)**

```mermaid
flowchart LR
  subgraph build["BUILD — intelligence (BI-1/2/3, ADR 0023)"]
    direction TB
    b1["Infer load strategy from the data<br/>full · incremental · merge"]:::container
    b2["Quality-in-build<br/>checks authored with the pipeline"]:::container
    b3["Idempotent builds<br/>re-runnable · exactly-once on a declared key"]:::container
  end
  subgraph operate["OPERATE — self-heal triage (Track 2)"]
    direction TB
    detect["Detect<br/>run fails → typed error (taxonomy)"]:::container
    diagnose["Diagnose<br/>deterministic lineage walk → EARLIEST broken producer"]:::container
    propose["Propose remediation<br/>(error · root-cause · quality · load) → ONE governed fix"]:::container
    detect --> diagnose --> propose
  end
  human["Human approves<br/>one-click: run.retry · task.update · incident.create"]:::gate
  build ==> operate
  propose -->|"auto = False — invariant"| human
  human -.->|"approved fix applied"| operate

  classDef container fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef gate fill:#e8a33d,stroke:#b97e22,color:#2b2b2b;
  style build fill:#eef4fb,stroke:#0b4884,color:#0b4884
  style operate fill:#eef4fb,stroke:#0b4884,color:#0b4884
```
> The agent already does the hard part most "AI data engineer" demos skip: when a gold run fails because silver wrote 0 rows because bronze broke, it walks the lineage DAG to the **earliest** broken producer and proposes the fix *there*, not at the symptom — then stops. `propose_remediation` carries an **`auto = False` invariant**: it presents "here's the fix I propose — approve it," it never self-applies (`codegen.py: diagnose_upstream_root_cause` + `propose_remediation`).

**12b — proposed (self-healing target state, wedge intact)**

```mermaid
flowchart TB
  p1["Proactive monitor (target)<br/>freshness · row-count · drift baselines<br/>catch INVISIBLE failures, not just run errors"]:::proposed
  detect["Detect — typed error (today)"]:::container
  diagnose["Diagnose — lineage root-cause (today)"]:::container
  blast["Blast-radius gate (target)<br/>deterministic BFS over lineage → LOW / MED / HIGH"]:::proposed
  propose["Propose ONE governed remediation (today)"]:::container
  gaten["Escalation gate (target)<br/>auto-eligible ONLY if recurrence-certified AND low-blast"]:::proposed
  human["HUMAN gates — UNCHANGED<br/>novel · high-blast · auth · production boundary"]:::gate
  auton["Narrow autonomous zone (target)<br/>a human certifies a RECURRING fix ONCE → it may self-apply<br/>recurrence-gated · reversible · fully audited"]:::proposed
  verify["Verify-after-fix (target)<br/>re-check result; max 3 tries, different strategy each"]:::proposed
  mttr["MTTR + % auto-resolved (target)<br/>operational proof, extends agent-trace scorer"]:::proposed
  p1 --> detect --> diagnose --> blast --> propose --> gaten
  gaten -->|"novel · high-blast · governed boundary"| human
  gaten -->|"recurring · low-blast · pre-certified"| auton
  auton --> verify
  human --> verify
  verify -.->|"outcome → pattern memory + metrics"| mttr

  classDef container fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef gate fill:#e8a33d,stroke:#b97e22,color:#2b2b2b;
  classDef proposed fill:#6a4c93,stroke:#4a2c73,color:#fff;
```
> The market's best self-healing systems add four things datadex is well-placed for: a **proactive monitor** (catch freshness/row-count drift, not only run failures), a **deterministic blast-radius gate** (we already have lineage + impact), a **verify-after-fix loop**, and **MTTR / %-auto metrics**. The one genuinely new policy is a **narrow autonomous zone** — and datadex takes it on-brand: a fix may self-apply *only* after a human has **certified that recurring pattern once** (recurrence-gated, the same DIAL-KG mechanism behind evidence-driven links). That keeps the documented stance — *governed proposals, no ungoverned background autonomy* — while still earning the MTTR wins. Human-only production gates never become machine-eligible.

### 13 · Pipeline DAG execution — how a pipeline actually runs (current, code-verified)

> Grounded in `routes/execution.py`. A pipeline is a **DAG of Tasks** (nodes) joined by **edges** (`from_task_id → to_task_id`). Execution is **control-plane orchestrated, agent-executed**: the control plane decides *what is ready*, the secure agent (the only tier that touches client data) *does the work*. Nothing here is aspirational — these are the real functions.

**13a — DAG structure & dependency-gated advance**

```mermaid
flowchart TB
  pre["Preflight (control plane)<br/>validate each Task · find ROOTs (no upstream edge) · approval gate<br/>same-pipeline 'table made upstream' blockers deferred to run time"]:::ctl
  subgraph dag["Pipeline DAG — Tasks joined by edges (from_task_id → to_task_id)"]
    direction TB
    r1["claims_ingest<br/>ROOT"]:::root
    r2["members_ingest<br/>ROOT"]:::root
    s1["claims_silver<br/>sql_transform"]:::task
    j1["member_join<br/>sql_transform"]:::task
    g1["claims_gold + DQ checks<br/>sql_transform"]:::task
    r1 --> s1
    r1 --> j1
    r2 --> j1
    s1 --> g1
    j1 --> g1
  end
  rule["Advance rule (recompute on every completion)<br/>queue a downstream Task ONLY when ALL its upstreams SUCCEEDED<br/>· data-dependency gated · independent branches run in parallel"]:::note
  roll["Roll up the parent run<br/>succeeded · partial · failed · cancelled"]:::ctl
  pre ==>|"queue the ROOT Tasks"| dag
  dag --- rule
  dag ==>|"all children terminal"| roll

  classDef ctl fill:#34516e,stroke:#1f3146,color:#fff;
  classDef root fill:#2e7d32,stroke:#1b5e20,color:#fff;
  classDef task fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef note fill:#fbf2e9,stroke:#cc6600,color:#cc6600;
  style dag fill:#eef4fb,stroke:#0b4884,color:#0b4884
```
> Roots start first; `member_join` waits for **both** `claims_ingest` and `members_ingest`; `claims_gold` waits for both of its parents. If a Task **fails**, the per-failure policy decides the blast: **`skip_downstream`** (default) BFS-cancels only that Task's not-yet-started descendants — independent branches keep running; **`halt`** cancels every un-started Task in the run; **`continue`** cancels nothing. A *running* Task is never force-cancelled — it's left to finish. A failing **critical DQ check** fails the run (and cascades as a failure); a warning is advisory.

**13b — one Task-run's lifecycle (through the secure agent)**

```mermaid
flowchart LR
  q["queued<br/>task_run row · pipeline_run_id"]:::state
  claim["claimed by a secure agent<br/>atomic UPDATE-return + LEASE (TTL)"]:::state
  runs["running<br/>extract · load · transform on CLIENT systems<br/>heartbeat renews the lease"]:::state
  ok["succeeded<br/>records_written"]:::ok
  fail["failed<br/>typed error_type"]:::bad
  adv["advance DAG / recompute<br/>queue newly-unblocked downstream"]:::ctl
  pol["apply on_task_failure<br/>skip_downstream · halt · continue"]:::ctl
  reap["reaper<br/>lease expired or timed out → fail<br/>(sharded run → re-queue, resume from checkpoint)"]:::ctl
  roll["roll up parent run"]:::ctl
  q -->|"agent polls queue · not_before gate"| claim --> runs
  runs --> ok
  runs --> fail
  ok ==> adv
  fail -->|"policy decides descendants"| pol
  fail -.->|"transient + retries left (opt-in)"| q
  runs -.->|"agent stops renewing lease"| reap
  reap --> fail
  adv --> roll
  pol --> roll

  classDef state fill:#1168bd,stroke:#0b4884,color:#fff;
  classDef ok fill:#2e7d32,stroke:#1b5e20,color:#fff;
  classDef bad fill:#b23b3b,stroke:#7f2626,color:#fff;
  classDef ctl fill:#34516e,stroke:#1f3146,color:#fff;
```
> The control plane only ever *queues* work; the **agent claims it with a lease and reaches OUT** (no inbound path) to run extract/load/transform on the client systems, renewing the lease via heartbeat. If the agent dies mid-run the **reaper** catches the expired lease and fails or re-queues it (a sharded run resumes mid-file from its committed checkpoint, ADR 0012). A just-failed run that is *transient* and has retries left re-enters `queued` behind a `not_before` backoff (auto-retry is opt-in). Every terminal child triggers a roll-up of the parent pipeline run.
