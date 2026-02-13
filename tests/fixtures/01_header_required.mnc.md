# tests/fixtures/01_header_required.mnc.md
# Purpose: Ensure parser requires first non-empty line to be the canvas header.
# Expected: FAIL (header missing)

== A01: Node Without Header
This should not parse because the canvas header is missing.