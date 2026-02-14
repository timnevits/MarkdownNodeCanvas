# Markdown Node Canvas (MNC) v0.3
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
2. Tag-style blocks (optional)
3. Node blocks
4. Link override lines (optional)

Legacy `- rule:` lines are accepted only for backward-compatible migration (Section 6).
They are not part of the active authoring model.

One file = one canvas.

---

# 3. Directives and Escaping

A line is structural only if:

- It begins at column 0
- It is not escaped
- It matches a directive prefix

"Begins at column 0" means the first character of the line is the directive prefix character; any leading spaces or tabs prevent directive recognition.

Directive prefixes at top-level (structural):

```
=
==
- link:
- rule:   (legacy migration input only; Section 6)
```

A line is structural only if it begins at column 0, is not escaped, and matches a structural prefix.

Inside node bodies, only an unescaped line that begins with `== ` at column 0 starts a new block.
This includes both node headers (`== NODEID: ...`) and tag-style headers (`== style: #tag`).
Any such `== ` line terminates the current node body.

Escape rule:

Prefix the first character with `\\` to treat it literally:

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
= canvas: Distributed Auth Design v0.3
```

---

# 5. Tags and Styles (Unified Model)

Tags and styles are synonymous in the active v0.3 model.

Non-geometric node appearance is derived only from tags.

`pos` and `size` remain node-local properties only and are never sourced from tag styles.

A tag with no matching style definition is valid and has no visual effect.

## 5.1 Tag-Style Block

Canonical syntax:

```
== style: #tag
- key: value
- key: value
```

`#tag` uses the same tag token form as `tags=`.

Unknown keys are allowed and must be preserved.

## 5.2 Supported Style Keys (Core v0.3)

### Visual
- bg
- text
- border
- borderWidth
- shape = rect | rounded | circle | ellipse | diamond | note
- radius (corner rounding)
- opacity (0-1)

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

## 5.3 Tag Order and Precedence (Normative)

For a node, tag precedence uses the exact left-to-right order in the node's `tags=` property list.

Example source order:

```
tags=#a,#b,#c
```

Precedence resolution:

1. Start with empty appearance properties.
2. For each tag in node tag order, apply that tag's style properties.
3. For key conflicts, the later tag in node tag order wins.

If the same tag appears multiple times, each occurrence is applied in order.

Tag-style definitions for the same tag merge in file order before node-level application; later definition lines/blocks for the same key win.

---

# 6. Backward Compatibility (Legacy Style/Rule Constructs)

The active model does not use rule indirection.

`- rule: #tag -> style Name` is legacy input only.

Legacy behavior in v0.3 is deterministic migration (not strict fail).

## 6.1 Accepted Legacy Inputs

- Legacy named style block:

```
== style: Name
- key: value
```

- Legacy rule line:

```
- rule: #tag -> style Name
```

`Name` is a legacy style name that is not a `#tag` token.

## 6.2 Deterministic Migration

Migration steps:

1. Parse legacy named styles (`Name`) and merge duplicate `Name` blocks in file order.
2. Parse legacy rules in file order.
3. For each `#tag`, build a migrated style by applying referenced legacy styles in that rule order.
4. Merge migrated style into the tag's unified style set.
   - If a canonical tag-style block (`== style: #tag`) also exists, canonical tag-style keys are applied after migrated keys for that tag (canonical wins on conflicts).

Validation rules for legacy migration:

- Rule references undefined legacy style `Name` => validation error.

Canonical save output requirements:

- MUST emit only unified tag-style blocks (`== style: #tag`).
- MUST NOT emit legacy named style blocks.
- MUST NOT emit `- rule:` lines.

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
- Properties are a sequence of tokens separated by one or more spaces or tabs, outside quotes.
- Each token is either:
  - `key=value` (assignment), or
  - `flag` (bare key with no `=`)
- Bare (unquoted) values must not contain whitespace or `)`.

Core structured values (normative):
- `pos=x,y` where x and y are base-10 integers with no spaces.
- `size=WxH` where W and H are base-10 integers with no spaces.
- `tags=#t1,#t2,#t3` where tags are comma-separated `#` identifiers with no spaces.

## 7.2 Node Properties

Inside parentheses:

```
key=value
```

Space-separated.

Boolean flags are allowed (e.g., `magnet`).

### Core Node Properties

Layout:
- pos=x,y
- size=WxH
- layer=back|normal|front

Identity and ordering:
- seq=integer

Styling input:
- tags=#a,#b,#c

Behavior:
- magnet
- collapsed=true|false

`style=...` is legacy input and must not affect appearance in the unified model.
Implementations MAY preserve it as an unknown property during parse; canonical save MUST drop it.

Unknown properties are preserved.

Quoted values allowed:

```
layer="front"
```

Quotes escape with `\\"`.

## 7.3 Node Body

Everything after header until next `== ` is Markdown.

Inline node links are defined inside Markdown.

---

# 8. Inline Node Links and Identity

Inline links define graph edges.

Canonical node identity is immutable internal `NODEID`.

`NODEID` remains authoritative for graph edges and canonical link output.

Authoring supports two link target forms:

