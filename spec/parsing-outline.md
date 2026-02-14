## Reference parsing algorithm outline (MNC v0.3)

This is an implementation-oriented outline (state machine + data model). It aims for deterministic behavior and minimal ambiguity.

---

# 0) Data model

### In-memory structures
- `Canvas { title: str, version: (major:int, minor:int), styles: Map[str, Style], rules: [Rule], nodes: Map[nodeId, Node], link_overrides: [LinkOverride], notes: [NoteLine] }`
- `Style { name: str, props: Map[str,str], raw_order: [key] (optional) }`
- `Rule { tag: str, style_name: str }`
- `Node { id: str, title: str, props: Map[str, Value], flags: Set[str], body_lines: [str], inline_links: [InlineLink] }`
- `InlineLink { src: nodeId, dst: nodeId, text: str, md_title: str? , span: (line, colStart, colEnd) }`
- `LinkOverride { directed: bool, a: nodeId, b: nodeId, props: Map[str,Value], flags:Set[str], raw_line: str }`

### Value representation
- For props: store as:
  - `int` where parsed as int (pos, size, seq, width, opacity if you later allow float)
  - `str` otherwise
- Preserve unknown props as strings.

---

# 1) Helpers

### 1.1 Directive detection (only at column 0)
For a raw line `L` (without trailing `\n`):

- `is_escaped = L.startswith("\\")`
- If `is_escaped`: it is **never** a directive.
- Else, at column 0:
  - `L.startswith("= ")` → CanvasHeader candidate
  - `L.startswith("== ")` → BlockHeader
  - `L.startswith("- rule:")` → RuleLine candidate
  - `L.startswith("- link:")` → LinkLine candidate
  - else → NoteLine (outside node), MarkdownLine (inside node)

### 1.2 Unescape-leading-char (for literal directive lines)
If a line begins with `\`:
- remove the first `\` only (i.e., `\==` becomes `==`)
- do not treat it as structure; keep as literal text.

---

# 2) Single-pass structural parse (blocks + lines)

Use a state machine:

States:
- `EXPECT_HEADER`
- `TOPLEVEL` (not inside node body)
- `IN_STYLE(style_name)`
- `IN_NODE(node_id)`

Maintain:
- `current_style: Style?`
- `current_node: Node?`

### 2.1 Read file as lines
- Normalize line endings: accept `\n` and `\r\n`; strip only the newline.
- Keep original body lines exactly as read (except newline normalization if you do it).

### 2.2 EXPECT_HEADER
Loop until you hit first non-empty, non-blank line:
- If line is escaped: ERROR (canvas header must be the first non-empty line; only blank lines may precede it)
- If line starts with `= canvas:` parse header:
  - Extract title and version (`v<maj>.<min>` token at end).
  - If fails → error: “Invalid canvas header”
  - Transition to `TOPLEVEL`
- Else → error: “Missing canvas header”

### 2.3 TOPLEVEL
For each line:
- If blank: continue (or store note if you want)
- If unescaped `== style:` → start style:
  - finalize previous style if any (shouldn’t be in TOPLEVEL)
  - create new `Style`, transition `IN_STYLE`
- Else if unescaped `== ` (non-style) → start node:
  - parse node header; create node; transition `IN_NODE`
- Else if unescaped `- rule:` → parse rule; append to `rules`
- Else if unescaped `- link:` → parse link override; append to `link_overrides`
- Else:
  - note line (store or ignore depending on your policy)

### 2.4 IN_STYLE
While in style block:
- If unescaped `== ` appears: this ends the style block.
  - Transition back to `TOPLEVEL`, then immediately re-handle this `==` line (style or node).
- Else if blank: ignore or preserve (recommend ignore inside style)
- Else if escaped: treat as literal and store as note (or ignore); simplest: ignore non-`- key:` lines in styles.
- Else if line matches `- <key>: <value>`:
  - parse key/value; store in `current_style.props[key]=value` (trim key; value as raw remainder trimmed)
- Else:
  - ignore (or store in a `style.unknown_lines` list if you want strict round-trip)

### 2.5 IN_NODE
While in node block:
- If unescaped `== ` appears: this ends the node body.
  - finalize current node (store)
  - Transition back to `TOPLEVEL`, then re-handle the `==` line.
- Else:
  - Append line (with leading-escape removed if present) to `node.body_lines`
  - Do **not** parse `- rule:` / `- link:` as directives here.

At EOF:
- If in `IN_NODE`, finalize that node.
- If in `IN_STYLE`, finalize that style.

---

# 3) Parsing node headers and props

### 3.1 Parse node header line
Input: line like
`== A12: Title (pos=1,2 size=3x4 tags=#a,#b style="Dark Panel" magnet seq=10)`

Algorithm:
1) Strip leading `== `.
2) Split on first `:` → `id_part`, `rest` (error if missing).
3) `node_id = trim(id_part)`, validate regex/length.
4) `rest = trim(rest)`
5) If `rest` contains `(` ... `)` at end:
   - Find the last `(` that starts props and ends with final `)` (simplest: require props, if present, to be the final suffix)
   - `title = trim(rest before that '(')`
   - `props_text = inside parens`
   Else:
   - `title = rest`, no props
