# tests/fixtures/09_code_fence_inline_links.mnc.md
# Purpose: Inline links inside fenced code blocks are ignored.
# Recommended behavior: IGNORE links inside code fences to avoid accidental edges.
# Expected (when fences are treated as code fences):
# - Only edge A01 -> A03 exists
# - No edge A01 -> A02

= canvas: Code Fence Inline Links v0.2

== A01: Source
This should be a real edge: [Real](mnc:A03 "Control")

```
This is code and should not produce an edge:
[Fake](mnc:A02 "Control")
```

== A02: Fake Target
Should not be linked from A01 if code fences are ignored.

== A03: Real Target
Should be linked from A01.