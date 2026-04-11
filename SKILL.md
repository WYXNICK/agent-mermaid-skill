---
name: agent-mermaid
description: |
  Generate professional-grade Mermaid diagram documentation packages from Agent System source code. Use for any Agent System: LangGraph, AutoGen, CrewAI, Swarm, Pydantic AI, DSPy, Semantic Kernel, LlamaIndex, ReAct, RAG pipelines, or custom architectures. Triggers on: "generate diagram", "mermaid chart", "diagram my code", "architecture diagram", "sequence diagram", "document agent system", "agent flow", "show interactions", "visualize agent", or whenever project code is provided with any request for visual documentation or architecture review. Always use for multi-file agent projects — even if user says "draw this" or "what does this look like". Output: complete deliverable package with system inventory, styled Mermaid diagrams (%%{init} themes, classDef node types, linkStyle edge types), source citations, error/parallel/memory diagrams, and architectural risk notes.
---

# Agent System — Professional Mermaid Documentation Generator

Produce a **complete, deliverable documentation package** from Agent System source code. The output must be accurate enough to be used by engineers who didn't write the code, and professional enough to be presented to technical stakeholders.

---

## Step 0: Pre-Analysis — Read Everything First

Before drawing a single diagram, perform a **full code inventory**. Read ALL provided files. Extract and record:

### 0.1 Component Inventory (fill this in mentally before proceeding)

| Category           | Items found                                               | Source file/line |
| ------------------ | --------------------------------------------------------- | ---------------- |
| **Agents**         | names, roles, which LLM each uses                         | —                |
| **Tools**          | names, signatures, external APIs called                   | —                |
| **State/Schema**   | TypedDict fields, Pydantic models, reducers               | —                |
| **Orchestration**  | graph edges, conditional routing logic, FINISH conditions | —                |
| **Memory**         | checkpointers, vector stores, conversation buffers        | —                |
| **Config**         | model names, temperature, max_tokens, API endpoints       | —                |
| **Async**          | async def, asyncio.gather, parallel branches, Send()      | —                |
| **Error handling** | try/except, retries, fallback agents, max_iterations      | —                |

### 0.2 Architecture Classification

Determine before drawing:
- **Pattern type**: Supervisor/Worker | Pipeline | ReAct Loop | GroupChat | Swarm/Handoff | Custom
- **Concurrency model**: Sequential | Parallel branches | Async event-driven
- **State model**: Shared mutable state | Message-passing | Stateless | External DB
- **Memory model**: In-memory | Persisted (checkpointer) | Vector retrieval | None
- **Framework**: LangGraph | AutoGen | CrewAI | Pydantic AI | DSPy | Semantic Kernel | LlamaIndex | Custom | Mixed

> **⚠️ MANDATORY:** Before proceeding, consult `references/patterns.md` to match the source code against each framework's **identifying markers**. Use the matched framework's canonical template as your diagram starting point. Do NOT guess the framework — match by code markers. For ambiguous or mixed-framework projects, identify EACH framework separately.

If framework is ambiguous, look for:
- Graph-based: `add_node`, `add_edge`, `StateGraph`, node functions returning state dicts
- Message-passing: `initiate_chat`, `GroupChat`, reply functions, termination conditions
- Declarative task: `Agent(role=...)`, `Task(description=...)`, `Crew`, `kickoff()`
- Functional/DSP: `@dspy.module`, `Predict`, `ChainOfThought`, `TypedPredictor`

### 0.3 Accuracy Rules — Always Follow

**EXPLICIT** (solid lines in diagrams): relationships directly expressed in code — `add_edge`, `goto`, function calls, class inheritance.

**INFERRED** (dashed lines + footnote): relationships implied by naming, structure, or LLM behavior — e.g., "this agent likely calls the LLM based on its base class."

**UNKNOWN** (node name ends in `?`, dashed border): components referenced but not in provided files.

Never draw a solid arrow for something you inferred. Mark every inference explicitly.

### 0.4 Mermaid Syntax Guard — Mandatory Pre-flight

Before writing any Mermaid code, internalize these critical rules (full reference: `references/syntax-guard.md`):