6) Parse props_text into `props` + `flags` (below).

### 3.2 Parse prop list (space-separated tokens)
Input: `pos=140,120 size=220x120 tags=#q,#a style="Dark Panel" magnet layer=back`

Tokenization (minimal, robust):
- Scan left-to-right.
- Tokens are separated by whitespace **outside quotes**.
- A token is either:
  - `key=value`
  - `flag` (key alone)

Value parsing:
- If value begins with `"`:
  - parse quoted string with escapes `\"` and `\\`
- Else value is the raw token tail.

Typed coercions (v0.3 core):
- `pos`: parse `x,y` ints
- `size`: parse `WxH` ints
- `seq`, `width`, `borderWidth`, `fontSize`: int
- `opacity`: keep string in v0.3 (or float if you decide)
- Everything else: string

Store:
- `flags.add(key)` for flag tokens (e.g., `magnet`)
- `props[key]=parsedValue` for key=value

---

# 4) Parse rule lines

Input:
`- rule: #question -> style Question Panel`

Algorithm:
1) Ensure prefix `- rule:`
2) Strip prefix; parse:
   - `tag` from start up to `->`
   - then expect `style`
   - then `styleName` remainder trimmed
3) Validate `tag` begins with `#` and contains no whitespace.

---

# 5) Parse explicit link override lines

Input:
`- link: A12 -> B07 id=L1 type=Choice route=orthogonal label="drives"`

Algorithm:
1) Ensure prefix `- link:`
2) Strip prefix; now parse endpoints:
   - read `nodeA` token
   - read operator token (`->` or `-`)
   - read `nodeB` token
3) Remaining text (if any) is prop list using the same tokenizer as node props.
4) Store as `LinkOverride(directed = (op=="->"), a=nodeA, b=nodeB, props, flags)`

Note: in v0.3, these lines may also create edges (structural-only edge) if no inline link exists.

---

# 6) Extract inline links from node Markdown bodies (second pass)

Perform after structural parse, per node:

### 6.1 Detect `mnc:` links in Markdown
Minimum viable extractor (works well without full Markdown parsing):
- Find Markdown link patterns: `[text](target)` and `[text](target "title")`
- Accept target forms:
  - `mnc:NODEID`
- Ignore code blocks (normative per spec):
  - Track fenced code blocks that start at any line beginning with ``` or ~~~ (optionally followed by an info string).
  - While inside a fence, do not extract inline links until the matching closing fence of the same marker (``` or ~~~) is encountered.

Algorithm sketch:
1) Iterate through `body_lines` with a code-fence toggle.
2) When not in code fence:
   - scan for `](`, then parse backwards to matching `[` for link text
   - parse forward to matching `)` respecting quoted title
   - if `target` startswith `mnc:`:
     - `dst = target after mnc:`, validate as NodeID
     - `md_title` if present (the Markdown title attribute)
     - record `InlineLink(src=node.id, dst, text, md_title, span)`

Defaults from inline links:
- Edge exists: `src -> dst`
- If `md_title` exists, treat it as default `type` (string) unless overridden by explicit link line.

---

# 7) Build final edge set (derivation + overrides)

### 7.1 Create derived edges
Initialize map:
- `edges[(src, dst, directed=true)] = Edge(props={})`

For each `InlineLink`:
- Create / merge edge entry
- If inline `md_title` exists:
  - set `edge.props["type"] = md_title` using **last-wins** for repeated inline links with the same `(src, dst)`, consistent with `spec/mnc-v0.3.md`.

### 7.2 Apply explicit link lines
For each `LinkOverride` in file order:
- Determine edge key:
  - if directed: `(a,b,true)`
  - else: represent undirected as `(min(a,b), max(a,b), false)`
- If edge exists: merge props (later override keys)
- If edge does not exist:
  - create edge entry (structural-only edge)

Edge identity (normative per spec):
- If `id` is present: (src, dst, directedness, id)
- If `id` is absent: (src, dst, directedness, ∅)

For the same identity, merge properties in file order; later keys override earlier keys.

---

# 8) Validation and diagnostics (recommended)
Produce warnings/errors but keep parsing resilient.

Errors (fail parse):
- Missing/invalid canvas header
- Invalid block header forms
- Invalid NodeID syntax in headers

Warnings (continue):
- Duplicate NODEID (recommended as an error in default v0.3 parsing)
- Inline links to missing NODEIDs (allow; resolve later; warn)
- Link overrides referencing unknown nodes (warn)
- Malformed rule/link/property tokens (warn, preserve raw line if possible)

---

# 9) Canonical save (normative)

Canonical save MUST follow the ordering and property-order rules defined in `spec/mnc-v0.3.md` (Section 12). This section describes one implementation approach consistent with the spec.

1) Emit header.
2) Emit styles sorted by name. Within each style block, emit `- key: value` lines in the **original authored order** (preserve insertion order).
3) Emit rules in original order.
4) Emit nodes sorted by NODEID:
   - node header with properties in canonical order (pos, size, tags, style, magnet, layer, seq, then others)
   - emit body lines exactly as stored
5) Emit link override lines sorted as spec states (if you canonicalize them).
   - Important: canonicalizing link overrides is optional if you rely primarily on inline links; but determinism is useful.

---
