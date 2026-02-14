# tests/fixtures/27_alias_to_id_canonicalization.mnc.md
# Purpose: Canonical save rewrites alias links to ID links.
# Expected: PASS
# Expected after canonical save:
# - Node A01 body link target is rewritten from mnc:"Target Node" to mnc:T01.
# - Link text and optional markdown title remain unchanged.
# - Canonical output contains no mnc:"..." alias targets.

= canvas: Alias to ID Canonicalization v0.3

== A01: Source
Alias link [jump](mnc:"Target Node" "Choice").

== T01: Target Node
Canonical identity target.
