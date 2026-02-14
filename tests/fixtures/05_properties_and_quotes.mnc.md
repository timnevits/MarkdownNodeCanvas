# tests/fixtures/05_properties_and_quotes.mnc.md
# Purpose: Parse properties with key=value, flags, quoted values, and escaped quotes.
# Expected:
# - Node props: pos, size, tags list, style string with spaces, magnet flag, layer, seq
# - Unknown prop preserved: note="..."
# - Quote escaping handled: \" and \\

= canvas: Properties and Quotes v0.3

== style: Dark Panel
- bg: #111111
- text: #e6e6e6

== A01: Props Test (pos=100,60 size=520x320 tags=#one,#two style="Dark Panel" magnet layer=back seq=42 note="He said: \"hello\" and used \\ backslash")
Body.