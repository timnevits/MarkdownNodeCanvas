# tests/fixtures/23_title_alias_unique_success.mnc.md
# Purpose: Title alias link resolves when title is unique.
# Expected: PASS
# Expected:
# - Alias [go](mnc:"Target Node") resolves uniquely to NODEID T01.
# - Derived edge is A01 -> T01.
# - No alias validation errors.

= canvas: Title Alias Unique Success v0.3

== A01: Source
See [go](mnc:"Target Node" "Choice").

== T01: Target Node
Unique title target.
