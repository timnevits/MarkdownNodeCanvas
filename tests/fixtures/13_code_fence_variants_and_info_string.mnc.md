# tests/fixtures/13_code_fence_variants_and_info_string.mnc.md
# Purpose: Verify both fence variants are supported (``` and ~~~) and info strings do not affect behavior.
# Expected (when fences are treated as code fences):
# - Only edge A01 -> A04 exists
# - No edges A01 -> A02 or A01 -> A03

= canvas: Code Fence Variants v0.2

== A01: Source
Real edge: [Real](mnc:A04 "Control")

```python
This is code (python fence). Should NOT produce edges:
[Nope1](mnc:A02 "Control")
```

~~~
This is code (tilde-style fence stand-in). Should NOT produce edges:
[Nope2](mnc:A03 "Control")
~~~

== A02: Fake Target 1
Should not be linked.

== A03: Fake Target 2
Should not be linked.

== A04: Real Target
Should be linked from A01.