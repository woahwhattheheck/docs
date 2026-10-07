---
description: >-
  Use MySQL 8.0.19+ with pREST — native dialect adapter (v2.5.0), REST CRUD,
  and documented gaps. MariaDB and MCP are not on this adapter.
---

# MySQL

**pREST** v2.5.0+ exposes **MySQL 8.0.19+** through a native dialect adapter. Set `engine = "mysql"`. An empty `engine` stays PostgreSQL.

*Last updated: October 7, 2026* · **Label:** Native (MySQL 8.0.19+)

MariaDB, TiDB, and Amazon Aurora MySQL are **not** this adapter. MCP (`/_mcp`) is not implemented for MySQL in v2.5.0.

---

## Supported features

| Capability | Status |
|------------|--------|
| Connection | `engine = "mysql"` only — URLs are not sniffed |
| Schema discovery | supported (`information_schema`; system schemas hidden) |
| CRUD | supported, with write differences below |
| Filtering / ordering / pagination | supported (`LIMIT` / `OFFSET`) |
| Scripts (`/_QUERIES`) | supported — `?` placeholders and backtick identifiers |
| ACL / permissions | same TOML allowlist as PostgreSQL |
| MCP (`/_mcp`) | not on this adapter |
| pgvector / ltree / `tsquery` | unsupported operators |

---

## Connection

```toml
engine = "mysql"

[pg]
url = "mysql://user:pass@tcp(localhost:3306)/mydb"
```

Or environment variables. `PREST_DB_*` is read first, then legacy `PREST_PG_*`:

```sh
export PREST_VERSION=2
export PREST_DB_URL='mysql://user:pass@tcp(localhost:3306)/mydb'
```

Port defaults to **3306** when unset. Postgres defaults (user `postgres`, database `prest`, port 5432) are not applied. A missing user or database fails startup.

TLS: `disable`, `require`, or `skip-verify`. A client cert, key, or root certificate fails startup in this release.

`[[databases]]` entries can set `engine = "mysql"` per alias. See [Multi-database](../get-started/multi-database.md).

---

## Paths

Routes stay `/{database}/{schema}/{table}`.

- `{database}` selects the pool (the alias, or the configured database on one connection).
- `{schema}` is the MySQL database. `/databases` and `/schemas` both list user databases.
- Hidden schemas: `mysql`, `information_schema`, `performance_schema`, `sys`.

---

## Known limitations

- **MySQL 8.0.19+.** The upsert form needs the row alias added in 8.0.19. MariaDB is out of scope.
- **No SQL `RETURNING`.** Insert is followed by `SELECT *` in the same transaction. `UPDATE`/`DELETE` without `_returning` return `{"rows_affected": n}`. With `_returning`, pREST emulates the row image inside a `REPEATABLE READ` transaction.
- **`Prest-Batch-Method: copy`** is a transactional multi-row `INSERT`, not `LOAD DATA`.
- **No `FULL` join.** `INNER`, `LEFT`, `RIGHT`, and `CROSS` are generated.
- **`$ilike` is `LIKE`.** Case follows the column collation. pREST does not wrap the column in `LOWER()`.
- **`$all`, ltree, `$tsquery`, and `vecdist` error** with `unsupported operator`.
- **Scripts** use `?` placeholders and backtick `ident`, not Postgres `$n` and double quotes.

Dialect matrix in the server repo: [`integration/mysql/DIFFERENCES.md`](https://github.com/prest/prest/blob/v2.5.0/integration/mysql/DIFFERENCES.md).

---

## Related

- [v2.5.0 release notes](../releases/v2.5.0.md)
- [Databases](README.md)
- [Database roadmap](roadmap.md)
- [API Reference](../api-reference/README.md)
- [Acronyms](../prestd/acronyms.md)
