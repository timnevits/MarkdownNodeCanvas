# tests/fixtures/28_mixed_legacy_new_migration.mnc.md
# Purpose: Mixed legacy/new style constructs migrate deterministically.
# Expected: PASS
# Expected:
# - Legacy style block LegacyPanel is referenced by rule #service -> LegacyPanel.
# - #service unified style also exists and overrides conflicting legacy keys.
# - Effective #service style has:
#   border=#00ff00 (from unified #service, overrides legacy #333333)
#   bg=#101010 (from legacy LegacyPanel)
#   text=#ffffff (from unified #service)
# - Canonical save emits only == style: #service block and no - rule: lines.
# - Canonical save does not emit legacy named style block == style: LegacyPanel.

= canvas: Mixed Legacy New Migration v0.3

== style: LegacyPanel
- border: #333333
- bg: #101010

- rule: #service -> style LegacyPanel

== style: #service
- border: #00ff00
- text: #ffffff

== A01: Service Node (tags=#service)
Legacy + unified merge target.