```
[Text](mnc:NODEID)
[Text](mnc:"Node Title")
```

Optional Markdown title attribute (v0.3 supported form):

```
[Text](mnc:NODEID "Choice")
[Text](mnc:"Node Title" "Choice")
```

Only double-quoted Markdown title attributes (`"..."`) are recognized.

## 8.1 Alias Resolution (Normative)

For `mnc:"Node Title"` targets:

- Resolve by exact node title match.
- Resolution succeeds only when exactly one node has that title.

Deterministic validation failures:

- Ambiguous alias (two or more nodes share the title) => validation error `E_ALIAS_AMBIGUOUS`.
- Unresolved alias (no node with that title) => validation error `E_ALIAS_UNRESOLVED`.
- Malformed alias syntax => validation error `E_ALIAS_MALFORMED`.

Malformed alias syntax includes any `mnc:"` target that is not exactly one valid double-quoted title token, for example:
- unterminated quote
- trailing non-whitespace characters after the closing quote inside the target token
- unsupported quoting form for alias target

If multiple alias errors exist, implementations MUST report them in deterministic source order: node `NODEID` order, then link encounter order within each node body.

## 8.2 Extraction Scope (Normative)

Inline link extraction MUST ignore any content inside fenced code blocks delimited by triple backticks (```) or triple tildes (~~~), until the matching closing fence is found.

This behavior is unchanged.

## 8.3 Link Semantics

- Source = containing node
- Target = resolved NODEID
- Edge is directional
- Title attribute (if present) becomes the default edge `type`.
  - If multiple inline links in the same source node body produce the same edge (same src and dst), the last encountered title attribute wins for the derived default `type`.

Inline links are the primary source of edge existence.

If an inline link is removed, the derived edge disappears.

---

# 9. Explicit Link Overrides

Optional `- link:` lines modify or supplement derived edges.

They do not require an inline link.

They may:

- Override metadata of derived edges
- Create structural-only edges (for system diagrams)

## 9.1 Syntax

Directed:

```
- link: A -> B key=value key=value
```

Undirected:

```
- link: A - B key=value
```

## 9.2 Link Properties (Core v0.3)

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
2. Resolve alias links to NODEID (Section 8.1).
3. Each inline link produces a directional edge.
4. Apply any matching `- link:` override (if same source/target).
5. If multiple overrides exist:
   - Later declarations override earlier keys.
6. If `- link:` exists without inline link:
   - Edge is created explicitly.
7. If both inline and explicit exist:
   - Properties merge (explicit overrides inline defaults).

Edge identity is a 4-tuple:

- If `id` is present: (source, target, directedness, id)
- If `id` is absent: (source, target, directedness, empty)

Derived (inline) edges always have `id=empty`.

Override attachment (normative):
- A `- link:` line with no `id` merges into the edge identity (source, target, directedness, empty).
- A `- link:` line with `id=X` merges into (source, target, directedness, X).
- If (source, target, directedness, X) does not exist yet, it is created as a distinct edge (even if a derived edge exists for the same source/target with `id=empty`).

For the same identity, properties merge in file order and later keys override earlier keys.

---

# 11. Magnetic Nodes

Nodes with `magnet` act as geometric containers.

Membership is determined by overlap.

No serialized attachment state.

---

# 12. Canonical Save Rules

Canonical save produces deterministic output for stable diffs.

Existing deterministic parse/derive/canonical-save guarantees remain in effect.

On canonical save:

1. Header
2. Unified tag-style blocks (sorted by tag token)
3. Nodes (sorted by NODEID)
4. Link lines (sorted by source, then target, then id)

Canonical save and links:

- All graph link targets MUST be rewritten to ID form (`mnc:NODEID`).
- Any alias-form inline link target (`mnc:"Node Title"`) MUST be rewritten to `mnc:NODEID`.
- Canonical output MUST NOT contain alias targets.

Property order:

Nodes:
1. pos
2. size
3. tags
4. magnet
5. layer
6. seq
7. others (alphabetical)

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

Node bodies are preserved byte-for-byte except for:
- newline normalization to `\n`
- inline link target canonicalization from alias form to ID form

Canonical output MUST NOT emit legacy `- rule:` lines.

---

# 13. Validation and Determinism Summary

Deterministic failures include:

- duplicate NODEID
- malformed property tokenization
- malformed alias syntax (`E_ALIAS_MALFORMED`)
- ambiguous alias (`E_ALIAS_AMBIGUOUS`)
- unresolved alias (`E_ALIAS_UNRESOLVED`)
- legacy rule references undefined legacy style name

Column-0 directive behavior remains unchanged.

Fenced-code inline-link ignore behavior remains unchanged.

---

# 14. Summary

MNC v0.3 supports:

- Unified tags/styles model (`== style: #tag`)
- Tag-order-based deterministic appearance precedence
- Authoring aliases for links (`mnc:"Node Title"`) with deterministic validation
- Canonical link identity by immutable NODEID
- Canonical save rewriting all links to `mnc:NODEID`
- Deterministic migration for legacy style/rule constructs
- Deterministic override merging
- Strict parsing rules
- Minimal syntax growth

---
