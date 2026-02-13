# Markdown Node Canvas (MNC) v0.2  
A markdown-native, LLM-friendly, deterministic node canvas format.

---

# 1. Design Goals

MNC is designed to be:

- Text-first (single UTF-8 file is source of truth)
- LLM-editable
- Human-readable
- Diff-friendly
- Deterministic
- Minimal grammar
- Semantically expressive (narrative + system design)

Non-goals:

- Pixel-perfect diagram control
- Port-level modeling
- Full CSS/layout engine
- Animation or transforms

---

# 2. File Structure

Canonical section order:

1. Canvas header (required)
2. Style blocks (optional)
3. Rules (optional)
4. Node blocks
5. Link override lines (optional)

One file = one canvas.

---

# 3. Directives and Escaping

A line is structural only if:

- It begins at column 0
- It is not escaped
- It matches a directive prefix

“Begins at column 0” means the first character of the line is the directive prefix character; any leading spaces or tabs prevent directive recognition.

Directive prefixes at top-level (structural):

``` 
= 
== 
- rule:
- link:
```

A line is structural only if it begins at column 0, is not escaped, and matches a structural prefix.

Inside node bodies, only an unescaped line that begins with `== ` at column 0 starts a new block.
This includes both node headers (`== NODEID: ...`) and style headers (`== style: ...`).
Any such `== ` line terminates the current node body.

Escape rule:

Prefix the first character with `\` to treat it literally:

``` 
\== not a header
\- link: not a link
\= not a canvas header
```

Inside node bodies, only `== ` starts a new block.  
All other directive-looking lines are treated as Markdown.

---

# 4. Canvas Header (Required)

First non-empty line:

``` 
= canvas: <Title> v<Major>.<Minor>
```

Example:

``` 
= canvas: Distributed Auth Design v0.2
```

---

# 5. Styles

## 5.1 Style Block

``` 
== style: <StyleName>
- key: value
- key: value
```

StyleName may contain spaces.

Unknown keys are allowed and must be preserved.

---

## 5.2 Supported Style Keys (Core v0.2)

### Visual
- bg
- text
- border
- borderWidth
- shape = rect | rounded | circle | ellipse | diamond | note
- radius (corner rounding)
- opacity (0–1)

### Typography
- font
- fontSize
- fontWeight
- align = left | center | right

### Depth
- shadow = none | soft | hard

### Behavior (render hints)
- collapsed = true|false

Unknown keys are permitted but not guaranteed across renderers.

---

## 5.3 Style Resolution

When rendering a node:

1. Apply base style (from `style=` property if present)
2. Apply tag-derived styles in rule order
3. Later style keys override earlier ones

Style merge is key-wise.

---

# 6. Rules (Tag → Style)

Syntax:

``` 
- rule: #tag -> style StyleName
```

Example:

``` 
- rule: #decision -> style Decision
```

Rules are evaluated in file order.

Multiple rules may apply.

---

# 7. Nodes

## 7.1 Node Header

``` 
== NODEID: Title (properties)
```

NODEID:

``` 
[A-Za-z][A-Za-z0-9_-]{1,31}
```

Properties are optional.

Property tokenization (normative):
- Properties are a sequence of **tokens** separated by one or more spaces or tabs, **outside quotes**.
- Each token is either:
  - `key=value` (assignment), or
  - `flag` (bare key with no `=`)
- Bare (unquoted) values must not contain whitespace or `)`.

Core structured values (normative):
- `pos=x,y` where x and y are base-10 integers with no spaces.
- `size=WxH` where W and H are base-10 integers with no spaces.
- `tags=#t1,#t2,#t3` where tags are comma-separated `#` identifiers with no spaces.

---

## 7.2 Node Properties

Inside parentheses:

``` 
key=value
```

Space-separated.

Boolean flags are allowed (e.g., `magnet`).

---

### Core Node Properties

Layout:
- pos=x,y
- size=WxH
- layer=back|normal|front

Identity & ordering:
- seq=integer

Styling:
- style=StyleName
- tags=#a,#b,#c

Behavior:
- magnet
- collapsed=true|false

Unknown properties are preserved.

Quoted values allowed:

``` 
style="Dark Panel"
```

Quotes escape with `\"`.

---

## 7.3 Node Body

Everything after header until next `== ` is Markdown.

Inline node links are defined inside Markdown.

---

# 8. Inline Node Links (Authoritative)

Inline links define graph edges.

Syntax:

``` 
[Text](mnc:NODEID)
```

Optional Markdown title attribute (v0.2 supported form):

``` 
[Text](mnc:NODEID "Choice")
```

Normative constraints for v0.2 inline link extraction:
- Only targets of the form `mnc:NODEID` are recognized.
- Only **double-quoted** title attributes (`"..."`) are recognized.
- Other Markdown link-title forms (single quotes or parentheses titles) are not recognized in v0.2.

