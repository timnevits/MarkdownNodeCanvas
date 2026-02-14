# tests/fixtures/21_tag_precedence_by_order.mnc.md
# Purpose: Tag precedence is determined by node tag order.
# Expected: PASS
# Expected:
# - Style for #a sets border=#111111 and bg=#aaaaaa.
# - Style for #b sets border=#222222.
# - Node A01 tags are #a,#b so border resolves to #222222 (later tag wins).
# - Reversing tag order would change only conflicting keys deterministically.

= canvas: Tag Precedence by Order v0.3

== style: #a
- border: #111111
- bg: #aaaaaa

== style: #b
- border: #222222

== A01: Ordered Tags (tags=#a,#b)
Node appearance conflict test.
