# Agent System Patterns Reference

## Table of Contents
1. [LangGraph](#langgraph)
2. [AutoGen / AG2](#autogen)
3. [CrewAI](#crewai)
4. [Pydantic AI](#pydantic-ai)
5. [DSPy](#dspy)
6. [Semantic Kernel](#semantic-kernel)
7. [LlamaIndex Agents](#llamaindex)
8. [OpenAI Swarm / Handoff](#swarm)
9. [Custom ReAct Agent](#react)
10. [RAG Pipeline Agent](#rag)
11. [Human-in-the-Loop (HITL)](#hitl)
12. [Streaming Agents](#streaming)
13. [Structured Output / Tool Calling](#structured-output)

---

## LangGraph {#langgraph}

**Identifying markers:** `StateGraph`, `add_node`, `add_edge`, `add_conditional_edges`, `compile`, `TypedDict` for state, `Command`, `Send`, `MemorySaver`, `SqliteSaver`, `interrupt`

**Key distinctions:**
- State is a `TypedDict` with optional reducers (`Annotated[list, add_messages]`)
- Nodes are plain functions: `def node(state: State) -> dict | Command`
- `Command(goto=..., update=...)` for dynamic routing
- `Send(node, state)` for parallel fan-out
- Checkpointing via `graph.compile(checkpointer=...)`

**Architecture template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','fontSize': '14px'}}}%%
graph TB
    classDef agent        fill:#E8EAF6,stroke:#3949AB,stroke-width:1.5px,color:#1A237E,font-weight:bold
    classDef orchestrator fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#0D47A1,font-weight:bold
    classDef storage      fill:#F3E5F5,stroke:#6A1B9A,stroke-width:1px,color:#4A148C
    classDef terminal     fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20,font-weight:bold

    START([__start__]):::terminal --> NODE1[node_function\nfirst step]:::agent
    NODE1 --> COND{"conditional_edge\nrouter_fn(state)"}
    COND -->|"'route_a'"| NODE2[node_a]:::agent
    COND -->|"'route_b'"| NODE3[node_b]:::agent
    NODE2 --> END([__end__]):::terminal
    NODE3 --> END
    STATE[(AgentState\nTypedDict + reducers)]:::storage
    STATE -.->|"read/write each node"| NODE1
```

**LangGraph parallel fan-out (Send):**
```mermaid
%%{init: {'theme': 'base'}}%%
graph TB
    classDef agent fill:#E8EAF6,stroke:#3949AB,color:#1A237E,font-weight:bold

    FAN[fan_out_node\nSend() to each worker]:::agent
    FAN -->|"Send('worker', task_a)"| W1[worker_node\ntask_a]:::agent
    FAN -->|"Send('worker', task_b)"| W2[worker_node\ntask_b]:::agent
    FAN -->|"Send('worker', task_c)"| W3[worker_node\ntask_c]:::agent
    W1 -->|"result_a"| AGG[aggregate_node\nmerge results]:::agent
    W2 -->|"result_b"| AGG
    W3 -->|"result_c"| AGG
```

---

## AutoGen / AG2 {#autogen}

**Identifying markers:** `ConversableAgent`, `UserProxyAgent`, `AssistantAgent`, `GroupChat`, `GroupChatManager`, `initiate_chat`, `register_function`, `is_termination_msg`, `human_input_mode`

**Key distinctions:**
- Agents communicate by replying to messages (not state dicts)
- `UserProxyAgent` acts as human proxy — can execute code
- `GroupChatManager` selects next speaker using LLM or round-robin
- Termination via `is_termination_msg` function or `max_turns`

**Architecture template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','fontSize': '14px'}}}%%
graph TB
    classDef manager  fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#0D47A1,font-weight:bold
    classDef agent    fill:#E8EAF6,stroke:#3949AB,color:#1A237E,font-weight:bold
    classDef proxy    fill:#FCE4EC,stroke:#880E4F,color:#880E4F
    classDef tool     fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20

    subgraph GroupChat["AutoGen GroupChat"]
        MGR[GroupChatManager\nspeaker selection LLM]:::manager
        UA[UserProxyAgent\nhuman_input_mode=NEVER]:::proxy
        AA1[AssistantAgent\n'coder'\nModel: gpt-4o]:::agent
        AA2[AssistantAgent\n'critic'\nModel: gpt-4o]:::agent
    end
    TOOLS["registered functions\n(executor: UserProxy)"]:::tool
    LLM["LLM Backend"]

    UA -->|"initiate_chat(msg)"| MGR
    MGR -->|"select_speaker() → AA1"| AA1
    MGR -->|"select_speaker() → AA2"| AA2
    AA1 -->|"function_call"| TOOLS
    TOOLS -->|"result"| UA
    AA1 -.->|"prompt"| LLM
    AA2 -.->|"prompt"| LLM
```

---

## CrewAI {#crewai}

**Identifying markers:** `Agent(role=, goal=, backstory=)`, `Task(description=, expected_output=, agent=)`, `Crew(agents=, tasks=, process=)`, `Process.sequential` / `Process.hierarchical`, `@tool`, `crew.kickoff()`

**Key distinctions:**
- Agents have role/goal/backstory (persona-driven)
- Tasks are discrete work units assigned to agents
- Sequential: task output becomes next task context
- Hierarchical: manager LLM routes tasks to agents

**Architecture template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','fontSize': '14px'}}}%%
graph TB
    classDef manager fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#0D47A1,font-weight:bold
    classDef agent   fill:#E8EAF6,stroke:#3949AB,color:#1A237E,font-weight:bold
    classDef task    fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C
    classDef tool    fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20

    subgraph Crew["Crew — Process: sequential"]
        subgraph Agents["Agents"]
            A1["researcher_agent\nrole: Senior Researcher\nModel: gpt-4o"]:::agent
            A2["writer_agent\nrole: Content Writer\nModel: gpt-4o-mini"]:::agent
        end
        subgraph Tasks["Tasks"]
            T1["research_task\nassigned: researcher_agent\noutput: ResearchReport"]:::task
            T2["write_task\nassigned: writer_agent\ncontext: [research_task]"]:::task
        end
    end
    TOOLS["@tool functions"]:::tool

    KICK([kickoff(inputs)]) --> T1
    T1 --> A1
    A1 --> TOOLS
    T1 -->|"output as context"| T2
    T2 --> A2
    T2 --> OUT([Final Answer])
```

---

## Pydantic AI {#pydantic-ai}

**Identifying markers:** `Agent(model=, system_prompt=, result_type=)`, `@agent.tool`, `@agent.tool_plain`, `RunContext`, `agent.run()` / `agent.run_sync()`, `ModelRetry`, `result_validators`

**Key distinctions:**
- Agent is typed: `Agent[DepsType, ResultType]`
- Tools are typed Python functions decorated with `@agent.tool`
- `RunContext[DepsType]` gives tools access to injected dependencies
- Result is validated against `ResultType` Pydantic model
- Retries triggered by raising `ModelRetry`

**Architecture template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','fontSize': '14px'}}}%%
graph TB
    classDef agent   fill:#E8EAF6,stroke:#3949AB,stroke-width:1.5px,color:#1A237E,font-weight:bold
    classDef tool    fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20
    classDef schema  fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C
    classDef external fill:#FFF3E0,stroke:#E65100,color:#BF360C,stroke-dasharray:4 2

    subgraph PydanticAgent["Pydantic AI Agent"]
        AG["my_agent\nAgent[MyDeps, MyResult]\nModel: claude-3-5-sonnet"]:::agent
        TOOL1["@agent.tool\nget_weather(ctx, city: str)\n→ WeatherData"]:::tool
        TOOL2["@agent.tool_plain\nformat_report(data: str)\n→ str"]:::tool
        VALID["result_validator\n@agent.result_validator"]:::schema
    end
    DEPS["MyDeps\n────────\nhttp_client: AsyncClient\napi_key: str"]:::schema
    RESULT["MyResult\n────────\nsummary: str\nconfidence: float"]:::schema
    EXT["External API"]:::external

    User -->|"agent.run_sync(prompt, deps=deps)"| AG
    AG -->|"tool_call"| TOOL1
    TOOL1 -->|"ctx.deps.http_client.get()"| EXT
    EXT -->|"WeatherData"| TOOL1
    AG -->|"tool_call"| TOOL2
    AG -->|"validate"| VALID
    VALID -->|"ModelRetry on failure"| AG
    VALID --> RESULT
```

---

## DSPy {#dspy}

**Identifying markers:** `dspy.configure(lm=)`, `dspy.Signature`, `dspy.Predict`, `dspy.ChainOfThought`, `dspy.ReAct`, `dspy.Module`, `self.forward()`, `dspy.Optimizer` / `BootstrapFewShot`, `dspy.teleprompt`

**Key distinctions:**
- Signatures define input/output fields with docstrings
- Modules compose predictors (like PyTorch nn.Module)
- No explicit prompt strings — prompts are learned/optimized
- `self.forward()` is the inference method

**Architecture template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','fontSize': '14px'}}}%%
graph TB
    classDef module  fill:#E8EAF6,stroke:#3949AB,stroke-width:1.5px,color:#1A237E,font-weight:bold
    classDef pred    fill:#EDE7F6,stroke:#512DA8,color:#311B92
    classDef sig     fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C
    classDef opt     fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20

    subgraph MyPipeline["MyPipeline(dspy.Module)"]
        subgraph Signatures
            SIG1["QuestionToContext\n────────\nquestion → context"]:::sig
            SIG2["ContextToAnswer\n────────\ncontext, question → answer, confidence"]:::sig
        end
        subgraph Predictors
            P1["self.retrieve\nRetrieve(k=5)"]:::pred
            P2["self.generate\nChainOfThought(ContextToAnswer)"]:::pred
        end
    end
    OPT["BootstrapFewShot\noptimizer"]:::opt
    LM["dspy.LM\nclaude-3-5-sonnet"]

    User -->|"pipeline(question=q)"| P1
    P1 -->|"passages"| P2
    P2 -->|"answer"| User
    OPT -->|"compiled few-shots"| P1
    OPT -->|"compiled few-shots"| P2
    P1 -.->|"LM call"| LM
    P2 -.->|"LM call"| LM
```

---

## Semantic Kernel {#semantic-kernel}

**Identifying markers:** `Kernel()`, `kernel.add_plugin`, `kernel.add_service`, `@kernel_function`, `KernelFunction`, `ChatCompletionAgent`, `AgentGroupChat`, `KernelArguments`, `FunctionChoiceBehavior`

**Key distinctions:**
- Plugins group related functions (like tool namespaces)
- `KernelArguments` carries context between steps
- `AgentGroupChat` coordinates multiple `ChatCompletionAgent`
- `FunctionChoiceBehavior.Auto()` enables automatic tool calling

**Architecture template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','fontSize': '14px'}}}%%
graph TB
    classDef kernel  fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#0D47A1,font-weight:bold
    classDef agent   fill:#E8EAF6,stroke:#3949AB,color:#1A237E,font-weight:bold
    classDef plugin  fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20
    classDef fn      fill:#F1F8E9,stroke:#558B2F,color:#33691E

    KERNEL["Kernel\n────────\nServices: AzureChatCompletion\nPlugins: [SearchPlugin, MathPlugin]"]:::kernel

    subgraph AgentGroupChat["AgentGroupChat"]
        A1["ChatCompletionAgent\nname: 'Researcher'\nInstructions: ..."]:::agent
        A2["ChatCompletionAgent\nname: 'Critic'\nInstructions: ..."]:::agent
    end

    subgraph SearchPlugin["SearchPlugin"]
        F1["@kernel_function\nsearch(query: str) → str"]:::fn
    end

    subgraph MathPlugin["MathPlugin"]
        F2["@kernel_function\ncalculate(expr: str) → float"]:::fn
    end

    User -->|"group_chat.invoke()"| A1
    A1 -->|"FunctionChoiceBehavior.Auto()"| F1
    A1 -->|"FunctionChoiceBehavior.Auto()"| F2
    A1 -->|"reply"| A2
    A2 -->|"critique → A1"| A1
    KERNEL -.->|"provides services"| A1
    KERNEL -.->|"provides services"| A2
```

---

## LlamaIndex Agents {#llamaindex}

**Identifying markers:** `FunctionCallingAgent`, `ReActAgent`, `AgentRunner`, `QueryEngineTool`, `FunctionTool`, `from_tools()`, `agent.chat()`, `AgentChatResponse`, `QueryPipeline`

**Key distinctions:**
- Tools wrap query engines, functions, or other agents
- `ReActAgent` uses Thought/Action/Observation loop
- `FunctionCallingAgent` uses native function calling
- Can nest agents as tools inside other agents

**Architecture template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','fontSize': '14px'}}}%%
graph TB
    classDef agent   fill:#E8EAF6,stroke:#3949AB,stroke-width:1.5px,color:#1A237E,font-weight:bold
    classDef tool    fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20
    classDef engine  fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C
    classDef store   fill:#FFF3E0,stroke:#E65100,color:#BF360C

    subgraph TopAgent["FunctionCallingAgent (top-level)"]
        AG["top_agent\nfrom_tools([sub_agent_tool, fn_tool])"]:::agent
    end

    subgraph SubAgent["ReActAgent (nested)"]
        SAG["sub_agent\nfrom_tools([qe_tool])"]:::agent
    end

    subgraph Tools["Tools"]
        T1["QueryEngineTool\nname='doc_search'\nquery_engine=..."]:::tool
        T2["FunctionTool\nfn=calculate"]:::tool
        T3["sub_agent_tool\nwraps sub_agent"]:::tool
    end

    VS[("VectorStoreIndex\nchroma / pinecone")]:::store
    QE["VectorIndexQueryEngine"]:::engine

    User -->|"agent.chat(msg)"| AG
    AG -->|"tool_call"| T3
    T3 -->|"sub_agent.chat()"| SAG
    SAG -->|"tool_call"| T1
    T1 --> QE
    QE -->|"query"| VS
    AG -->|"tool_call"| T2
```

---

## OpenAI Swarm / Handoff {#swarm}

**Identifying markers:** `Agent(name=, instructions=, functions=)`, `Swarm()`, `client.run()`, `handoff` functions named `transfer_to_*`, `ContextVariables`, `Result(agent=, context_variables=)`

**Key distinctions:**
- Handoffs are Python functions that return an `Agent` object
- `ContextVariables` pass shared context between agents
- No central orchestrator — agents hand off to each other directly
- Stateless: each `client.run()` call is independent (or history passed manually)

**Sequence template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'actorBkg': '#E8EAF6','actorBorder': '#3949AB','actorTextColor': '#1A237E','activationBkgColor': '#E3F2FD','activationBorderColor': '#1565C0','fontSize': '13px'}}}%%
sequenceDiagram
    autonumber
    actor User
    participant SW as Swarm client.run()
    participant TRIAGE as triage_agent
    participant SALES as sales_agent
    participant SUPPORT as support_agent

    User->>+SW: run(agent=triage_agent, messages=[...])
    SW->>+TRIAGE: invoke

    TRIAGE->>TRIAGE: classify intent
    alt sales intent detected
        TRIAGE->>SW: Result(agent=sales_agent)
        SW->>-TRIAGE: (handoff)
        SW->>+SALES: invoke with context_variables
        SALES-->>-SW: final response
    else support intent detected
        TRIAGE->>SW: Result(agent=support_agent)
        SW->>-TRIAGE: (handoff)
        SW->>+SUPPORT: invoke
        opt needs escalation
            SUPPORT->>SW: Result(agent=triage_agent)
        end
        SUPPORT-->>-SW: final response
    end
    SW-->>User: SwarmResult(messages, agent, context_variables)
```

---

## Custom ReAct Agent {#react}

**Identifying markers:** Thought/Action/Observation loop, `while not done`, tool dispatch dict, `parse_action`, `execute_tool`, scratchpad accumulation, `max_iterations` guard

**Flow template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E3F2FD','primaryTextColor': '#0D47A1','primaryBorderColor': '#1565C0','lineColor': '#546E7A'}}}%%
flowchart TD
    classDef process  fill:#E8EAF6,stroke:#3949AB,color:#1A237E
    classDef decision fill:#FFF3E0,stroke:#E65100,color:#BF360C,font-weight:bold
    classDef terminal fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20,font-weight:bold
    classDef error    fill:#FFEBEE,stroke:#C62828,color:#B71C1C

    START([User Query]):::terminal --> INIT[Init scratchpad\niteration = 0]:::process
    INIT --> THINK["Thought\nLLM: given history, what next?"]:::process
    THINK --> PARSE["parse_action(response)"]:::process
    PARSE --> ACT{"Action type?"}:::decision
    ACT -->|"'final_answer'"| END([Return answer]):::terminal
    ACT -->|"tool_name"| TOOL["execute_tool(name, args)"]:::process
    TOOL --> OBS["Observation\nappend result to scratchpad"]:::process
    OBS --> CHECK{"iteration < max_iterations?"}:::decision
    CHECK -->|"yes — increment"| THINK
    CHECK -->|"no"| FALLBACK["Return partial answer\nor raise MaxIterationsError"]:::error
```

**Sequence template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'actorBkg': '#E8EAF6','actorBorder': '#3949AB','actorTextColor': '#1A237E','activationBkgColor': '#E3F2FD','fontSize': '13px'}}}%%
sequenceDiagram
    autonumber
    actor User
    participant REACT as ReActAgent
    participant LLM
    participant REG as Tool Registry

    User->>+REACT: run(query)
    REACT->>REACT: init scratchpad

    loop ReAct loop (max_iterations=N)
        REACT->>+LLM: [system] + query + scratchpad
        LLM-->>-REACT: "Thought: ...\nAction: tool_name[{args}]"
        alt action == "Final Answer"
            REACT-->>User: answer
        else tool call
            REACT->>+REG: dispatch(tool_name, args)
            REG-->>-REACT: Observation: result
            REACT->>REACT: append Obs to scratchpad
        end
    end
    REACT-->>-User: MaxIterationsError or partial
```

---

## RAG Pipeline Agent {#rag}

**Identifying markers:** `embed`, `similarity_search`, `vectorstore`, `retriever`, `VectorStoreRetriever`, `RetrievalQA`, `stuff_documents_chain`, `create_retrieval_chain`

**Architecture template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','fontSize': '14px'}}}%%
flowchart TD
    classDef process fill:#E8EAF6,stroke:#3949AB,color:#1A237E
    classDef storage fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C
    classDef io      fill:#FFF3E0,stroke:#E65100,color:#BF360C
    classDef external fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20

    subgraph Ingestion["Ingestion Pipeline (offline)"]
        DOCS["Source Documents"]:::io --> SPLIT["TextSplitter\nchunk_size=1000, overlap=200"]:::process
        SPLIT --> EMBED1["EmbeddingModel\ntext-embedding-3-small"]:::process
        EMBED1 --> VS[("VectorStore\nChroma / Pinecone")]:::storage
    end

    subgraph Query["Query Pipeline (online — per request)"]
        Q([User Query]):::io --> EMBED2["EmbeddingModel\nsame model as ingestion"]:::process
        EMBED2 --> RET["Retriever\nsimilarity_search(k=5)"]:::process
        VS -->|"ANN search"| RET
        RET --> CTX["Context Builder\nformat_docs(docs)"]:::process
        CTX --> PROMPT["Prompt Template\n{context} + {question}"]:::process
        PROMPT --> LLM["LLM\ngpt-4o-mini"]:::process
        LLM --> ANS([Answer + Sources]):::io
    end
```

---

## Human-in-the-Loop (HITL) {#hitl}

**Identifying markers:** `interrupt()`, `interrupt_before=["node"]`, `interrupt_after=["node"]`, `human_approval`, `input()` inside node, `HumanMessage` injected mid-graph, LangGraph `breakpoint`

**Sequence template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'actorBkg': '#E8EAF6','actorBorder': '#3949AB','actorTextColor': '#1A237E','activationBkgColor': '#E3F2FD','noteBkgColor': '#FFFDE7','noteBorderColor': '#F57F17','fontSize': '13px'}}}%%
sequenceDiagram
    autonumber
    actor Human
    participant GRAPH as StateGraph
    participant AGENT as Agent Node
    participant TOOL as Tool

    Human->>+GRAPH: invoke(input, thread_id="t1")
    GRAPH->>+AGENT: execute
    AGENT->>AGENT: generate proposed action
    AGENT-->>-GRAPH: update state

    Note over GRAPH: interrupt_before=["tool_node"]
    GRAPH-->>-Human: ⏸ State snapshot — approve action?

    alt Human approves
        Human->>+GRAPH: invoke(None, thread_id="t1") — resume
        GRAPH->>+TOOL: execute approved action
        TOOL-->>-GRAPH: result
        GRAPH-->>-Human: ✅ Final output
    else Human rejects with feedback
        Human->>+GRAPH: update_state(HumanMessage(feedback))
        GRAPH->>+AGENT: re-invoke with feedback
        AGENT-->>-GRAPH: revised action
        GRAPH-->>-Human: ⏸ Approval requested again
    end
```

---

## Streaming Agents {#streaming}

**Identifying markers:** `agent.astream()`, `graph.astream_events()`, `AsyncIterator`, `StreamingResponse`, `stream_mode="values"` / `"updates"` / `"messages"`, `yield` inside agent function, SSE endpoints

**Architecture template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','fontSize': '14px'}}}%%
sequenceDiagram
    autonumber
    actor Client
    participant API as FastAPI\nSSE endpoint
    participant GRAPH as LangGraph\nastream_events()
    participant LLM as LLM\nstreaming=True

    Client->>+API: GET /stream?query=...
    API->>+GRAPH: astream_events(input, stream_mode="messages")
    GRAPH->>+LLM: invoke (streaming=True)

    loop Token streaming
        LLM-->>GRAPH: AIMessageChunk(content="token")
        GRAPH-->>API: on_chat_model_stream event
        API-->>Client: data: {"token": "..."}\n\n
    end

    LLM-->>-GRAPH: complete AIMessage
    GRAPH->>GRAPH: execute tools if needed
    GRAPH-->>-API: on_chain_end event
    API-->>-Client: data: {"done": true}\n\n

    Note over Client,API: Server-Sent Events (SSE)\nContent-Type: text/event-stream
```

---

## Structured Output / Tool Calling {#structured-output}

**Identifying markers:** `with_structured_output(Schema)`, `bind_tools(tools)`, `tool_calls` on `AIMessage`, `ToolCall`, `JsonOutputParser`, `PydanticOutputParser`, `response_format={"type": "json_object"}`

**Flow template:**
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#E8EAF6','primaryTextColor': '#1A237E','primaryBorderColor': '#3949AB','lineColor': '#546E7A','fontSize': '14px'}}}%%
flowchart TD
    classDef process  fill:#E8EAF6,stroke:#3949AB,color:#1A237E
    classDef schema   fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C
    classDef decision fill:#FFF3E0,stroke:#E65100,color:#BF360C,font-weight:bold
    classDef terminal fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20,font-weight:bold

    PROMPT[Build prompt\n+ tool schemas injected]:::process
    LLM["LLM.bind_tools(tools)\nor with_structured_output(Schema)"]:::process
    PARSE{"Response type?"}:::decision
    TOOL_CALL["AIMessage.tool_calls\n[{name, args, id}]"]:::process
    EXEC["ToolExecutor\nexecute each call"]:::process
    TOOL_MSG["ToolMessage\n(result, tool_call_id)"]:::process
    TEXT["AIMessage\n(content: str)"]:::terminal
    STRUCT["Parsed Schema\nPydantic model instance"]:::schema

    PROMPT --> LLM
    LLM --> PARSE
    PARSE -->|"tool_calls present"| TOOL_CALL
    TOOL_CALL --> EXEC
    EXEC --> TOOL_MSG
    TOOL_MSG -->|"append to messages"| PROMPT
    PARSE -->|"no tool_calls + structured"| STRUCT
    PARSE -->|"no tool_calls + plain"| TEXT
```