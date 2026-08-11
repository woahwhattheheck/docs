---
description: >-
  Changes since v2.4.0 included in v2.4.1 and v2.4.2 — MCP expose enforcement,
  HMAC key minimums, bound script values, and SQL redacted from logs.
---

# Changes since v2.4.0 (in v2.4.1 and v2.4.2)

**Released as [v2.4.1](v2.4.1.md) and [v2.4.2](v2.4.2.md).** The commits below were merged after [v2.4.0](v2.4.0.md) and are included in those two releases. Compare: [v2.4.0...v2.4.2](https://github.com/prest/prest/compare/v2.4.0...v2.4.2).

Upgrade straight to **v2.4.2** — v2.4.1 shipped a script-value screen that was too broad and was relaxed one release later ([#1030](https://github.com/prest/prest/issues/1030)).

| Commit | PR | Tag | Summary |
|---|---|---|---|
| `3cf83bc` | [#1016](https://github.com/prest/prest/pull/1016) | v2.4.1 | MCP endpoint honours `[expose]`; SQL-keyword screen on script values |
| `c354461` | [#1005](https://github.com/prest/prest/pull/1005) | v2.4.2 | Bump `google.golang.org/grpc` 1.81.1 → 1.82.1 |
| `d25fdd5` | [#1015](https://github.com/prest/prest/pull/1015) | v2.4.2 | Bump `github.com/lestrrat-go/jwx/v3` 3.1.1 → 3.2.0 |
| `786d775` | [#1017](https://github.com/prest/prest/pull/1017) | v2.4.2 | HMAC `jwt.key` minimums, `go-jose/v4` migration, `jwt.algo` enforced |
| `9070bda` | [#1023](https://github.com/prest/prest/pull/1023) | v2.4.2 | Credential headers withheld from templates, SQL removed from logs, script path traversal rejected |

---

## #1016 — Expose configuration for the MCP endpoint

`/_mcp` bypassed the exposure middleware entirely, so a deployment that disabled the `/databases`, `/schemas`, or `/tables` REST routes still served its full catalog to any MCP client. The MCP handler now consults the same `[expose]` settings.

- `prest.list_databases`, `prest.list_schemas`, and `prest.list_tables` return **400** with JSON-RPC `{"code":400,"message":"unauthorized listing"}` when their listing is denied, and are dropped from `tools/list`.
- If **any** listing is denied, the per-table `prest.select.{database}.{schema}.{table}` tools are not enumerated at all — their names and descriptions disclose table and column names.
- `prest.describe_table` and `prest.select_table` are always advertised; `[expose]` controls discovery, `[access]` controls reads.
- The listing flags only apply while `expose.enabled = true`. Hiding a listing requires `enabled = true` **and** the specific flag set to `false`.

The same PR added a SQL-keyword screen to values interpolated by `/_QUERIES` scripts, closing an unauthenticated injection that needed no quote or parenthesis (`0 UNION SELECT ... FROM ...`). It proved too broad and was relaxed in [#1023](https://github.com/prest/prest/pull/1023) — see below.

Details: [v2.4.1 release notes](v2.4.1.md).

---

## #1017 — JWT key handling and dependencies

Migrated from the unmaintained `square/go-jose.v2` to `go-jose/go-jose/v4`, which enforces RFC 7518 HMAC key sizes. pREST now validates the key at config load rather than failing at request time:

| `jwt.algo` | Minimum `jwt.key` |
|---|---|
| `HS256` (and the default when unset) | 32 bytes |
| `HS384` | 48 bytes |
| `HS512` | 64 bytes |

An undersized key is discarded, `/auth` is unregistered, and the JWT middleware is removed — **pREST keeps serving, unauthenticated**. Rotate short secrets before upgrading.

`jwt.algo` is also enforced for the first time: it is passed to the parser as the sole permitted signature algorithm, so tokens signed with a different `alg` now return **401**, and a value outside the supported set (including wrong case) returns **500** on every request.

Details: [v2.4.2 release notes](v2.4.2.md).

---

## #1023 — SQL logging, script hardening, and CI permissions

- **Credential headers withheld from templates** — `Authorization`, `Proxy-Authorization`, `Cookie`, `X-Api-Key`, `X-Auth-Token`, `X-Access-Token` render empty in `/_QUERIES` scripts, and cannot be recovered through `sqlVal`.
- **Rejected interpolated values fail with 400** instead of substituting an empty string, with a message pointing at the `sqlVal` helper. The value itself is never echoed back.
- **SQL removed from logs** — script statements are no longer logged on the read or write path; CRUD statements keep `sql` at `debug` but replace parameter values with `parameter_count`.
- **Script path traversal rejected** — `..` segments and symlinks escaping the queries directory return **400** `invalid script path`, checked both lexically and after symlink resolution.
- **Error bodies are JSON-escaped**, so messages containing a quote no longer produce unparseable JSON.
- **CI only:** explicit `permissions:` blocks on the build, lint, and duplicate-issue workflows. No runtime effect.

### Script value screen relaxed ([#1030](https://github.com/prest/prest/issues/1030))

The v2.4.1 screen ran on every value, so a single-word value containing a SQL keyword token — `sao-joao-do-sul` splits on `-` and yields `do` — was silently blanked and the request returned `200` with the wrong rows. The screen now applies only to values containing a space; the character allow-list and the `--` / `::` rejection still apply to everything.

Details: [v2.4.2 release notes](v2.4.2.md).

---

## #1005, #1015 — Dependency bumps

`google.golang.org/grpc` 1.81.1 → 1.82.1 (indirect, used by the OTLP exporters) and `github.com/lestrrat-go/jwx/v3` 3.1.1 → 3.2.0 (JWKS parsing). Both touch only `go.mod` and `go.sum`.

---

## Related

- [v2.4.2 release notes](v2.4.2.md)
- [v2.4.1 release notes](v2.4.1.md)
- [v2.4.0 release notes](v2.4.0.md)
- [Releases](README.md)