1. **`%%{init}` must be line 1** of every Mermaid block — no blank lines before it, use single quotes inside JSON
2. **Labels with special characters** → always wrap in `["..."]`: `A["function(arg)"]` not `A[function(arg)]`
3. **Subgraph IDs** → alphanumeric + underscore only: `subgraph AgentLayer` not `subgraph Agent Layer`
4. **Subgraph titles** → use quoted bracket syntax: `subgraph ID["Title With Spaces"]`
5. **Edge labels with spaces** → always quote inside pipes: `-->|"label text"| B`
6. **`classDef` before use** → define ALL `classDef` lines at the top of the diagram, before any node references
7. **No HTML in labels** → use `<br/>` for line breaks in flowcharts, `\n` in sequence participant aliases
8. **30-node limit** → if >30 nodes in a single diagram, split into C4 layers (see Special Cases)
9. **Bracket matching** → every `[`, `(`, `{`, `"` must have a matching closer; every `subgraph`/`loop`/`alt`/`par` must have `end`
10. **Activation balancing** → in sequence diagrams, every `+` (activate) must have a matching `-` (deactivate)

> **After generating each diagram, mentally validate against `references/syntax-guard.md` §8 Quick Reference Checklist before outputting. If a diagram is complex (>15 nodes), run the Self-Repair Protocol from §6 preemptively.**

### 0.5 Scope & Degradation Strategy

Not all projects warrant all 8 diagrams. Determine scope before drawing:

| Condition | Action |
|-----------|--------|
| Code < 50 lines, 1 agent, no tools | Generate Diagram 1 only + Architecture Summary |
| Code < 150 lines, 2-3 agents | Generate Diagrams 1 + 2 + 3 (mandatory set only) |
| Standard project (3-15 components) | Generate Diagrams 1-3 (mandatory) + all applicable optional diagrams |
| Large system (>15 components) | Apply C4 layering (see Special Cases) + all applicable diagrams per layer |
| Cannot identify framework | **Do NOT guess.** Ask: "Is coordination graph-based, message-passing, or hierarchical?" |
| Mixed frameworks detected | Identify each framework separately → draw per-framework diagrams → add unified overview diagram |
| Partial code provided | Add banner: `> ⚠️ Partial analysis — [list missing files]. Nodes marked ? are inferred.` All unknown nodes get `?` suffix + `stroke-dasharray:5 3` |
| Mermaid rendering fails | Apply Self-Repair Protocol (`references/syntax-guard.md` §6): simplify labels → remove special chars → check bracket matching → reduce nodes → split diagrams |

> **Quality over quantity:** It is better to output 3 perfect diagrams than 8 broken ones. If a diagram adds no insight beyond what previous diagrams show, skip it and note why.

---

## Step 1: Generate the Deliverable Header

Always start output with this block, filled in from the code:

```
# Agent System Architecture Documentation
**Generated:** [current date]  **Analyzer:** Claude (agent-mermaid skill)
**Source:** [list filenames analyzed, or "code snippet provided"]
**Framework:** [detected framework(s)]
**Pattern:** [Supervisor/Worker | Pipeline | ReAct | GroupChat | Swarm | Custom]

## System Inventory
| Category | Count | Names |
|---|---|---|
| Agents | N | agent_a, agent_b, ... |
| Tools | N | tool_1, tool_2, ... |
| LLM Models | N | gpt-4o (supervisor), gpt-4o-mini (workers), ... |
| State Fields | N | messages, next, error_count, ... |
| External APIs | N | Tavily, OpenAI, Pinecone, ... |
| Memory/Persistence | type | MemorySaver / SqliteSaver / None |

## Architecture Summary
[2-3 sentences: what this system does, its primary pattern, and the key architectural decision that defines it]

## Diagram Index
| # | Type | Title | Key question answered |
|---|---|---|---|
| 1 | Architecture | [title] | What exists and how is it connected? |
| 2 | Sequence | [title] | What happens at runtime, step by step? |
| 3 | Flow | [title] | How does routing/decision logic work? |
| ... | ... | ... | ... |
```

---

## Step 2: Generate Diagrams

### Mandatory for every project: Diagrams 1 + 2 + 3
### Add 4-8 based on what exists in the code

---

### Diagram 1: System Architecture (always)

**Purpose:** Static structural overview — what exists and how it connects.

