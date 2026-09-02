---
name: api-dash-text-case-conversion
description: Convert text between naming conventions and novelty formats using the free keyless API Dash text and case conversion endpoints.
api: openapi/api-dash-openapi.yml
base_url: https://api.apidash.dev
auth: none
operations:
  - to_camel_case_case_camel_get
  - to_snake_case_case_snake_get
  - to_kebab_case_case_kebab_get
  - to_pascal_case_case_pascal_get
  - to_constant_case_case_constant_get
  - to_title_case_case_title_get
  - to_title_case_case_title_post
  - camel_to_lower_case_case_camel2lower_get
  - snake_to_lower_case_case_snake2lower_get
  - kebab_to_lower_case_case_kebab2lower_get
  - to_slug_convert_slug_get
  - phone_number_to_numeric_convert_phone2numeric_get
---

# Text and case conversion on the API Dash APIs

27 pure-function operations over `https://api.apidash.dev`. **No API key.** Same input always
yields the same output, nothing is stored, and nothing needs undoing — these are safe to retry.

## Identifier styles (`/case/*`)

Forward conversions, all `GET`, one operation each:
`to_camel_case_case_camel_get`, `to_snake_case_case_snake_get`, `to_kebab_case_case_kebab_get`,
`to_pascal_case_case_pascal_get`, `to_constant_case_case_constant_get`,
`to_camel_snake_case_case_camelsnake_get`, `to_pascal_snake_case_case_pascalsnake_get`,
`to_dot_case_case_dot_get`, `to_cobol_case_case_cobol_get`, `to_train_case_case_train_get`,
`to_flat_case_case_flat_get`, `to_swap_case_case_swap_get`.

Prose styles ship **both** a GET and a POST — use POST when the text is long enough to strain a
URL: `to_lower_case_case_lower_get` / `..._post`, `to_upper_case_case_upper_get` / `..._post`,
`to_capital_case_case_capital_get` / `..._post`, `to_title_case_case_title_get` / `..._post`,
`to_sentence_case_case_sentence_get` / `..._post`.

Reversals back to spaced lowercase: `camel_to_lower_case_case_camel2lower_get`,
`snake_to_lower_case_case_snake2lower_get`, `kebab_to_lower_case_case_kebab2lower_get`.

## Other conversions (`/convert/*`)

`to_slug_convert_slug_get` (URL slug), `phone_number_to_numeric_convert_phone2numeric_get`
(letters in a phone number to digits), `to_leet_convert_leet_get`,
`to_upside_down_convert_upsidedown_get`, `to_mirror_convert_mirror_get`.

## Rules

- **Even the POST variants take their input as query parameters** unless the spec says otherwise.
  Read the declared parameters off `https://api.apidash.dev/openapi.json` before constructing a
  call; do not assume a JSON body.
- **422 is the only declared error.** Malformed or missing input returns the FastAPI validation
  envelope, not RFC 9457 problem+json.
- **No idempotency key exists and none is needed.** These operations have no side effects, so
  retrying on a network failure is always safe. That is a property of the operation, not a
  guarantee the provider makes — do not generalize it to `/io/user/*`.
- **Batch client-side.** There is no bulk endpoint; one call converts one input. If you need many,
  the MCP `transform_text` tool accepts a list in a single call, which the REST surface does not.

## Coverage gap worth knowing

The MCP tool `transform_text` collapses the forward styles into one `style` enum but documents no
style for the three `*2lower` reversals or for the `/convert/*` novelty formats. Those are
REST-only. See `mcp/api-dash-tool-crosswalk.yml`.
