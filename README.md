<div align="center">

# `agent-mermaid`

### Professional Mermaid architecture documentation for Multi-Agent Systems

Generate **evidence-backed, presentation-ready Mermaid diagram packages** from real agent-system code — not just a single pretty chart, but a complete technical deliverable engineers and stakeholders can actually use.

<p>
  <a href="./README.zh-CN.md"><strong>简体中文</strong></a>
</p>

<p>
  <img src="https://img.shields.io/badge/Mermaid-Professional-blueviolet" alt="Mermaid Professional" />
  <img src="https://img.shields.io/badge/Multi--Agent-Systems-1f6feb" alt="Multi-Agent Systems" />
  <img src="https://img.shields.io/badge/Framework-Aware-0a7f5a" alt="Framework Aware" />
  <img src="https://img.shields.io/badge/Output-Deliverable%20Package-f59e0b" alt="Deliverable Package" />
  <img src="https://img.shields.io/badge/Quality-Syntax%20Guard%20%2B%20Self--Repair-8b5cf6" alt="Syntax Guard and Self Repair" />
</p>

</div>

---

> **Built for serious agent documentation.**  
> `agent-mermaid` turns source code for multi-agent systems into a **structured architecture documentation package** with framework-aware diagrams, source-backed relationships, runtime flow views, risk notes, and Mermaid syntax discipline.

## At a glance

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','secondaryColor': '#E3F2FD','tertiaryColor': '#F3E5F5','background': '#FAFAFA','fontSize': '14px'}}}%%
flowchart LR
    A["Read codebase"] --> B["Build inventory"]
    B --> C["Detect framework & pattern"]
    C --> D["Generate diagram suite"]
    D --> E["Validate Mermaid"]
    E --> F["Deliver docs + risks"]
