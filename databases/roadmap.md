---
description: >-
  pREST multi-database roadmap — MySQL 8 shipped in v2.5.0; MariaDB, SQLite,
  and SQL Server are still planned.
---

# Database roadmap

This page describes **planned** SQL adapters for pREST. Items still marked Roadmap are not installable. PostgreSQL is native. MySQL 8.0.19+ shipped in [v2.5.0](../releases/v2.5.0.md) — see [Databases](README.md).

*Last updated: October 7, 2026*

---

## Why this order?

Developer usage (for example Stack Overflow surveys) weights **PostgreSQL, MySQL, SQLite, and SQL Server** heavily for application builders. Broader market-visibility rankings put Oracle and warehouses higher, but they are a poorer early fit for open-source adoption per engineering hour.

pREST therefore prioritizes:

1. Certify the **PostgreSQL-compatible** family on the existing adapter  
2. **MySQL family** (high SEO + open-source fit)  
3. **SQLite** (local/file DX)  
4. **SQL Server / Azure SQL** (enterprise)  
5. **Oracle** or **analytical read-only** based on demand  

---

## Adapter families (target)

```text
PostgreSQL family — native today
├── PostgreSQL
├── CockroachDB (certify)
├── YugabyteDB (certify)
└── Aurora PostgreSQL (certify)

MySQL family — Phase 2
├── MySQL 8 — shipped in v2.5.0
├── MariaDB (dialect profile, not shipped)
└── TiDB, Aurora MySQL (certify after MariaDB profile)

Embedded family — Phase 3
└── SQLite

SQL Server family — Phase 4
├── SQL Server
└── Azure SQL

Later / separate
├── Oracle (Phase 5)
└── Analytical read-only: DuckDB, ClickHouse, Snowflake, BigQuery
```

---

## Phases

| Phase | Focus | Status |
|-------|--------|--------|
| **1** | Certify CockroachDB, YugabyteDB, Aurora PostgreSQL; Timescale E2E ([v2.2.0](../releases/v2.2.0.md) / [#988](https://github.com/prest/prest/pull/988)); Timescale adapter + multi-adapter routing ([v2.3.0](../releases/v2.3.0.md) / [#999](https://github.com/prest/prest/pull/999)) | **In progress** |
| **2** | MySQL 8 dialect adapter ([v2.5.0](../releases/v2.5.0.md) / [#1048](https://github.com/prest/prest/pull/1048)); MariaDB profile and TiDB + Aurora MySQL still planned | **Partial** — MySQL 8 shipped |
| **3** | SQLite native — one file → REST/MCP | Roadmap |
| **4** | SQL Server + Azure SQL | Roadmap |
| **5** | Oracle vs analytical read-only track | Undecided |

Current support labels: [Databases](README.md).

---

## Analytical databases (separate track)

Snowflake, Databricks, BigQuery, ClickHouse, and DuckDB speak SQL but are a poor fit for generic transactional CRUD (`POST`/`PATCH`, row updates, transactions).

Planned direction:

```text
adapter mode: transactional
adapter mode: analytical-read-only
```

Analytical mode would emphasize schema exploration, controlled queries, scripts, and MCP — not forced row-level CRUD.

---

## Architecture direction

Future adapters should isolate **SQL generation and schema behavior**, not only connection drivers. Design direction (not yet implemented in this docs pass):

- Explicit `Dialect` / `Capabilities` interfaces  
- Shared contract-test suite (discovery, CRUD, pagination, JSON, MCP, ACL)  
- Separate modules where practical (`prest-adapter-postgres`, `prest-adapter-mysql`, …)  

---

## Related

- [Databases](README.md)
- [Homepage](../README.md)
- [Key features](../prestd/prestd-key-features.md)
