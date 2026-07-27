---
description: >-
  Download and run pREST — Docker, Homebrew, or Go — for instant
  REST and MCP APIs on SQL databases (PostgreSQL native today).
---

# Get pREST

Download and run pREST to get instant REST and MCP APIs for your SQL database.
PostgreSQL is the native adapter today — see [Databases](../databases/README.md).
If this is your first install, start with [Start with Docker](start-with-docker.md) or [Deploying with Docker](../deployment/deploying-with-docker.md). Compare channels in [Distribution](distribution.md).

* Use the Docker Hub image
* Use the GitHub image
* [Homebrew](start-with-homebrew.md) — `prestd` (homebrew-core) and `prest-mcp` ([prest/tap](https://github.com/prest/homebrew-tap))
* Download from [source](start-with-golang.md) and run
* GitHub [releases](https://github.com/prest/prest/releases)
* [Distribution channels](distribution.md) — which method for server vs MCP adapter

## Latest: v2.4.0

**[v2.4.0](../releases/v2.4.0.md)** adds opt-in OpenTelemetry instrumentation, pgvector KNN ordering / distance filtering, and a Studio dependency upgrade — plus Studio from [v2.2.0](../releases/v2.2.0.md) and MCP from [v2.1.0](../releases/v2.1.0.md). See the [upgrade guide](../get-started/upgrading-to-v2.md), [pREST Studio](../get-started/prest-studio.md), [MCP over HTTP](../get-started/mcp-over-http.md), and [AI and MCP](../ai/README.md).

* **Binary:** [v2.4.0 assets](https://github.com/prest/prest/releases/tag/v2.4.0)
* **Docker:** `prest/prest:v2.4.0`
* **Go:** `go install github.com/prest/prest/v2/cmd/prestd@v2.4.0`
* **MCP adapter:** `brew install prest/tap/prest-mcp` or see [Install pREST MCP Adapter](../ai/install-prest-mcp.md)

Database support labels: [Databases](../databases/README.md).

## Related

- [Homepage](../README.md)
- [Acronyms](../prestd/acronyms.md) ([REST](../prestd/acronyms.md#rest), [MCP](../prestd/acronyms.md#mcp))
- [Databases](../databases/README.md)
- [Get Started](../get-started/README.md)
- [v2.4.0 release notes](../releases/v2.4.0.md)