```

## Overview

Most diagram prompts stop at “draw me an architecture chart.”  
`agent-mermaid` does something far more useful:

- reads the provided codebase first
- inventories agents, tools, state, orchestration, memory, async behavior, config, and error handling
- identifies the **actual framework and coordination pattern**
- generates a **scoped Mermaid documentation package**
- distinguishes what is **explicit**, **inferred**, or **unknown**
- validates diagram quality with a dedicated Mermaid syntax guard
- finishes with **architectural notes, risks, and assumptions**

The result is a deliverable you can hand to:

- engineers joining the project
- reviewers evaluating system design
- architects documenting complex agent workflows
- technical stakeholders who need accurate visual explanations

---

## Typical one-shot diagram prompt vs `agent-mermaid`

| Dimension | Typical one-shot diagram prompt | `agent-mermaid` |
|---|---|---|
| Input discipline | Often skims snippets | Reads all provided files first |
| Framework awareness | Usually guessed from context | Detects frameworks via identifying markers |
| Relationship accuracy | Mixes fact and assumption | Separates **Explicit / Inferred / Unknown** |
| Output scope | Usually one chart | Full documentation package |
| Runtime behavior | Often omitted | Includes sequence and control-flow views |
| Syntax reliability | Easy to break Mermaid | Uses syntax guard + self-repair mindset |
| Large systems | Becomes cluttered quickly | Uses scope strategy and C4-style layering when needed |
| Partial code | Often hides uncertainty | Marks unknowns clearly and degrades transparently |
| Architecture review value | Mostly cosmetic | Useful for engineering review and stakeholder communication |

---

## Why this skill stands out

### 1) Full code inventory before drawing
Before a single diagram is produced, the skill performs a structured inventory of:

- **Agents**
- **Tools**
- **State / schema**
- **Orchestration**
- **Memory / persistence**
- **Config**
- **Async / concurrency**
- **Error handling**

That means diagrams are anchored in the real system, not in a vague impression of it.

### 2) Framework and pattern detection by code markers
The skill does not “guess” the architecture style. It matches code against known framework markers and patterns, including graph-based orchestration, message-passing systems, hierarchical delegation, ReAct loops, RAG pipelines, and mixed setups.

### 3) Explicit vs inferred vs unknown discipline
One of the strongest parts of this skill is its accuracy contract:

- **Explicit** relationships are drawn as hard facts
- **Inferred** relationships are visually distinguished and documented
- **Unknown** components are marked as uncertain rather than invented

This makes the output safer and more trustworthy for real technical use.

### 4) Mermaid syntax guard and self-repair mindset
The repository includes a dedicated syntax guard covering:

- `%%{init}` placement
- label escaping
- subgraph rules
- edge labeling
- activation balancing
- complexity thresholds
- fallback and repair strategy when rendering gets fragile

This is a major advantage over generic Mermaid generation.

### 5) Scope and degradation strategy
Not every project deserves eight diagrams.  
This skill decides what is warranted based on code size, complexity, identified features, and certainty level—favoring **fewer excellent diagrams over more broken ones**.

### 6) Professional deliverable structure
The output is not “just Mermaid.” It is a documentation package with:

- a deliverable header
- a system inventory
- a diagram index
- cited source excerpts
- reading guides
- inference notes
- architectural strengths, risks, and assumptions

### 7) Handles the realities of modern agent systems
The skill is built to cover not just the happy-path architecture, but also:

- large systems
- mixed frameworks
- partial codebases
- async and parallel execution
- memory / checkpointing / vector retrieval
- routing logic
- retries and fallback paths
- error and recovery flows

---

## What you get

| Deliverable | What it includes | Why it matters |
|---|---|---|
| Deliverable header | Generated date, source files analyzed, framework, pattern | Establishes context and trust |
| System inventory | Counts and names of agents, tools, models, state fields, APIs, memory | Gives fast architectural orientation |
| Diagram index | Structured map of generated diagrams | Makes the package navigable |
| Mandatory diagrams 1–3 | Architecture, sequence, routing/control flow | Covers structure + runtime + decisions |
| Optional diagrams 4–8 | Class/component, data/state, parallelism, error/recovery, memory lifecycle | Adds depth where the code warrants it |
| Architectural notes & risks | Strengths, anti-patterns, recommendations, assumptions | Turns diagrams into engineering insight |

---

## Diagram suite

The skill defines a scalable 8-diagram suite.

| # | Diagram type | Purpose | When it appears |
|---|---|---|---|
| 1 | System Architecture | Static structural overview of components and relationships | Always |
| 2 | Sequence — Primary Flow | Happy-path runtime behavior step by step | Always |
| 3 | Routing & Control Flow | Decision logic, branching, retries, termination | Always when conditional behavior exists; effectively part of the mandatory core |
| 4 | Class / Component Structure | Class hierarchy, composition, schema modeling | When OOP structure or Pydantic-style models exist |
| 5 | Data / State Transformation | How data and state evolve across the pipeline | When explicit schema/state transformations exist |
| 6 | Parallel / Concurrent Execution | What runs concurrently and how results join | When async, parallel fan-out, or concurrent workers exist |
| 7 | Error & Recovery Flow | Dedicated failure, retry, fallback, and recovery behavior | When error handling exists |
| 8 | Memory & Persistence Lifecycle | Checkpointing, vector retrieval, memory read/write timing | When memory, persistence, or retrieval layers exist |

---

## Framework and pattern coverage

`agent-mermaid` is designed for real-world multi-agent ecosystems and mixed architectures.

### Supported ecosystems
- **LangGraph**
- **AutoGen / AG2**
- **CrewAI**
- **Pydantic AI**
- **DSPy**
- **Semantic Kernel**
- **LlamaIndex Agents**
- **OpenAI Swarm / Handoff**
- **Custom architectures**
- **Mixed-framework systems**

### Supported patterns and behaviors
- **Supervisor / Worker**
- **Pipeline**
- **ReAct loops**
- **Group chat / message-passing**
- **Swarm / handoff**
- **RAG pipelines**
- **Human-in-the-loop (HITL)**
- **Streaming**
- **Parallel fan-out / aggregation**
- **Checkpointed / memory-backed flows**

---

## Accuracy contract

This skill is intentionally strict about what it claims.

| Classification | Meaning | Diagram treatment |
|---|---|---|
| **Explicit** | Directly expressed in source code | Solid relationships |
| **Inferred** | Implied by naming, structure, or framework behavior | Dashed relationships + documented inference |
| **Unknown** | Referenced but not present in provided files | Marked with uncertainty instead of invented detail |

This contract is one of the key reasons the output remains reliable under real engineering scrutiny.

---

## How it works

### 1. Read everything
The skill begins with a full pre-analysis pass over the provided files and builds a mental inventory of the system.

### 2. Identify framework via `references/patterns.md`
It compares the source against framework markers to classify the architecture correctly and choose the right diagram template as the starting point.

### 3. Choose scope
Based on project size and clarity, it decides whether to generate only the mandatory core or a richer diagram package.

### 4. Generate diagrams
It produces architecture, runtime, routing, and—when justified—class, data, concurrency, error, and memory views.

### 5. Validate with `references/syntax-guard.md`
Before output, Mermaid is checked against syntax and rendering pitfalls, with a built-in self-repair approach for complex diagrams.

### 6. Add notes and risks
The package concludes with architectural strengths, risk findings, assumptions, and recommendations.

---

## Prompt ideas

Here are example prompts you can use with a skill-enabled assistant:

- **Generate a full Mermaid architecture package for this multi-agent repository.**
- **Analyze this LangGraph project and produce architecture, sequence, and routing diagrams.**
- **Visualize my AutoGen group chat flow and show runtime interactions step by step.**
- **Create a professional Mermaid documentation package for this CrewAI workflow.**
- **Document this mixed-framework agent system and separate explicit vs inferred relationships.**
- **Generate diagrams for memory, parallel execution, and error recovery in this agent project.**
- **Review this agent architecture and include risks, assumptions, and recommendations.**
- **I only provided partial code—generate the best possible architecture package and mark unknowns clearly.**

---

## Best-fit use cases

`agent-mermaid` shines when you need documentation that is both **accurate** and **presentation-ready**.

### Ideal scenarios
- Architecture reviews for multi-agent systems
- Technical onboarding for new engineers
- Design documentation for internal platforms
- Stakeholder-friendly system explanations
- Refactoring and migration planning
- Debugging complex orchestration and routing logic
- Comparing framework patterns across agent implementations
- Turning code into diagrams without hand-authoring Mermaid from scratch

### Especially valuable when
- the codebase spans multiple files
- the system has supervisor/worker or routing logic
- concurrency or memory behavior matters
- the architecture needs to be explained beyond raw source code
- a simple one-chart answer would be misleading

---

## Repository layout

```text
agent-mermaid-skill/
├─ SKILL.md
└─ references/
   ├─ patterns.md
   ├─ syntax-guard.md
   └─ real-example.md
