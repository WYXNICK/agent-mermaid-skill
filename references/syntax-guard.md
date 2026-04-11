# Mermaid Syntax Guard — Pitfalls, Prevention & Self-Repair

> **When to use this file:** Before outputting ANY Mermaid code block, validate against these rules. After rendering failures, use the Self-Repair section to diagnose and fix.

---

## 1. Universal Rules (All Diagram Types)

### 1.1 `%%{init}` Directive
- **MUST** be the very first line inside the Mermaid code block — no blank lines before it
- **Valid:** `%%{init: {'theme': 'base', 'themeVariables': {...}}}%%`
- **Invalid:** Any content before `%%{init}`
- Use single quotes `'` inside the JSON, not double quotes — Mermaid's parser chokes on nested double quotes

### 1.2 Comments
- Use `%%` for inline comments: `%% This is a comment`
- Comments must be on their own line — do not append to node/edge lines
- Never place comments inside `%%{init}` blocks

### 1.3 Special Characters in Labels — The #1 Cause of Broken Diagrams
| Character | Problem | Solution |
|-----------|---------|----------|
| `(` `)` | Parsed as node shape delimiters | Wrap entire label in `["..."]`: `A["function(arg)"]` |
| `{` `}` | Parsed as decision node | Wrap in `["..."]`: `A["dict{key: val}"]` |
| `<` `>` | Parsed as asymmetric shape | Wrap in `["..."]`: `A["List<str>"]` |
| `"` inside label | Breaks quoting | Use `&quot;` or restructure label to avoid |
| `|` in label text | Parsed as edge label delimiter | Use `["label"]` with `&#124;` or avoid pipes |
| `#` | Parsed as hex color or entity start | Use `&num;` or wrap in `["..."]` |
| `&` | Parsed as HTML entity start | Use `&amp;` |
| `\n` in labels | Line break inside node | Use `<br/>` in flowchart, `\n` (literal backslash-n ) in sequence diagram participants |

**Golden rule:** When in doubt, wrap the entire label in `["double-quoted brackets"]`.

### 1.4 Node ID Rules
- IDs must start with a letter or underscore
- Allowed: letters, digits, underscores, hyphens
- **Invalid IDs:** `123node`, `my node`, `node.name`, `node:name`
- **Valid IDs:** `node_123`, `myNode`, `node-123`, `_private`

### 1.5 Label Length
- Keep single-line labels under **50 characters**
- For longer content, use multi-line with `<br/>` (flowchart) or `\n` (sequence participant)
- If a node has >3 lines of text, consider splitting into multiple connected nodes

---

## 2. Flowchart / Graph Specific

### 2.1 `graph` vs `flowchart`
- Prefer `flowchart` (newer, more features, better subgraph support)
- `graph` is legacy — use only if `flowchart` causes rendering issues in target environment

### 2.2 Node Shapes
| Shape | Syntax | Notes |
|-------|--------|-------|
| Rectangle | `A[text]` | Default |
| Rounded | `A(text)` | Common for processes |
| Stadium | `A([text])` | Good for start/end terminals |
| Cylinder | `A[(text)]` | Good for databases/storage |
| Diamond | `A{text}` | Decision nodes |
| Hexagon | `A{{text}}` | Good for preparation steps |
| Circle | `A((text))` | Good for events/connectors |
| Double circle | `A(((text)))` | Rarely needed |

**Pitfall:** If your label contains `()`, `{}`, or `[]`, the parser confuses them with shape delimiters. Always use `["quoted label with (parens)"]`.

### 2.3 Edge Syntax
```
A --> B          %% solid arrow
A --- B          %% solid line, no arrow
A -.-> B         %% dotted arrow
A -.- B          %% dotted line
A ==> B          %% thick arrow
A -->|"label"| B %% labeled arrow (ALWAYS quote labels with spaces or special chars)
A -- "label" --> B  %% alternative label syntax
```

**Common mistake:** `-->|label with spaces|` — this fails. Always use `-->|"label with spaces"|`.

### 2.4 Subgraph Rules
```
subgraph SubID["Display Title With Spaces"]
    direction TB  %% optional: TB, BT, LR, RL
    A[node]
end
```
- Subgraph ID: **no spaces, no special characters** — only `[a-zA-Z0-9_]`
- Display title: use `["quoted title"]` for titles with spaces
- Always close with `end`
- Maximum recommended nesting: **3 levels**
- Nodes defined inside a subgraph belong to it — don't redefine elsewhere

