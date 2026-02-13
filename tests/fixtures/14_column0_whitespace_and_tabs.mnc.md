# tests/fixtures/14_column0_whitespace_and_tabs.mnc.md
# Purpose: Directives are recognized only at column 0 (no leading spaces or tabs).
# Expected:
# - The indented "== style:" line is NOT a style block.
# - The indented "- rule:" line is NOT a rule.
# - The indented "== A00:" line is NOT a node header.
# - The first real structural block is the unindented header, style, rule, and node below.
# - Node A01 body should include lines that look like directives but are indented.

= canvas: Column 0 Directives v0.2

	== style: IndentedNotAStyle
	- border: #ffffff

  - rule: #x -> style IndentedNotARule

  == A00: IndentedNotANode
  This should be treated as a note line (top-level), not a node.

== style: Default
- border: #666666

- rule: #x -> style Default

== A01: Real Node (tags=#x style=Default)
These should be literal text inside the body (they are indented, so not column 0):

	== NotAHeader
  - link: Not a top-level link
	- rule: Not a top-level rule

This IS an inline edge: [Go](mnc:A02 "Choice")

== A02: Target Node
Reached.