**Mermaid requirements:**
- Always open with `%%{init}%%` theme config
- Always define `classDef` for node categories
- Always apply `:::className` to nodes
- Group with `subgraph` blocks: Orchestration | Agents | Tools | Storage | External
- Edge labels must name the relationship type from actual code, not generic arrows

**Template:**
```
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','secondaryColor': '#E3F2FD','tertiaryColor': '#F3E5F5','background': '#FAFAFA','fontSize': '14px'}}}%%
graph TB
    classDef agent        fill:#E8EAF6,stroke:#3949AB,stroke-width:1.5px,color:#1A237E,font-weight:bold
    classDef orchestrator fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#0D47A1,font-weight:bold
    classDef tool         fill:#E8F5E9,stroke:#2E7D32,stroke-width:1px,color:#1B5E20
    classDef external     fill:#FFF3E0,stroke:#E65100,stroke-width:1px,color:#BF360C,stroke-dasharray:4 2
    classDef storage      fill:#F3E5F5,stroke:#6A1B9A,stroke-width:1px,color:#4A148C
    classDef human        fill:#FCE4EC,stroke:#880E4F,stroke-width:1px,color:#880E4F

    User([User]):::human

    subgraph Orchestration["Orchestration Layer"]
        ORCH[supervisor_agent\nroutes to workers\nModel: gpt-4o]:::orchestrator
    end

    subgraph Agents["Worker Agents"]
        A1[researcher_agent\nweb search + synthesis\nModel: gpt-4o-mini]:::agent
        A2[coder_agent\ncode generation\nModel: gpt-4o]:::agent
    end

    subgraph Tools["Tools"]
        T1[tavily_search\nsearch(query) -> Results]:::tool
        T2[python_repl\nexecute(code) -> str]:::tool
    end

    subgraph External["External APIs"]
        EXT1[Tavily API]:::external
        EXT2[OpenAI API]:::external
    end

    subgraph Storage["State & Memory"]
        STATE[(AgentState\nCheckpointer)]:::storage
    end

    User -->|"invoke({'messages': [...]})"| ORCH
    ORCH -->|"Command(goto='researcher')"| A1
    ORCH -->|"Command(goto='coder')"| A2
    ORCH -->|"route: FINISH"| END([__end__])
    A1 -->|"tool_call"| T1
    A2 -->|"tool_call"| T2
    T1 -->|"HTTP GET"| EXT1
    ORCH -.->|"read/write"| STATE
    A1 -->|"Command(goto='supervisor')"| ORCH
    A2 -->|"Command(goto='supervisor')"| ORCH
```

**Node naming rule:** Node IDs and labels must match actual class/function names from source, not descriptions.

---

### Diagram 2: Sequence — Primary Flow (always)

**Purpose:** Runtime message flow for the happy path. Must show: actual method names, activate/deactivate lifelines, LLM calls, tool calls, and state updates.

Critical additions vs basic sequence:
- `rect` blocks to group critical sub-flows
- `note` for non-obvious behavior
- `loop` for ReAct/retry iterations
- `par` for genuinely parallel execution
- `alt`/`else` for conditional paths that exist in code

**Template:**
```
%%{init: {'theme': 'base', 'themeVariables': {'actorBkg': '#E8EAF6','actorBorder': '#3949AB','actorTextColor': '#1A237E','activationBkgColor': '#E3F2FD','activationBorderColor': '#1565C0','noteBkgColor': '#FFFDE7','noteBorderColor': '#F57F17','signalColor': '#546E7A','signalTextColor': '#263238','fontSize': '13px'}}}%%
sequenceDiagram
    autonumber
    actor User
    participant ORCH as supervisor_agent
    participant A1 as researcher_agent
    participant LLM as LLM (gpt-4o)
    participant TOOL as tavily_search
    participant STATE as AgentState

    User->>+ORCH: invoke({"messages": [HumanMessage]})
    ORCH->>STATE: get_tuple(thread_id) — resume or init

    rect rgb(232, 234, 246)
        Note over ORCH,LLM: Supervisor routing decision
        ORCH->>+LLM: [system_prompt] + state["messages"]
        LLM-->>-ORCH: AIMessage(tool_calls=[{next: "researcher"}])
    end

    ORCH->>+A1: Command(goto="researcher")
    Note right of ORCH: State passed implicitly\nvia shared AgentState

    loop ReAct loop (max_iterations=10)
        A1->>+LLM: prompt + scratchpad history
        LLM-->>-A1: Action: tavily_search({"query": "..."})
        A1->>+TOOL: search(query)
        TOOL-->>-A1: ToolMessage(content=results)
        A1->>A1: append to scratchpad
    end

    A1-->>-ORCH: Command(goto="supervisor", update={"messages": [...]})
    ORCH->>STATE: put_tuple — checkpoint saved
    ORCH-->>-User: state["messages"][-1].content
```

