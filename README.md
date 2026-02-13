# Markdown Node Canvas (MNC)

A Markdown-native, LLM-friendly, deterministic node canvas format.

Markdown Node Canvas (MNC) is a text-first format for building node-based canvases that support:

- Narrative plotting
- Interactive fiction
- System design
- Knowledge graphs
- Large idea dumps organized over time

The single source of truth is a readable Markdown file.  
The canvas UI is a projection of that file.



---

## Why MNC Exists

Most visual canvas tools:

- Store data in opaque formats (JSON-heavy or binary)
- Make the UI the source of truth
- Are difficult to diff, version, or edit programmatically

MNC is designed to be:

- Human-editable

- LLM-editable

- Deterministic

- Diff-friendly

- Minimal in syntax

- Structured enough for automation

  

---

## Core Concepts

An MNC file contains:

- A required canvas header
- Optional style definitions
- Optional tag-to-style rules
- Node blocks (Markdown bodies)
- Inline node links (authoritative edges)
- Optional link override lines (styling/metadata)

Inline Markdown links define graph edges.  
The graph is derived from the text.



---

## Quick Example

```mnc
= canvas: Simple Auth Flow v0.2

== style: Service
- shape: rounded
- bg: #1e1e1e
- text: #ffffff

- rule: #service -> style Service

== A01: User Login (pos=100,100 size=260x160 seq=1 tags=#decision)
User submits credentials.

Choose:
- [Validate](mnc:SVC1 "Control")
- [Reject](mnc:END1 "Choice")

== SVC1: Auth Service (pos=500,100 size=260x160 seq=2 tags=#service)
Responsible for JWT validation.

Calls:
- [Token Store](mnc:DB1 "Data")

== DB1: Token Store (pos=900,100 size=200x140 seq=3 tags=#service)

== END1: Access Denied (pos=500,300 size=200x120 seq=4)
```



### **What this does**

- Each == NODEID: block defines a node.
- Node bodies are raw Markdown.
- [Text](mnc:TARGET) creates a directional edge.
- The optional Markdown title attribute becomes the default edge type.
- Styles and rules control rendering.
- Layout hints like pos= and size= are optional.



------



## **Edge Model**



### **Inline Links (Authoritative)**

```
[Go](mnc:TARGET)
```



- Always directional (current node → TARGET)
- Removing the link removes the edge
- Optional "Type" sets default edge type



### **Explicit Link Overrides**

```
- link: A01 -> SVC1 route=orthogonal line=solid
```

Used to:

- Override styling
- Add metadata
- Create structural-only edges (optional)

Inline links are preferred for narrative and flow modeling.



------

## **Design Principles**

- **Markdown-first**: Node bodies are pure Markdown.
- **Minimal grammar**: Few reserved prefixes.
- **No indentation semantics**.
- **Deterministic canonicalization**.
- **Extensible but controlled styling**.
- **LLM-friendly parsing model**.



------

## **Repository Structure**

```
markdown-node-canvas/
  README.md
  spec/
    mnc-v0.2.md
    mnc-v0.2.ebnf
    parsing-outline.md
    scaling.md
  examples/
    interactive-fiction.mnc.md
    system-design.mnc.md
  tests/
    README.md
    fixtures/
      01_header_required.mnc.md
      02_header_first_nonempty.mnc.md
      03_directive_precedence_in_node_body.mnc.md
      04_escaping_directives_everywhere.mnc.md
      05_properties_and_quotes.mnc.md
      06_inline_links_and_types.mnc.md
      07_link_overrides_merge.mnc.md
      08_structural_only_edges.mnc.md
      09_code_fence_inline_links.mnc.md
      10_canonicalization_smoke.mnc.md
      11_style_rules_resolution_smoke.mnc.md
      12_undirected_vs_directed.mnc.md
      13_code_fence_variants_and_info_string.mnc.md
      14_column0_whitespace_and_tabs.mnc.md
      15_crlf_normalization.mnc.md
      16_duplicate_nodeid_error.mnc.md
      17_malformed_props_tokenization.mnc.md
      18_inline_link_edge_cases.mnc.md
      19_undirected_edge_normalization.mnc.md
      20_override_type_wins_over_inline_default.mnc.md
```



- mnc-v0.2.md — Normative specification
- mnc-v0.2.ebnf — Compact formal grammar
- parsing-outline.md — Reference implementation guide
- examples/ — Sample canvases
- tests/fixtures/ — Edge case validation files



------

## **File Extension**

Recommended:

```
*.mnc.md
```

This preserves Markdown tooling compatibility while signaling format identity.



------

## **Scaling**

MNC is designed to scale to hundreds of nodes by:

- Using multiple canvases
- Creating hub/index canvases
- Tag-based filtering
- Collapsible container nodes
- Deterministic IDs and sequencing

See spec for detailed guidance.



------

## **Status**

Current version: **v0.2**

This version defines:

- Inline links as authoritative graph edges
- Optional link overrides
- Deterministic canonical save rules
- Compact EBNF grammar
- Reference parsing model

Future versions may extend semantics without breaking backward compatibility.



------

## **License**

This project is licensed under the MIT License.

Copyright © 2026 Timothy J. Nevits

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
