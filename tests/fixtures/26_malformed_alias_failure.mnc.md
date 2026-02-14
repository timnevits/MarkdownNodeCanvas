# tests/fixtures/26_malformed_alias_failure.mnc.md
# Purpose: Malformed alias syntax is a deterministic validation error.
# Expected: FAIL
# Expected error code: E_ALIAS_MALFORMED
# Expected:
# - Target starts with mnc:" but is not a valid closed alias token.
# - Validation fails deterministically with E_ALIAS_MALFORMED.

= canvas: Malformed Alias Failure v0.3

== A01: Source
Malformed alias [x](mnc:"Unclosed Alias).

== B01: Unclosed Alias
Present but should not be resolved due to malformed syntax.
