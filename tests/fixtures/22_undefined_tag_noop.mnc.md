# tests/fixtures/22_undefined_tag_noop.mnc.md
# Purpose: Undefined tags are valid no-ops.
# Expected: PASS
# Expected:
# - Node A01 has tags #known,#missing.
# - #known applies bg=#00aa00.
# - #missing has no style definition and causes no validation error.
# - Effective appearance includes #known contributions only.

= canvas: Undefined Tag Noop v0.3

== style: #known
- bg: #00aa00

== A01: Uses Missing Tag (tags=#known,#missing)
Missing tag should be ignored without failure.
