# tests/fixtures/15_crlf_normalization.mnc.md
# Purpose: Ensure CRLF input is accepted and newline normalization does not corrupt bodies.
# NOTE: To actually test CRLF, save this file with Windows line endings (\r\n).
# Expected:
# - Parse succeeds.
# - Body text lines are preserved (aside from newline normalization policy).
# - Inline edge A01 -> A02 exists.

= canvas: CRLF Normalization v0.3

== A01: CRLF Node
Line 1
Line 2
Edge: [Go](mnc:A02 "Choice")
Line 4

== A02: Target
Ok.