---
name: marriott-tip-internet-portal-guest-flow
description: >-
  Operate Marriott's TIP Internet Portal partner API end to end — mint a bearer token from Basic
  credentials, generate the guest landing-page URL, validate a guest by room number and last name
  against the cloud PMS, and post the in-hotel internet purchase back to the PMS. For in-hotel
  internet providers holding a Marriott partner contract.
api: openapi/marriott-tip-internet-portal-api-openapi.json
operations:
  - POST /v3/ent-auth/sso/token
  - GET /partners/v1/internet-portal/landing-page
  - POST /partners/v1/internet-portal/guest-validation
  - POST /partners/v1/internet-portal/purchase-update
method: generated
generated: '2026-07-28'
---

# Marriott TIP Internet Portal — guest connect and purchase

## Before you start

This API is **not self-serve**. Marriott publishes no developer program, no signup and no
documentation for it. The specification this skill is grounded in
(`openapi/marriott-tip-internet-portal-api-openapi.json`, OpenAPI 3.0.4, version 1.0.2) is publicly
readable on SwaggerHub under the `marriott-api` owner, but the only host it names —
`https://gatewaydsapuat3.marriott.com` — is a Marriott UAT gateway that answers **HTTP 401** on the
documented partner route. You cannot obtain credentials without a Marriott partner relationship.
Do not attempt to work around that.

The four operations below carry **no `operationId`** in Marriott's document, so reference them by
method and path.

## Step 1 — mint a bearer token

`POST /v3/ent-auth/sso/token`

- Security: **HTTP Basic** (`BasicAuth`) — this is the only operation that takes Basic.
- Content type: `application/x-www-form-urlencoded`.
- Returns `TokenResponse` on 200.

Marriott's own `info.description` states the contract explicitly: enter Basic auth credentials, call
the token endpoint, then copy the bearer token into the Bearer authorization for every other call.

Note the token endpoint is declared as a **root-relative path**, not an absolute URL, so it resolves
against whichever Marriott gateway host your partner agreement assigns you — not necessarily the UAT
host in the document.

Failure modes:
- `400` — `Error` envelope `{status:"failed", code, message}`. Invalid request to the token endpoint.
- `401` — `AuthorizationError` envelope `{faultcode, faultstring}`, e.g.
  `"1012100 - Credentials invalid or missing."`. This is a SOAP-style fault surfacing on a JSON REST
  response; the numeric code is embedded in the message string, so string-parse it rather than
  expecting a code field.
- `500` — `Error` envelope.

## Step 2 — generate the guest landing page

`GET /partners/v1/internet-portal/landing-page`

- Security: **Bearer JWT** (`BearerAuth`) — the document-level default security.
- Required query: `propertyCode` (Marriott MARSHA code, five alpha characters), `L_URL`, `zone`.
- Optional query: `LR`.
- Required header: `locale`.
- Returns `PreconnectResponse` on 200, carrying the landing-page URL to redirect the guest to.

Failure modes:
- `400` / `500` — `PreconnectError`, which is the flat error envelope **plus** a `landingPageURI`
  field carrying a marriott.com fallback URL (the spec example is
  `https://www.marriott.com/hotel-search.mi?invalidProperty=true`). **Redirect the guest to
  `landingPageURI` when it is present** rather than showing a raw error — that is what the field is
  for.
- `401` — `AuthorizationError`. Re-mint the token at step 1.

## Step 3 — validate the guest against the cloud PMS

`POST /partners/v1/internet-portal/guest-validation`

- Security: Bearer JWT.
- Body: `UserValidationRequest` — room number and last name.
- Returns `GuestValidationResponse` on 200.

Failure modes:
- `404` — `Error`, "Guest details not found." Treat as a normal negative outcome, not a fault: the
  room/last-name pair did not match a resident guest. Do not retry.
- `400` — `Error`, invalid input.
- `401` — `AuthorizationError`.
- `500` — `Error`.

## Step 4 — post the internet purchase back to the PMS

`POST /partners/v1/internet-portal/purchase-update`

- Security: Bearer JWT.
- Body: `UserPurchaseRequest`.
- Returns `InternetPurchaseUpdate` on 200.

**This is a billable, non-idempotent write.** Marriott declares **no idempotency key** on this or any
other operation in its estate (see `conventions/marriott-conventions.yml`). A retried purchase-update
may post a duplicate charge to the guest folio. Rules:

- Do **not** blind-retry on timeout or on `5xx`. Treat the outcome as unknown.
- Do **not** retry on `404` ("Guest details not found.") — re-run step 3 instead.
- Only retry after re-establishing state through a channel outside this API, or after your partner
  agreement's documented reconciliation process. Marriott publishes no reconciliation or
  purchase-lookup operation.

## Cross-cutting rules

- **Error envelopes are not uniform even inside this one document.** Three shapes appear:
  `Error` (flat `status`/`code`/`message`), `PreconnectError` (the same plus `landingPageURI`) and
  `AuthorizationError` (`faultcode`/`faultstring`). Branch on the HTTP status first, then on the
  presence of `faultcode`. See `errors/marriott-problem-types.yml`.
- **No `correlation-id`.** Unlike four of the other five Marriott documents, this one declares no
  correlation-id header, so you have no request-tracing handle. Log your own request identifiers.
- **No rate limits are published.** No `429`, no `Retry-After`, no `RateLimit-*` headers are declared.
  Assume a limit exists and back off exponentially on any `5xx`.
- **No versioning or deprecation policy exists** (`lifecycle/marriott-lifecycle.yml`). The path
  carries `/v1/` for partner routes and `/v3/` for enterprise auth; Marriott publishes no support
  window for either.
- **Terms.** Marriott's Terms of Use prohibit automated access to Marriott systems without written
  permission. Use this skill only under an executed Marriott partner agreement.