Semantics:

- Source = containing node
- Target = NODEID
- Edge is directional
- Title attribute (if present) becomes the default edge `type`.
  - If multiple inline links in the same source node body produce the same edge (same src and dst), the **last** encountered title attribute wins for the derived default `type` (last-wins).

Inline links are the primary source of edge existence.

If an inline link is removed, the derived edge disappears.

---

## 8.1 Extraction scope (normative)

Inline link extraction MUST ignore any content inside fenced code blocks delimited by triple backticks (```) or triple tildes (~~~), until the matching closing fence is found.

---

# 9. Explicit Link Overrides

Optional `- link:` lines modify or supplement derived edges.

They do not require an inline link.

They may:

- Override metadata of derived edges
- Create structural-only edges (for system diagrams)

---

## 9.1 Syntax

Directed:

``` 
- link: A -> B key=value key=value
```

Undirected:

``` 
- link: A - B key=value
```

---

## 9.2 Link Properties (Core v0.2)

Identity:
- id=LINKID

Semantics:
- type=TypeName
- label="Text"

Routing hint:
- route=auto|straight|orthogonal|bezier

Visual:
- line=solid|dashed|dotted
- width=integer
- color=#RRGGBB
- arrow=none|end|both

Styling:
- style=StyleName

Unknown keys allowed.

---

# 10. Edge Resolution Rules

1. Collect inline links from all node bodies.
2. Each inline link produces a directional edge.
3. Apply any matching `- link:` override (if same source/target).
4. If multiple overrides exist:
   - Later declarations override earlier keys.
5. If `- link:` exists without inline link:
   - Edge is created explicitly.
6. If both inline and explicit exist:
   - Properties merge (explicit overrides inline defaults).

Edge identity is a 4-tuple:

- If `id` is present: (source, target, directedness, id)
- If `id` is absent: (source, target, directedness, ∅)

Derived (inline) edges always have `id=∅`.

Override attachment (normative):
- A `- link:` line with no `id` merges into the edge identity (source, target, directedness, ∅).
- A `- link:` line with `id=X` merges into (source, target, directedness, X).
- If (source, target, directedness, X) does not exist yet, it is created as a distinct edge (even if a derived edge exists for the same source/target with `id=∅`).

For the same identity, properties merge in file order and later keys override earlier keys.

---

# 11. Magnetic Nodes

Nodes with `magnet` act as geometric containers.

Membership is determined by overlap.

No serialized attachment state.

---

# 12. Canonical Save Rules

Canonical save produces a deterministic ordering for stable diffs.

On canonical save:

1. Header
2. Styles (sorted by name)
3. Rules (file order)
4. Nodes (sorted by NODEID)
5. Link lines (sorted by source, then target, then id)

Property order:

Nodes:
1. pos
2. size
3. tags
4. style
5. magnet
6. layer
7. seq
8. others (alphabetical)

Links:
1. id
2. type
3. route
4. line
5. width
6. color
7. arrow
8. label
9. style
10. others

Node bodies preserved byte-for-byte (except newline normalization).

---

# 13. Scaling Guidance (Non-Normative)

For large canvases:

- Use `seq` for logical ordering
- Use tags for filtering
- Use collapsed=true for grouping
- Split large projects into multiple canvas files
- Use “hub” canvases to connect conceptual domains

---

# 14. Example (Interactive Fiction + System Design)

``` 
= canvas: Auth Flow Narrative v0.2

== style: Decision
- shape: diamond
- border: #88aaff

== style: Service
- shape: rounded
- bg: #1e1e1e
- text: #ffffff

- rule: #decision -> style Decision
- rule: #service -> style Service

== A01: User Login (pos=100,100 size=300x180 seq=1 tags=#decision)
User submits credentials.

Choose:
- [Validate Token](mnc:SVC1 "Control")
- [Reject Request](mnc:END1 "Choice")

== SVC1: Auth Service (pos=500,100 size=260x160 seq=2 tags=#service)
Responsible for JWT validation.

Calls:
- [Token Store](mnc:DB1 "Data")

== DB1: Token Store (pos=900,100 size=220x140 seq=3 tags=#service)
Stores active tokens.

== END1: Access Denied (pos=500,300 size=200x120 seq=4)

- link: A01 -> SVC1 route=orthogonal line=solid
- link: A01 -> END1 route=bezier line=dashed
```

---

# 15. Summary

MNC v0.2 now supports:

- Authoritative inline links
- Edge styling without geometric complexity
- Deterministic override merging
- Narrative branching
- System design modeling
- Scalable organization
- Strict parsing rules
- Minimal syntax growth

---