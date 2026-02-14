# tests/fixtures/04_escaping_directives_everywhere.mnc.md
# Purpose: Escaped directive-looking lines are literal text everywhere.
# Expected:
# - Escaped lines at top-level are NOT parsed as directives (treated as notes or ignored)
# - Escaped lines inside node body remain literal text
# - One actual style block and one actual node block are parsed

= canvas: Escaping v0.3

\= canvas: This is literal text, not a header
\== style: NotAStyle
\- rule: #x -> style Default
\- link: A -> B

== style: Default
- border: #666666

== A01: Escapes in Body
Literal directive markers in body:
\== not a header
\= not a header
\- link: not a link
\- rule: not a rule