# tests/fixtures/11_style_rules_resolution_smoke.mnc.md
# Purpose: Ensure rules map tags to styles; multiple rules apply; later rules override earlier.
# Expected:
# - Node A01 has tags #x,#y and base style Default
# - Apply Default, then rule #x -> XStyle, then rule #y -> YStyle
# - If both set same key (e.g., border), YStyle wins due to later rule declaration

= canvas: Style Rules Resolution v0.2

== style: Default
- border: #111111
- bg: #101010

== style: XStyle
- border: #88aaff
- shape: rounded

== style: YStyle
- border: #ff8855
- shadow: hard

- rule: #x -> style XStyle
- rule: #y -> style YStyle

== A01: Styled Node (tags=#x,#y style=Default)
Body.