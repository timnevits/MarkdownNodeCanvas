# tests/fixtures/24_title_alias_ambiguity_failure.mnc.md
# Purpose: Title alias fails when multiple nodes share the same title.
# Expected: FAIL
# Expected error code: E_ALIAS_AMBIGUOUS
# Expected:
# - Alias mnc:"Shared Title" matches B01 and B02.
# - Validation fails deterministically with E_ALIAS_AMBIGUOUS.

= canvas: Title Alias Ambiguity Failure v0.3

== A01: Source
Ambiguous link [x](mnc:"Shared Title").

== B01: Shared Title
First duplicate title.

== B02: Shared Title
Second duplicate title.
