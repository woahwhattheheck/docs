---
description: >-
  Changes since v2.3.0 included in v2.4.0 — OpenTelemetry instrumentation,
  pREST Studio dependency bump, and pgvector nearest-neighbor / distance-filter
  support.
---

# Changes since v2.3.0 (in v2.4.0)

**Released as [v2.4.0](v2.4.0.md).** The commits below were merged after [v2.3.0](v2.3.0.md) and are included in the v2.4.0 release. Compare: [v2.3.0...v2.4.0](https://github.com/prest/prest/compare/v2.3.0...v2.4.0).

| Commit | PR | Summary |
|--------|-----|---------|
| `496f893` | [#1003](https://github.com/prest/prest/pull/1003) | Opt-in OpenTelemetry (push) instrumentation — traces, metrics, slog→OTel log bridge — plus a local SigNoz dev stack |
| `e8dca3b` | [#1004](https://github.com/prest/prest/pull/1004) | pREST Studio dependency upgrade; auth-dialog and tool-invocation fixes |
| `e0cd7bd` | [#1011](https://github.com/prest/prest/pull/1011) | pgvector nearest-neighbor ordering (`_korder`) and distance-threshold filtering (`:vecdist`) |

---

## #1003 — OpenTelemetry instrumentation + SigNoz dev stack

Adds **opt-in** OTLP/gRPC push telemetry: HTTP, Postgres, and MCP traces; `http.server.*` / `db.client.operation.duration` / `db.sql.connection.*` metrics; and a `slog` → OTel logs bridge that still writes to stdout. No new HTTP route is added (no `/metrics` scrape endpoint), so the attack surface is unchanged.

New `[otel]` config section (`PREST_OTEL_*` env overrides):

| Key | Default | Purpose |
|---|---|---|
| `enabled` | `false` | Master switch — zero overhead and no outbound connections when off |
| `service_name` | `prestd` | Resource `service.name` |
| `endpoint` | _(unset)_ | OTLP gRPC collector `host:port` |
| `protocol` | `grpc` | Only `grpc` for now |
| `sample_ratio` | `1.0` | Trace head sampling, clamped `0.0–1.0` |
| `metrics_interval` | `15s` | Metric export period |
| `insecure` | `false` | Disable TLS to collector (local/dev) |
| `db_statement` | `false` | Record SQL text on DB spans (trusted envs only) |

Setup fails closed: if the exporter can't be built at startup, pREST logs a warning and keeps serving with telemetry disabled. The PR also adds graceful shutdown (`SIGINT`/`SIGTERM` draining via `signal.NotifyContext`) as a prerequisite for flushing telemetry on exit. A self-contained SigNoz stack lives under `dev/signoz/` (`make signoz-up` / `make signoz-down`) for local viewing.

## #1004 — pREST Studio dependency upgrade

Routine dependency bump for the embedded Studio UI. Fixes the auth dialog not reflecting saved credentials / "remember me" state on reopen, and avoids building incomplete MCP tool-call requests when argument construction fails. No config or API changes.

## #1011 — pgvector KNN ordering and distance filtering

Adds two query-parameter forms for `vector`-typed columns (requires the `pgvector` extension), both restricted to a fixed metric whitelist (`l2`/`euclidean`, `cosine`/`cos`, `ip`/`inner`/`dot`, `l1`/`manhattan`) and validated end-to-end — column via identifier validation, vector literal by round-tripping through `ParseFloat`/`FormatFloat`, threshold as a bound parameter:

| Parameter | Form | Example | Effect |
|---|---|---|---|
| `_korder` | `<column>:<metric>:<vector>` | `_korder=embedding:l2:[1,0,0]` | Orders by nearest-neighbor distance (KNN); composes with `_order` as an additional sort term |
| `<column>:vecdist` | `<metric>:<comparison>:<vector>:<threshold>` | `embedding:vecdist=l2:lt:[1,0,0]:0.5` | Filters rows by distance threshold; comparison is restricted to `=`, `!=`, `<`, `<=`, `>`, `>=` |

Malformed metrics, non-numeric vector elements, oversized vectors (>16000 dims, pgvector's own limit), non-scalar comparisons (e.g. `like`), and dimension mismatches all return `400` rather than reaching the database unsafely.

---

## Related

- [v2.4.0 release notes](v2.4.0.md)
- [v2.3.0 release notes](v2.3.0.md)
- [Releases](README.md)