### 2.5 `classDef` and `:::` Application
```
classDef myClass fill:#E8EAF6,stroke:#3949AB,color:#1A237E
A[text]:::myClass
```
- Define ALL `classDef` lines **after** the `graph`/`flowchart` declaration, **before** any nodes
- Class names: letters, digits, underscores only
- Apply with `:::className` as a suffix to node definition
- You CAN apply to nodes inside subgraphs

### 2.6 `linkStyle` Rules
```
linkStyle 0 stroke:#1565C0,stroke-width:2px
linkStyle 1,2,3 stroke:#2E7D32  %% apply to multiple edges
linkStyle default stroke:#546E7A  %% default for all edges
```
- Indices are **0-based**, counting edges in order of definition
- Count carefully — miscounted indices apply styles to wrong edges
- Use `linkStyle default` for base styling, then override specifics
- **Tip:** When edge count is uncertain, use `linkStyle default` instead of individual indices

---

## 3. Sequence Diagram Specific

### 3.1 Participant Declaration
```
participant A as "Long Display Name"        %% quoted
participant B as Short_Name                 %% unquoted (no spaces)
actor User                                  %% actor icon (stick figure)
```
- `participant` vs `actor`: actors get stick-figure icons
- If display name has spaces → quote it
- Participant order = left-to-right display order — declare in logical order

### 3.2 Message Syntax
```
A->>B: sync call          %% solid line, filled arrow
A-->>B: async response    %% dashed line, filled arrow
A->>+B: call (activate)   %% activate B's lifeline
B-->>-A: return (deactivate) %% deactivate B's lifeline
A-)B: async fire-and-forget  %% open arrow
```

### 3.3 Activation (`+`/`-` shorthand)
- Prefer `+`/`-` shorthand over explicit `activate`/`deactivate`
- `A->>+B:` = send message AND activate B
- `B-->>-A:` = send response AND deactivate B
- **Every `+` must have a matching `-`** — unmatched activations break rendering
- Count your activations! A common error is forgetting to deactivate

### 3.4 Annotations
```
Note right of A: Single participant note
Note over A,B: Spanning note (comma-separated, no spaces around comma)
Note left of A: Left side note

rect rgb(200, 210, 230)    %% colored background region
    A->>B: grouped calls
end

loop Retry (max 3)         %% loop block
    A->>B: attempt
end

alt Condition A            %% conditional
    A->>B: path A
else Condition B
    A->>C: path B
end

par Parallel Task 1        %% parallel execution
    A->>B: task 1
and Parallel Task 2
    A->>C: task 2
end

opt Optional Step          %% optional block
    A->>B: maybe
end
```

**Pitfalls:**
- `Note over A, B:` — space after comma is OK, but be consistent
- `rect` must contain at least one message
- All blocks (`loop`, `alt`, `par`, `opt`, `rect`, `critical`) must close with `end`
- **No `classDef` or `:::` in sequence diagrams** — they don't support it
- `autonumber` must be on its own line, right after `sequenceDiagram`

### 3.5 Sequence Diagram `%%{init}` Theme Variables
Key variables for sequence diagrams (different from flowchart):
```
actorBkg, actorBorder, actorTextColor     %% participant boxes
activationBkgColor, activationBorderColor  %% lifeline activation bars
noteBkgColor, noteBorderColor              %% Note blocks
signalColor, signalTextColor               %% message arrows and text
labelBoxBkgColor, labelBoxBorderColor      %% loop/alt/par labels
```

---

## 4. Class Diagram Specific

### 4.1 Class Definition
```
class MyClass {
    <<interface>>               %% stereotype annotation
    +publicField: str           %% + = public
    -privateField: int          %% - = private
    #protectedField: bool       %% # = protected
    ~packageField: float        %% ~ = package/internal
    +publicMethod(p1: str, p2: int) ReturnType
    -privateMethod()* ReturnType  %% * = abstract
    +staticMethod()$ ReturnType   %% $ = static
}
```

### 4.2 Relationships
```
A <|-- B     %% inheritance (B extends A)
A *-- B      %% composition (B is part of A)
A o-- B      %% aggregation (B belongs to A)
A --> B      %% association
A ..> B      %% dependency
A ..|> B     %% realization/implementation
A -- B       %% link (solid)
A .. B       %% link (dashed)
```

