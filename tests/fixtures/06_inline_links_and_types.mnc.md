# tests/fixtures/06_inline_links_and_types.mnc.md
# Purpose: Inline links create directed edges. Title attribute becomes default edge type.
# Expected:
# - Derived edges:
#   A01 -> A02 type=Choice
#   A01 -> A03 type=Reference (or absent type) depending on your default policy
#   A02 -> A03 type=Data
# - No explicit link overrides

= canvas: Inline Links v0.2

== A01: Start
Choices:
- [Go left](mnc:A02 "Choice")
- [Look around](mnc:A03)

== A02: Left Path
You find a terminal. It queries [A03](mnc:A03 "Data").

== A03: Observation
End.