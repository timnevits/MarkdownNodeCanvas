# tests/README.md
This folder contains test fixtures intended to validate Markdown Node Canvas (MNC) v0.3 parsing behavior.

These fixtures are intentionally small and targeted. They are designed to support:

- Unit tests for a parser/loader
- Golden-file (round-trip) tests for canonical save
- Edge-derivation tests (inline links → edges)
- Link override merge tests
- Escaping and directive-precedence tests

## Suggested test categories

1. **Structural parsing**
   - Header required and first non-empty line
   - `== style:` vs node headers
   - Block termination on next `==`

2. **Directive precedence**
   - `- link:` and `- rule:` must be directives only at top-level
   - Inside node bodies they are Markdown text

3. **Escaping**
   - `\==`, `\=`, `\- link:`, `\- rule:` treated as literal text everywhere

4. **Properties**
   - `key=value` and flags
   - quoted values with `\"` and `\\`
   - unknown keys preserved

5. **Inline link extraction**
   - `mnc:NODEID` links create directed edges
   - Markdown title attribute becomes default edge `type`

6. **Link overrides**
   - Override metadata for derived edges
   - Create structural-only edges
   - Multiple overrides: later overrides earlier keys

7. **Canonicalization**
   - Deterministic ordering (styles, nodes, links)
   - Standard property order
   - Body preserved byte-for-byte (except newline normalization)

## How to use

- Each fixture is an `.mnc.md` file under `tests/fixtures/`.
- A test harness can:
  1) parse fixture
  2) assert AST expectations (nodes, styles, rules, edges)
  3) optionally canonical-save and compare to an expected `.canon.mnc.md`

