# tests/fixtures/17_malformed_props_tokenization.mnc.md
# Purpose: Define behavior for malformed property tokenization.
# This fixture includes:
# - unclosed quote
# - stray ')' in an unquoted value
# - weird spacing
# Recommended: FAIL on unclosed quote (hard parse error), since it makes tokenization ambiguous.
# Expected: FAIL (unclosed quote in style="...)

= canvas: Malformed Props v0.2

== A01: Bad Props (pos=10,10 size=200x120 style="Dark Panel tags=#x)
This should fail to parse due to an unclosed quote in props.

== A02: Unreached
If you parse this, you're not failing on malformed props.