### 4.3 Cardinality
```
A "1" --> "1..*" B : has
A "0..1" --> "0..*" B : contains
```

### 4.4 Notes
```
note for MyClass "This is important\nadditional line"
```
- Use `\n` for line breaks inside notes
- Notes must reference an existing class

---

## 5. Maximum Complexity Thresholds

| Metric | Threshold | Action if exceeded |
|--------|-----------|-------------------|
| Total nodes per diagram | **30** | Split into C4 layers or multiple diagrams |
| Edges per diagram | **40** | Simplify — group related edges into composite arrows |
| Nesting depth (subgraphs) | **3 levels** | Flatten — promote inner subgraphs to separate diagrams |
| Participants (sequence) | **8** | Split into multiple sequence diagrams by scenario |
| Label text length | **50 chars** | Use `<br/>` or `\n` for multi-line, or abbreviate |
| `linkStyle` indices | **20** | Use `linkStyle default` + selective overrides |

---

## 6. Self-Repair Protocol

When a diagram fails to render, follow this diagnostic sequence:

### Step 1: Bracket & Quote Matching
- Count all `[`, `]`, `(`, `)`, `{`, `}`, `"` — they must be balanced
- Check for un-escaped special characters in labels (see §1.3)

### Step 2: Structural Integrity
- Every `subgraph` has a matching `end`
- Every `loop`/`alt`/`par`/`opt`/`rect`/`critical` has a matching `end`
- Every `activate` has a matching `deactivate` (or use `+`/`-` shorthand)

### Step 3: Simplification
- Remove all `classDef`, `linkStyle`, `%%{init}` — does the bare diagram render?
- If yes → re-add styling one block at a time to find the offender
- If no → the structural issue is in nodes/edges

### Step 4: Isolation
- Test each `subgraph` block independently
- Remove edges to find which one causes the break
- Check that all referenced node IDs are defined

### Step 5: Node Label Cleanup
- Wrap ALL labels in `["double-quoted brackets"]`
- Replace all special characters with HTML entities
- Shorten all labels to <30 chars

### Step 6: Fallback
- If the diagram still fails, simplify to a basic version without styling
- Note the rendering issue in the output
- Provide the intended diagram as a code block with a note: "Manual styling may be needed for some renderers"

---

## 7. Renderer Compatibility Notes

| Feature | Mermaid.live | GitHub | VS Code | Confluence |
|---------|-------------|--------|---------|------------|
| `%%{init}` themes | ✅ | ✅ | ✅ | ⚠️ Partial |
| `classDef` | ✅ | ✅ | ✅ | ✅ |
| `linkStyle` | ✅ | ✅ | ✅ | ⚠️ Limited |
| `subgraph` nesting | ✅ 3+ levels | ✅ 2-3 levels | ✅ 3 levels | ⚠️ 2 levels |
| `par` blocks (sequence) | ✅ | ✅ | ✅ | ✅ |
| `classDiagram` notes | ✅ | ✅ | ✅ | ⚠️ May not render |
| `direction` in subgraph | ✅ | ✅ | ✅ | ❌ |
| Dark theme variables | ✅ | ❌ (auto) | ✅ | ❌ |

**Safe baseline:** Target GitHub Flavored Markdown rendering. If targeting other platforms, avoid features marked ⚠️ or ❌.

---

## 8. Quick Reference Checklist

Before outputting any Mermaid block, verify:

- [ ] `%%{init}` is line 1,  uses single quotes internally
- [ ] All `classDef` defined before any node references
- [ ] All node IDs are alphanumeric + underscore/hyphen (no spaces, no dots)
- [ ] All labels with special characters are wrapped in `["..."]`
- [ ] All edge labels with spaces are quoted: `-->|"label"|`
- [ ] All `subgraph` blocks have matching `end`
- [ ] All `loop`/`alt`/`par`/`opt`/`rect` blocks have matching `end`
- [ ] Sequence diagram: every `+` activation has a matching `-` deactivation
- [ ] Sequence diagram: `Note over A,B:` — participant names match declarations
- [ ] Total node count ≤ 30; if exceeded, split into layers
- [ ] No raw HTML tags in labels (use `<br/>` only where documented)
- [ ] All referenced class/participant names match their declarations exactly
