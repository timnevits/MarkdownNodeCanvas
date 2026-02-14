# tests/fixtures/25_title_alias_missing_failure.mnc.md
# Purpose: Title alias fails when target title does not exist.
# Expected: FAIL
# Expected error code: E_ALIAS_UNRESOLVED
# Expected:
# - Alias mnc:"No Such Node" resolves to no nodes.
# - Validation fails deterministically with E_ALIAS_UNRESOLVED.

= canvas: Title Alias Missing Failure v0.3

== A01: Source
Missing link [x](mnc:"No Such Node").

== B01: Existing Node
Not referenced by alias.
