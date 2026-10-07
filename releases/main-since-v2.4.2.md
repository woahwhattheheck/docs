---
description: >-
  Changes merged after v2.4.2 and released in v2.5.0 — MySQL adapter, JOIN ACL,
  plugin builds, and dependency bumps.
---

# Changes since v2.4.2 (in v2.5.0)

**Released as [v2.5.0](v2.5.0.md).** The commits below were merged after [v2.4.2](v2.4.2.md) and ship in that tag. Compare: [v2.4.2...v2.5.0](https://github.com/prest/prest/compare/v2.4.2...v2.5.0).

| Commit | PR | Summary |
|--------|----|---------|
| `76d114d` | [#1048](https://github.com/prest/prest/pull/1048) | MySQL 8 dialect adapter (`engine = "mysql"`) |
| `acbc663` | [#1032](https://github.com/prest/prest/pull/1032) | JOIN columns honour `restrict` ([#364](https://github.com/prest/prest/issues/364)) |
| `254f30a` | [#1033](https://github.com/prest/prest/pull/1033) | Reject bad joins; multiple `_join` values keep order |
| `b84da9b` | [#1039](https://github.com/prest/prest/pull/1039) | Middleware plugin build and load ([#948](https://github.com/prest/prest/issues/948)) |
| `f00df6e` | [#1055](https://github.com/prest/prest/pull/1055) | Go and Studio dependency bumps |

Details: [v2.5.0 release notes](v2.5.0.md).

---

## Related

- [v2.5.0 release notes](v2.5.0.md)
- [v2.4.2 release notes](v2.4.2.md)
- [Releases](README.md)