```

### File roles

| File | Role |
|---|---|
| `SKILL.md` | Core skill definition, workflow, diagram specs, quality gate, styling guidance |
| `references/patterns.md` | Framework identification markers and canonical architecture templates |
| `references/syntax-guard.md` | Mermaid syntax pitfalls, prevention rules, and self-repair checklist |
| `references/real-example.md` | End-to-end quality benchmark from source code to finished deliverable |

---

## Presentation quality and styling

This skill is designed not only for correctness, but also for **clear visual communication**.

### Styling principles
- **Light theme by default** for GitHub and documentation-friendly rendering
- **Dark theme available on request** for presentations and dark-mode environments
- structured `classDef` palettes for consistent node semantics
- styled edges for control flow, tool calls, state access, and error paths
- Mermaid blocks that aim to be both expressive and renderer-safe

In other words: the diagrams are meant to look professional **and** survive real Markdown/Mermaid environments.

---

## Design philosophy

- **Accuracy over decoration**
- **Evidence over guesswork**
- **Scope-aware generation over diagram spam**
- **Professional documentation over toy examples**
- **Transparent uncertainty over invented architecture**

---

## Why it is compelling

A lot of diagram generators can make something that *looks* like architecture.

`agent-mermaid` is different because it aims to produce something that can stand up in front of:

- an engineering team
- an architecture review
- a technical lead
- a product stakeholder
- a documentation handoff

It is code-first, framework-aware, syntax-conscious, and structured for serious delivery.

---

## Final word

**If you want Mermaid output that is accurate enough for engineers, polished enough for architects, and clear enough for technical stakeholders, `agent-mermaid` is the skill you want in the loop.**