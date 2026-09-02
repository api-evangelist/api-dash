---
name: api-dash-country-lookup
description: Look up country names, ISO codes, flags, geographic/demographic data and subdivisions from the free keyless API Dash APIs.
api: openapi/api-dash-openapi.yml
base_url: https://api.apidash.dev
auth: none
operations:
  - get_country_code_dictionary_country_codes_get
  - get_country_data_country_data_get
  - get_country_flag_country_flag_get
  - get_country_name_country_name_get
  - get_official_country_name_country_officialname_get
  - get_country_subdivisions_country_subdivisions_get
---

# Country lookup on the API Dash APIs

Six read-only operations over `https://api.apidash.dev`. **No API key and no token.** None of these
operations carries a security requirement in the contract, so call them directly.

## Steps

1. **Resolve a code to a name.** `get_country_name_country_name_get` — `GET /country/name`.
   For the full legal form use `get_official_country_name_country_officialname_get`
   (`GET /country/officialname`).
2. **Get the code dictionary.** `get_country_code_dictionary_country_codes_get` —
   `GET /country/codes` — when you need the whole ISO 3166-1 alpha-2/alpha-3 mapping rather than
   one lookup.
3. **Get geographic and demographic data.** `get_country_data_country_data_get` —
   `GET /country/data`.
4. **Get the flag.** `get_country_flag_country_flag_get` — `GET /country/flag`.
5. **Get subdivisions.** `get_country_subdivisions_country_subdivisions_get` —
   `GET /country/subdivisions`. Subdivision data does not exist for every country; the provider's
   MCP README states coverage for AE, AU, CA, CH, CN, ES, IN, JP, KR, SG and US.

## Rules

- **Read the parameter names off the live spec, not off this file.** Every parameter on these
  operations is a query parameter; fetch `https://api.apidash.dev/openapi.json` (or the harvested
  copy at `openapi/api-dash-openapi.yml`) and use the declared names verbatim.
- **The only error the contract declares is 422**, the FastAPI validation envelope
  `{"detail":[{"loc":[...],"msg":"...","type":"..."}]}` served as `application/json`. It is **not**
  RFC 9457 problem+json. A 404 with `{"detail":"Not Found"}` is reachable but undeclared. Nothing
  tells you what a 429 or 5xx looks like, so treat any non-2xx that is not shaped like the 422
  envelope as opaque and back off.
- **No rate-limit headers are returned** and no limit is published. Cloudflare fronts the origin,
  so edge protection may exist at an undisclosed threshold. Be conservative: serialize bulk
  lookups and add your own backoff rather than relying on a `Retry-After` that will not arrive.
- **No pagination.** These return whole payloads.
- Prefer `GET /country/codes` once and cache it over calling `/country/name` in a loop.

## Same capability over MCP

`get_country_info` and `search_countries` in the foss42 MCP server cover this ground and add
forgiving name resolution plus partial-name/region search that REST does not offer. That server is
**local stdio only** — a human must clone and run `github.com/foss42/mcp` first. See
`mcp/api-dash-tool-crosswalk.yml`.
