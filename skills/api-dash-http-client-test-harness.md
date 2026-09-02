---
name: api-dash-http-client-test-harness
description: Exercise an HTTP client against the API Dash APIs' purpose-built /io endpoints — timeouts, multipart upload, HEAD, octet-stream, SSE streaming and full verb coverage.
api: openapi/api-dash-openapi.yml
base_url: https://api.apidash.dev
auth: none
operations:
  - delayed_request_io_delay_get
  - form_to_rotate_text_chars_io_form_post
  - create_file_io_filesize_post
  - analyze_img_file_io_img_post
  - head_request_io_head_head
  - octet_stream_request_io_octet_stream_post
  - create_user_io_user_create_post
  - update_user_io_user_update_put
  - patch_user_io_user__username__patch
  - delete_user_io_user__username__delete
  - sse_events_sse_events__count__get
---

# Testing an HTTP client against the API Dash APIs

The `/io/*` family exists for exactly this: a free, keyless, live server you can point a client at
to prove it handles the awkward parts of HTTP. No API key, no sign-up.

## Steps

1. **Timeout and cancellation.** `delayed_request_io_delay_get` — `GET /io/delay?wait=<seconds>`
   (default 5). Use it to prove your client's timeout fires, and that an in-flight request can be
   cancelled.
2. **Multipart form data.** `form_to_rotate_text_chars_io_form_post` — `POST /io/form`. One of only
   three operations in the whole API that declares a `requestBody`.
3. **File upload.** `create_file_io_filesize_post` — `POST /io/filesize` returns the size of what
   it received, so it doubles as a check that your client did not truncate or re-encode the body.
   `analyze_img_file_io_img_post` — `POST /io/img` — for image uploads.
4. **HEAD.** `head_request_io_head_head` — `HEAD /io/head`. The only HEAD operation in the contract.
5. **Raw binary.** `octet_stream_request_io_octet_stream_post` — `POST /io/octet-stream`.
6. **Verb coverage.** `create_user_io_user_create_post` (POST), `update_user_io_user_update_put`
   (PUT), `patch_user_io_user__username__patch` (PATCH),
   `delete_user_io_user__username__delete` (DELETE).
7. **Streaming.** `sse_events_sse_events__count__get` — `GET /sse/events/{count}` emits a bounded
   Server-Sent Events stream. Use it to prove your client detects a streaming content type and
   surfaces events incrementally rather than buffering to completion.

## Rules

- **The `/io/user/*` verbs are echoes, not storage.** Nothing you POST, PUT, PATCH or DELETE there
  is persisted, and there is no operation that reads back a user created through
  `/io/user/create`. The readable user collection (`/users`, `/users/{user_id}`, `/profile`) is a
  separate fixed sample dataset those verbs do not touch. Do not build a create-then-read
  assertion on top of them; it will fail for a reason that has nothing to do with your client.
- **Nothing here needs reversing.** Because no write durably lands, there is no cancel, refund or
  restore operation and no window to respect. `reversibility` is recorded `na` for this provider in
  `conventions/api-dash-conventions.yml` — an honest not-applicable, not an unmeasured gap.
- **422 is the only declared error status.** Expect the FastAPI validation envelope,
  `application/json`, not problem+json.
- **No rate-limit headers and no published limit.** If you are hammering `/io/delay` in a loop,
  throttle yourself — the provider gives you no signal to react to.
- **This is production.** There is no separate sandbox host: the API Dash APIs are free and keyless,
  so the surface you test against is the surface everyone uses. Be a good neighbour.

## Provider-published test targets

`doc/dev_guide/api_endpoints_for_testing.md` in `foss42/apidash` lists the endpoints API Dash uses
to test itself, including third-party targets for bad certificates, XML and media. Captured in
`sandbox/api-dash-sandbox.yml`.