---

### Diagram 3: Routing & Control Flow (always when conditional edges exist)

**Purpose:** Decision logic — routing conditions, termination, retries, guards. Must quantify loops and show all branches including error paths.

**Template:**
```
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E3F2FD','primaryTextColor': '#0D47A1','primaryBorderColor': '#1565C0','lineColor': '#546E7A','background': '#FAFAFA'}}}%%
flowchart TD
    classDef decision fill:#FFF3E0,stroke:#E65100,color:#BF360C,font-weight:bold
    classDef process  fill:#E8EAF6,stroke:#3949AB,color:#1A237E
    classDef terminal fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20,font-weight:bold
    classDef error    fill:#FFEBEE,stroke:#C62828,color:#B71C1C,stroke-dasharray:3 2

    START([User Input]):::terminal --> INIT[Init AgentState]:::process
    INIT --> SUP{"route_to_worker(state)\nstate[last_msg].tool_calls"}:::decision

    SUP -->|"tool_calls[0].next == 'researcher'"| A1[researcher_agent]:::process
    SUP -->|"tool_calls[0].next == 'coder'"| A2[coder_agent]:::process
    SUP -->|"not tool_calls → FINISH"| END([Return final state]):::terminal

    A1 --> RETRY{"retry_count < max_retries?"}:::decision
    RETRY -->|yes| A1
    RETRY -->|no| ERR[error_handler]:::error
    ERR --> SUP
    A2 --> SUP
```

---

### Diagram 4: Class / Component Structure (when OOP or Pydantic models exist)

**Purpose:** Class hierarchy, interfaces, composition. Annotate with `<<abstract>>`, `<<TypedDict>>`, visibility modifiers, exact types.

```
%%{init: {'theme': 'base'}}%%
classDiagram
    class AgentState {
        <<TypedDict>>
        +list~AnyMessage~ messages
        +str next
        +int retry_count
        +Optional~str~ error
    }
    note for AgentState "add_messages reducer\napplied to messages field"

    class BaseAgent {
        <<abstract>>
        +str name
        +BaseLanguageModel llm
        +list~BaseTool~ tools
        +run(state: AgentState) Command*
        #_build_prompt(state: AgentState) list~BaseMessage~
    }

    BaseAgent <|-- SupervisorAgent
    BaseAgent <|-- WorkerAgent
    SupervisorAgent "1" --> "1..*" BaseAgent : delegates to
    AgentState --o BaseAgent : consumed and produced
```

---

### Diagram 5: Data / State Transformation (when explicit schema exists)

**Purpose:** Field-level data transformation across the pipeline.

```
%%{init: {'theme': 'base'}}%%
flowchart LR
    classDef schema    fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C
    classDef transform fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20
    classDef io        fill:#FFF3E0,stroke:#E65100,color:#BF360C

    IN["HumanMessage\n────────\ncontent: str\nrole: 'human'"]:::io
    STATE["AgentState\n────────\nmessages: list — add_messages\nnext: str\nretry_count: int"]:::schema
    AI["AIMessage\n────────\ncontent: str\ntool_calls: list[ToolCall]"]:::schema
    TOOL["ToolMessage\n────────\ncontent: str\ntool_call_id: str"]:::schema
    OUT["Final output\n────────\nstate['messages'][-1].content"]:::io

    IN -->|"invoke() wraps in list"| STATE
    STATE -->|"passed as context"| AI
    AI -->|"add_messages appends"| STATE
    AI -->|"tool_calls dispatched"| TOOL
    TOOL -->|"add_messages appends"| STATE
    STATE -->|"__end__ reached"| OUT
```

---

### Diagram 6: Parallel / Concurrent Execution (when async or Send() exists)

