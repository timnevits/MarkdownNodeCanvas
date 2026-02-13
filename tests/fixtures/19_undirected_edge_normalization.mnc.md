# tests/fixtures/19_undirected_edge_normalization.mnc.md
# Purpose: If you normalize undirected edges, A - B and B - A should refer to the same undirected edge identity.
# Recommended: normalize endpoints (min/max) for undirected edges.
# Expected (with normalization):
# - Exactly one undirected edge between A01 and A02 with id=U1
# - label should be "second" (later override wins)
# If you choose NOT to normalize, this fixture should be marked FAIL or updated accordingly.

= canvas: Undirected Normalization v0.2

== A01: Node A
No inline links.

== A02: Node B
No inline links.

- link: A01 - A02 id=U1 type=Coupled label="first" route=auto line=dotted arrow=none
- link: A02 - A01 id=U1 label="second"