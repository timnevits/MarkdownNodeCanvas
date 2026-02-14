# tests/fixtures/03_directive_precedence_in_node_body.mnc.md
# Purpose: Inside node bodies, only unescaped "== " starts a new block.
#          "- link:" and "- rule:" must remain Markdown text within a node body.
# Expected:
# - One node A01 with body containing literal "- link:" and "- rule:" lines
# - One explicit link line at top-level should parse
# - One rule line at top-level should parse

= canvas: Directive Precedence v0.3

- rule: #x -> style Default

== style: Default
- border: #666666

== A01: Body Contains Fake Directives (pos=10,10 size=200x120 tags=#x style=Default)
These two lines should remain in the node body as Markdown:

- link: A01 -> A02 route=bezier
- rule: #y -> style Danger

And this is a real inline edge: [Go](mnc:A02 "Choice")

== A02: Target Node
Reached.

# This line is a real top-level link override (should parse):
- link: A01 -> A02 id=L1 route=orthogonal line=dashed width=2