# tests/fixtures/07_link_overrides_merge.mnc.md
# Purpose: Link overrides merge with derived edges; later overrides win per key.
# Expected:
# - Derived edge A01 -> A02 exists from inline link
# - Override supplies route/line/width
# - Second override changes route and adds label; route should be final value

= canvas: Link Override Merge v0.2

== A01: Source
[To target](mnc:A02 "Control")

== A02: Target
Done.

- link: A01 -> A02 id=E1 route=orthogonal line=solid width=2
- link: A01 -> A02 id=E1 route=bezier label="final label"