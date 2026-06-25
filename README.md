# datadex architecture diagrams

Diagrams-as-code for **datadex** — an automatic semantic + context layer that sits on top
of your existing data stack, auto-builds an evidence-driven ontology, and gives AI the
perfect **read** context to reason over your business.

**Single source: [`MERMAID.md`](./MERMAID.md).** Every diagram is authored in
[Mermaid](https://mermaid.js.org/), so it renders natively on GitHub — just open the file.
To produce a polished visual, paste any block into an AI / code-rendering tool
(Claude, ChatGPT, Eraser, Napkin, Excalidraw, [mermaid.live](https://mermaid.live)).

## Scope

datadex's current scope is **(1) auto-build a good evidence-driven ontology**
(Objects · Properties · Links) **+ (2) give AI the perfect READ context** (the Context
packet). Ontology **Actions / write-backs are deferred** — the AI reads and reasons, it
does not yet write back to client systems. Every diagram reflects this read-only scope.

The legible wedge on every frame: **evidence-built + human-certified + agent-only.**

## What's inside `MERMAID.md`

A brand palette + a cheatsheet (so you can author new diagrams in the same style), then
16 diagrams in four groups:

| # | Diagram | What it shows |
|---|---------|---------------|
| 1 | Where we sit | datadex sits *on top of* Informatica/Databricks/Snowflake — it doesn't replace them |
| 2 | How it works | The value spine: connect → observe → propose → **human certify** → brain → reason |
| 3 | The living loop | The drift answer: drift → confidence drops → re-propose → re-certify |
| 4 | Build status | "You are here" — built vs in-progress vs deferred |
| 5 | Architecture (current) | Control plane (metadata-only) over the data plane (secure agent) |
| 6 | Deployment topology | Data residency — bulk data never leaves the customer environment |
| 7 | Semantic-model shape | The ontology made concrete (Claim · Member · Provider) |
| 8 | Context-packet anatomy | What "perfect context for AI" concretely contains |
| **Target state (proposed)** | | |
| 9 | Retrieval architecture | Current graph-only vs proposed hybrid retrieval + cited answers |
| 10 | Context packet | Current vs an enriched, point-in-time, cited packet |
| **Deep dives (code-verified current · researched target)** | | |
| 11 | Trust & security model | Outbound-only agent, metadata-only plane, human-only production gates; zero-trust target |
| 12 | Competitive map + the wedge | Where datadex sits vs Palantir / Atlan / dbt / warehouse-native AI |
| 13 | Agent build → operate → self-heal | Lineage root-cause + governed remediation; self-healing target |
| 14 | Pipeline DAG execution | How a pipeline actually runs — DAG gating + per-task run lifecycle |

> The **current** diagrams are verified against the running code; the **proposed** halves
> (purple) are derived from 2026 market research and are clearly marked as not-yet-built.

## Rendering notes

- **GitHub** renders every block automatically — no build step, nothing to install.
- Mermaid reserves a few words (`graph`, `end`, `class`, `subgraph`, `style`); avoid them
  as node IDs. Diagram 12a uses Mermaid's `quadrantChart`; the rest are `flowchart`.