**Purpose:** What runs concurrently. Use `par` blocks. Critical for correctness understanding.

```
%%{init: {'theme': 'base'}}%%
sequenceDiagram
    autonumber
    participant ORCH as Orchestrator
    participant A1 as agent_researcher
    participant A2 as agent_analyst
    participant A3 as agent_writer

    ORCH->>ORCH: Send([task_a, task_b, task_c])

    par Parallel worker dispatch
        ORCH->>+A1: invoke(subtask_a)
    and
        ORCH->>+A2: invoke(subtask_b)
    and
        ORCH->>+A3: invoke(subtask_c)
    end

    A1-->>-ORCH: result_a
    A2-->>-ORCH: result_b
    A3-->>-ORCH: result_c

    ORCH->>ORCH: aggregate(results) via add_messages reducer
```

---

### Diagram 7: Error & Recovery Flow (when error handling exists)

**Purpose:** Dedicated error path diagram — never collapse into the happy-path diagram.

```
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#FFEBEE','primaryBorderColor': '#C62828','primaryTextColor': '#B71C1C','lineColor': '#B71C1C'}}}%%
flowchart TD
    classDef err    fill:#FFEBEE,stroke:#C62828,color:#B71C1C
    classDef ok     fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20
    classDef decide fill:#FFF3E0,stroke:#E65100,color:#BF360C

    CALL[Tool / Agent Call]:::ok --> RES{"Response valid?"}:::decide
    RES -->|success| HAPPY[Continue flow]:::ok
    RES -->|"ToolException / HTTP 4xx/5xx"| ERR1{"retry_count < max_retries?"}:::decide
    ERR1 -->|"yes — increment retry_count"| BACKOFF[Exponential backoff\nsleep 2^n seconds]:::err
    BACKOFF --> CALL
    ERR1 -->|"no — max exceeded"| FALLBACK[fallback_agent\nor error message]:::err
    FALLBACK --> LOG[Log error\nstate.error = msg]:::err
    LOG --> END([Return partial result]):::err
```

---

### Diagram 8: Memory & Persistence Lifecycle (when checkpointing/vector store exists)

**Purpose:** When and how memory is read, written, and retrieved.

```
%%{init: {'theme': 'base'}}%%
sequenceDiagram
    autonumber
    participant APP as Application
    participant GRAPH as StateGraph
    participant CKPT as Checkpointer\n(MemorySaver)
    participant VS as VectorStore

    APP->>GRAPH: invoke(input, config={"thread_id": "u-123"})
    GRAPH->>CKPT: get_tuple(thread_id) → resume?
    CKPT-->>GRAPH: existing AgentState or None

    Note over GRAPH,CKPT: Checkpoint read on every invoke

    GRAPH->>VS: similarity_search(query, k=5)
    VS-->>GRAPH: relevant memories

    GRAPH->>GRAPH: execute nodes
    GRAPH->>CKPT: put_tuple(thread_id, new_state)
    GRAPH-->>APP: final state

    Note over APP,CKPT: Each step auto-checkpointed\nResumable after interruption
```

---

## Step 3: Output Format for Each Diagram

Use this exact structure for every diagram:

---

**📌 Diagram N — [Type]: [Specific descriptive title]**

> **Cross-ref:** [Reference to a related diagram — e.g., "Architecture §1 shows supervisor as central node; this diagram shows its internal routing logic"]
> **Derived from:** `filename.py` · `ClassName.method_name()` · lines N–M

```python
# Source excerpt — 10-25 lines of the most decision-critical code
# Annotate non-obvious logic with inline comments
```

```mermaid
[complete diagram with %%{init}, classDef, linkStyle, and all styling]
```

