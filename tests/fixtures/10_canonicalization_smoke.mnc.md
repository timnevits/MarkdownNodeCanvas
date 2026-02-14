# tests/fixtures/10_canonicalization_smoke.mnc.md
# Purpose: A compact file to canonical-save and compare ordering and property formatting.
# Suggested assertions after canonical save:
# - Styles sorted by name
# - Nodes sorted by NODEID
# - Link lines sorted by (source, target, id)
# - Node property order: pos, size, tags, style, magnet, layer, seq, others
# - Link property order: id, type, route, line, width, color, arrow, label, style, others
# - Bodies preserved

= canvas: Canonicalization Smoke v0.3

== style: Zeta
- border: #000000

== style: Alpha
- border: #ffffff

== B02: Second Node (seq=2 pos=20,20 size=200x120 tags=#b,#a style=Zeta magnet layer=front extra=ok)
Body line 1
Body line 2

== A01: First Node (pos=10,10 size=100x60 tags=#a style=Alpha seq=1)
Edge to [B02](mnc:B02 "Control")

- link: A01 -> B02 type=Control id=L2 width=2 route=orthogonal line=solid arrow=end label="call" color=#88aaff
- link: A01 -> B02 id=L1 route=bezier line=dashed