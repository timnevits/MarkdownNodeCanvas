# tests/fixtures/12_undirected_vs_directed.mnc.md
# Purpose: Validate directed and undirected explicit links are both parsed and kept distinct.
# Expected:
# - Directed edge A01 -> A02 exists
# - Undirected edge A01 - A02 exists (distinct from directed)
# (Whether UI renders both is up to the application.)

= canvas: Directed vs Undirected v0.2

== A01: Node A
No inline links.

== A02: Node B
No inline links.

- link: A01 -> A02 id=D1 type=Control route=straight line=solid arrow=end
- link: A01 - A02 id=U1 type=Coupled route=auto line=dotted arrow=none