> **Reading guide:** [What does this diagram reveal that the code alone doesn't? What is the most important non-obvious architectural decision visible here?]

> **Inferences:** [List dashed-line relationships or nodes that were inferred, not explicit in code. If none: "All relationships explicit in source."]

---

## Step 4: Architectural Notes & Risks

After all diagrams, always include:

```markdown
## Architectural Notes & Risks

### Strengths
- [Specific strength from code analysis]

### Risks & Anti-patterns Found
| Risk | Severity | Location | Recommendation |
|---|---|---|---|
| No iteration cap on ReAct loop | High | agent.py:42 | Add max_iterations guard |
| Synchronous LLM calls in parallel branch | Medium | graph.py:88 | Use asyncio.gather() |
| API key hardcoded | Critical | config.py:3 | Move to env var |

### Assumptions & Inferences
- [What was inferred vs explicitly in code]
- [e.g., "Assumed gpt-4o-mini for workers based on class defaults; not set in provided files"]
```

---

## Step 5: Quality Gate — Check All Before Outputting

> **CRITICAL:** Do not output ANY diagram that fails these checks. Fix first, then output.

**Accuracy:**
- [ ] Every node ID matches an actual class/function name from source
- [ ] All solid edges have explicit code backing; inferred edges are dashed + noted
- [ ] All routing conditions quote the actual expression from code
- [ ] Loop bounds (max_iterations, max_retries) are numerically accurate from code
- [ ] EXPLICIT / INFERRED / UNKNOWN classification is correct for every relationship

**Mermaid syntax (validate against `references/syntax-guard.md`):**
- [ ] Every diagram opens with `%%{init: ...}%%` as the FIRST line (no blank lines before)
- [ ] Every diagram defines `classDef` for at least 3 node types, BEFORE any node references
- [ ] Every `classDef` is applied with `:::className` on relevant nodes
- [ ] All labels with special characters (`()`, `{}`, `<>`, `|`) are wrapped in `["..."]`
- [ ] All edge labels with spaces are quoted: `-->|"label text"| B`
- [ ] All `subgraph` IDs contain only `[a-zA-Z0-9_]` — no spaces or special chars
- [ ] Sequence diagrams use `+`/`-` shorthand for activation/deactivation, all balanced
- [ ] Sequence diagrams include at least one `rect` or `note` annotation
- [ ] Every `subgraph`/`loop`/`alt`/`par`/`opt`/`rect` block has a matching `end`
- [ ] Total nodes per diagram ≤ 30; if exceeded, split into C4 layers
- [ ] No raw HTML tags in labels (only `<br/>` where documented)

**Completeness:**
- [ ] Every agent appears in Architecture + Sequence diagrams
- [ ] Every tool appears in at least one diagram
- [ ] Error paths covered in Diagram 3 or 7
- [ ] Parallel execution (if any) has `par` blocks in Diagram 6
- [ ] Deliverable header with Diagram Index is present
- [ ] Scope decision from §0.5 followed — no unnecessary diagrams, no missing mandatory ones

**Professional quality:**
- [ ] System Inventory table complete with all 6 categories
- [ ] Architectural Notes & Risks section present with Strengths + Risks table + Assumptions
- [ ] Each diagram has: source citation + cross-ref + reading guide + inference declaration
- [ ] No invented names — all node names match source code exactly
- [ ] Output quality matches or exceeds `references/real-example.md` standard

---

## Mermaid Styling Reference

### Theme configs

**Architecture / flow:**
```
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','secondaryColor': '#E3F2FD','tertiaryColor': '#F3E5F5','background': '#FAFAFA','fontSize': '14px'}}}%%
```

**Sequence diagrams:**
```
%%{init: {'theme': 'base', 'themeVariables': {'actorBkg': '#E8EAF6','actorBorder': '#3949AB','actorTextColor': '#1A237E','activationBkgColor': '#E3F2FD','activationBorderColor': '#1565C0','noteBkgColor': '#FFFDE7','noteBorderColor': '#F57F17','signalColor': '#546E7A','signalTextColor': '#263238','fontSize': '13px'}}}%%
```

**Error flows:**
```
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#FFEBEE','primaryBorderColor': '#C62828','primaryTextColor': '#B71C1C','lineColor': '#B71C1C','tertiaryColor': '#FFF8E1'}}}%%
```

**Dark theme (for presentations / dark-mode UIs):**

*Architecture / flow (dark):*
```
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#2D2D3F','primaryTextColor': '#E8EAF6','primaryBorderColor': '#7986CB','lineColor': '#90A4AE','secondaryColor': '#1A237E','tertiaryColor': '#311B92','background': '#1E1E2E','fontSize': '14px'}}}%%
```

*Dark classDef palette:*
```
classDef orchestrator fill:#1A237E,stroke:#7986CB,stroke-width:2px,color:#C5CAE9,font-weight:bold
classDef agent        fill:#283593,stroke:#5C6BC0,stroke-width:1.5px,color:#C5CAE9,font-weight:bold
classDef tool         fill:#1B5E20,stroke:#4CAF50,stroke-width:1px,color:#C8E6C9
classDef external     fill:#E65100,stroke:#FF9800,stroke-width:1px,color:#FFF3E0,stroke-dasharray:4 2
classDef storage      fill:#4A148C,stroke:#AB47BC,stroke-width:1px,color:#E1BEE7
classDef decision     fill:#BF360C,stroke:#FF7043,color:#FBE9E7,font-weight:bold
classDef terminal     fill:#2E7D32,stroke:#66BB6A,color:#C8E6C9,font-weight:bold
classDef error        fill:#B71C1C,stroke:#EF5350,color:#FFCDD2,stroke-dasharray:3 2
classDef human        fill:#880E4F,stroke:#EC407A,stroke-width:1px,color:#FCE4EC
```

> **When to use**: Ask the user's preference if the target platform is unknown. Default to light theme for documentation and GitHub/Markdown. Use dark theme when user requests it or when targeting dark-mode presentations.

### Standard classDef palette

```
classDef orchestrator fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#0D47A1,font-weight:bold
classDef agent        fill:#E8EAF6,stroke:#3949AB,stroke-width:1.5px,color:#1A237E,font-weight:bold
classDef tool         fill:#E8F5E9,stroke:#2E7D32,stroke-width:1px,color:#1B5E20
classDef external     fill:#FFF3E0,stroke:#E65100,stroke-width:1px,color:#BF360C,stroke-dasharray:4 2
classDef storage      fill:#F3E5F5,stroke:#6A1B9A,stroke-width:1px,color:#4A148C
classDef memory       fill:#FCE4EC,stroke:#880E4F,stroke-width:1px,color:#880E4F
classDef decision     fill:#FFF3E0,stroke:#E65100,color:#BF360C,font-weight:bold
classDef terminal     fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20,font-weight:bold
classDef error        fill:#FFEBEE,stroke:#C62828,color:#B71C1C,stroke-dasharray:3 2
classDef human        fill:#FCE4EC,stroke:#880E4F,stroke-width:1px,color:#880E4F
```

### linkStyle edge typing

Apply after all edge definitions (0-based index):
```
linkStyle 0 stroke:#1565C0,stroke-width:2px          %% primary control flow
linkStyle 1 stroke:#2E7D32,stroke-width:1px           %% tool call
linkStyle 2 stroke:#6A1B9A,stroke-dasharray:4 2       %% state read/write
linkStyle 3 stroke:#C62828,stroke-dasharray:2 2       %% error path
```

---

## Special Cases

### Large Systems (>15 components) — C4 Model Layering

When a system has more than 15 components, a single diagram becomes unreadable. Split into layers:

**L1 — Context Diagram** (5-8 nodes max): System as a single box + all external actors and systems.
```
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','fontSize': '14px'}}}%%
graph TB
    classDef system   fill:#1565C0,stroke:#0D47A1,stroke-width:2px,color:#FFFFFF,font-weight:bold
    classDef external fill:#FFF3E0,stroke:#E65100,stroke-width:1px,color:#BF360C,stroke-dasharray:4 2
    classDef human    fill:#FCE4EC,stroke:#880E4F,stroke-width:1px,color:#880E4F

    USER([End User]):::human
    SYS["Customer Support\nMulti-Agent System\n────────\n4 agents, 2 tools\nLangGraph + GPT-4o"]:::system
    EXT1["OpenAI API"]:::external
    EXT2["SendGrid"]:::external
    EXT3["Ticketing System"]:::external

    USER -->|"support queries"| SYS
    SYS -->|"LLM calls"| EXT1
    SYS -->|"email notifications"| EXT2
    SYS -->|"ticket CRUD"| EXT3
    SYS -->|"responses"| USER
```

**L2 — Container Diagram**: Internal containers (agent clusters, tool groups, databases). Expand the L1 system box into its major subsystems.
```
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','fontSize': '14px'}}}%%
graph TB
    classDef container fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#0D47A1,font-weight:bold
    classDef storage   fill:#F3E5F5,stroke:#6A1B9A,stroke-width:1px,color:#4A148C
    classDef external  fill:#FFF3E0,stroke:#E65100,stroke-width:1px,color:#BF360C,stroke-dasharray:4 2

    subgraph System["Customer Support MAS"]
        ORCH["Orchestration Layer\n────────\nsupervisor agent\nrouting logic"]:::container
        WORKERS["Worker Agent Pool\n────────\nfaq, ticket, escalation\n3 specialized agents"]:::container
        TOOLS["Tool Layer\n────────\ncreate_ticket, send_email\nexternal integrations"]:::container
        STATE[("State & Memory\n────────\nSupportState + MemorySaver")]:::storage
    end

    ORCH --> WORKERS
    WORKERS --> TOOLS
    ORCH -.- STATE
    WORKERS -.- STATE
```

**L3 — Component Diagram**: Individual agents/tools inside ONE L2 container. Generate one L3 per container.

> **Rule:** Always generate L1 first. Ask the user which L2 containers to expand to L3. Don't generate all L3 diagrams automatically for systems with >5 containers.

### Partial Code

- Mark unknown components with `?` suffix and `stroke-dasharray:5 3`
- Add banner at the top of the deliverable:
  ```
  > ⚠️ **Partial analysis** — Missing files: [list]. Nodes marked `?` are inferred from references in provided code. Relationships to unknown nodes are dashed.
  ```
- Unknown nodes use a dedicated classDef:
  ```
  classDef unknown fill:#F5F5F5,stroke:#9E9E9E,stroke-dasharray:5 3,color:#616161,font-style:italic
  ```

### Async / Streaming

- Use `->>+` / `-->>-`, `par` blocks
- Add `Note right of Agent: SSE streaming / async`
- For streaming token flows, show the `loop Token streaming` pattern (see `references/patterns.md` §Streaming)

### Unknown Framework

- **Do NOT guess.** Ask exactly one question before drawing:
  > "Is coordination graph-based (nodes + edges), message-passing (agents reply to each other), or hierarchical (supervisor delegates)?"
- If the user doesn't answer, default to a generic flowchart with neutral styling

### Mixed Frameworks

- Identify each framework separately using `references/patterns.md` markers
- Draw separate per-framework diagrams first
- Then draw a unified "integration overview" diagram showing cross-framework interactions
- Label each subgraph with the framework name: `subgraph LangGraphLayer["LangGraph — Agent Orchestration"]`

---

## Incremental Update Guidelines

When updating diagrams after code changes:

1. **Re-run Step 0** against the changed files only — update the Component Inventory delta
2. **Identify affected diagrams** — which diagrams reference the changed components?
3. **Update affected diagrams only** — do not regenerate unchanged diagrams
4. **Mark changes** — in the updated diagram's reading guide, note: `> 🔄 Updated [date]: [summary of change]`
5. **Re-validate** — run the Quality Gate (Step 5) on updated diagrams only
6. **Update the Deliverable Header** — increment the generation date and note changed files

**Diagrams most likely to need updates by change type:**
| Code Change | Diagrams to Update |
|---|---|
| New agent added | 1 (Architecture), 2 (Sequence), 3 (Routing) |
| New tool added | 1 (Architecture), 2 (Sequence if used in happy path) |
| Routing logic changed | 3 (Routing), 2 (Sequence if flow changes) |
| State schema changed | 5 (Data/State), 4 (Class if schema is Pydantic) |
| Error handling added/changed | 7 (Error & Recovery), 3 (Routing if guards added) |
| Checkpointer/memory changed | 8 (Memory Lifecycle) |
| Parallelism added | 6 (Parallel), 1 (Architecture for new edges) |

## Reference Files

- `references/patterns.md` — **Framework identification** + canonical templates for LangGraph, AutoGen, CrewAI, Pydantic AI, DSPy, Semantic Kernel, LlamaIndex, ReAct, RAG, HITL, Swarm, Streaming (13 frameworks)
- `references/syntax-guard.md` — **Mermaid syntax pitfall guide** — escaping rules, node/edge syntax, complexity thresholds, self-repair protocol, renderer compatibility matrix
- `references/real-example.md` — **End-to-end worked example** — complete pipeline from LangGraph source code to finished professional deliverable (use as quality benchmark)