---
generated: '2026-09-02'
method: probed
source: https://api.apidash.dev/graphql
---

# API Dash and GraphQL — no GraphQL API is published

**API Dash does not publish a GraphQL API.** This file previously contained a single heading,
"# API Dash GraphQL API", and `apis.yml` carried a `type: GraphQL` pointer at it. That pointer
asserted a surface this company does not serve, so it was removed on 2026-09-02 and this file now
records what was actually measured.

## What was probed

| URL | Status | Result |
|---|---|---|
| `https://api.apidash.dev/graphql` | 404 | `{"detail":"Not Found"}` — the FastAPI app has no GraphQL route |
| `https://apidash.dev/graphql` | 200 | Soft-404. The SPA edge returns the same 42,967-byte HTML shell for every path, including a random control path that cannot exist |

Introspection was therefore never attempted: there is no endpoint to introspect.

## Where the GraphQL association actually comes from

API Dash is a **GraphQL client**, not a GraphQL provider. Its own agent guide
([AGENTS.md](https://github.com/foss42/apidash/blob/main/AGENTS.md)) lists the mainline baseline as
"GraphQL requests — Supported through HTTP POST body construction", executed by the first-party
`better_networking` package ("Simplified Networking. Support for sending REST & GraphQL API
Requests", pub.dev, publisher `apidash.dev`). The `graphql`, `graphql-api` and `graphql-client`
topics on the GitHub repository describe that consumer capability.

That is a real and useful thing about this company, and it is recorded where it belongs — as
`client_interoperability` in `conformance/api-dash-conformance.yml` — rather than as a contract
API Dash publishes.

The machine-readable contract this company *does* publish is REST: OpenAPI 3.1.0 with 57
operations at `https://api.apidash.dev/openapi.json`, harvested to `openapi/`.
