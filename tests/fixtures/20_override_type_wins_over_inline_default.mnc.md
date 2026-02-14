# tests/fixtures/20_override_type_wins_over_inline_default.mnc.md
# Purpose: Inline link title attribute sets default edge type, but explicit override can replace it.
# Expected:
# - Derived edge A01 -> A02 exists.
# - Inline default type would be "Choice".
# - Override sets type=Control; final edge type MUST be "Control".

= canvas: Override Type Wins v0.3

== A01: Source
Inline edge with default type: [Go](mnc:A02 "Choice")

== A02: Target
Ok.

- link: A01 -> A02 id=E1 type=Control route=orthogonal line=solid width=2