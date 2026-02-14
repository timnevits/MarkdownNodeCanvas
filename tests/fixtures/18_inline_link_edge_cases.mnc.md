# tests/fixtures/18_inline_link_edge_cases.mnc.md
# Purpose: Lock in supported inline link extraction behaviors.
# Includes:
# - multiple inline links on one line
# - a normal URL with nested parentheses (should NOT be treated as mnc edge)
# - link text containing an escaped closing bracket \]
# Expected:
# - Derived edges: A01 -> A02, A01 -> A03, A01 -> A04 exist
# - No edge is created for the normal URL link (it is not mnc:)

= canvas: Inline Link Edge Cases v0.3

== A01: Source
One line, multiple mnc links: [A02](mnc:A02 "Choice") and [A03](mnc:A03 "Reference") and [Escaped \] Bracket Text](mnc:A04 "Choice").

Normal URL with parentheses should not be an edge:
[Wikipedia example](https://en.wikipedia.org/wiki/Function_(mathematics))

== A02: Target 2
Ok.

== A03: Target 3
Ok.

== A04: Target 4
Ok.