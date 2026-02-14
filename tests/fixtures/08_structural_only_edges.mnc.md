# tests/fixtures/08_structural_only_edges.mnc.md
# Purpose: Explicit link lines can create edges even without inline links.
# Expected:
# - Edge A01 -> A02 exists (structural-only)
# - Undirected edge A02 - A03 exists

= canvas: Structural Only Edges v0.3

== A01: Node One
No inline edges.

== A02: Node Two
No inline edges.

== A03: Node Three
No inline edges.

- link: A01 -> A02 id=S1 type=Dependency route=straight line=dashed arrow=end
- link: A02 - A03 id=S2 type=Coupled line=dotted arrow=none