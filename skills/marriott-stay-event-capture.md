---
name: marriott-stay-event-capture
description: >-
  Publish a stay event — digital-key issuance, door-lock transaction, device registration or shared-key
  download — against a Marriott reservation confirmation number using the Marriott Data Collection API,
  for downstream consumption by PACD.
api: openapi/marriott-data-collection-api-openapi.json
operations:
  - DataCollectionEvent
method: generated
generated: '2026-07-28'
---

# Marriott Data Collection — publish a stay event

## Before you start

Marriott publishes no developer program and no documentation for this API. The specification
(`openapi/marriott-data-collection-api-openapi.json`, OpenAPI 3.0.3, version 1.0.0) is publicly
readable on SwaggerHub under the `marriott-api` owner. Its declared hosts are two SwaggerHub
auto-mocks plus `https://gatewaydsapdev1.marriott.com/` (dev) and
`https://gatewaydsaptst1.marriott.com/` (test) — **there is no production host published**. Both
Marriott gateways are live behind Akamai but return 404 on the root and grant nothing anonymously.
Access requires a Marriott partner or vendor relationship.

## The single operation

`POST /osd/v1/stays/{confirmation-number}/data-collection` — `operationId: DataCollectionEvent`

- **Path parameter (required):** `confirmation-number` — the Marriott reservation confirmation number.
  This is the resource key; the event is always scoped to one stay.
- **Header (required):** `correlation-id`. This is Marriott's one consistent house convention across
  the estate. Generate a fresh value per request and log it — Marriott does **not** echo it back on
  the response, so your value is the only trace handle you will have.
- **Security:** the document declares **no securitySchemes at all**. Authorization is implied by
  network placement behind the Marriott gateway. Do not assume an unauthenticated route exists on any
  real Marriott environment.
- **Body:** `DataCollectionRequest`.
- **Success:** `202 Accepted`.

## Building the request body

`DataCollectionRequest` composes seven optional sub-objects. Send only the ones your event class
populates:

| Field | Schema | Use it for |
|---|---|---|
| `guestStay` | `GuestStay` → `Reservation` + `Customer` + `SourceEnum` | Always. `Reservation` carries `confirmationNumber`, `propertyCode` (MARSHA), `arrivalDate`, `departureDate`; `Customer` carries `altCustomerId`. |
| `device` | `Device` → `DeviceDetail` + `Application` | Guest handset context: `guestDeviceId`, `deviceType`, `deviceOs` (`DeviceOSEnum`), `deviceOsVersion`, `appName`, `appVersion`. |
| `lock` | `Lock` → `Battery` | Door-lock telemetry: `lockId`, `lockType`, `firmwareVersion`, `lcuFirmwareVersion`, `system`, `statusFlags`, `accessAttemptInformation`, `rfu`, and battery `voltage`/`status`. |
| `transaction` | `Transaction` | Outcome of the attempt: `status`, `dateTime`, `duration`, `code`, `message`, `frameworkStatus`, `reason`. |
| `vendor` | `Vendor` | `sdkVersion` of the lock vendor SDK. |
| `deviceRegistration` | `DeviceRegistration` | `regToken`, `registrationType`. |
| `sharedKey` | `SharedKey` | `token`. |

Top-level fields: `transactionType`, `issuedDt`, `expirationDt`, `lockVendor` (`VendorTypeEnum`),
`downloadType` (`DownloadTypeEnum`).

Enumerations (`VendorTypeEnum`, `DownloadTypeEnum`, `DeviceOSEnum`, `SourceEnum`) are defined only
inside the specification — Marriott publishes no code list elsewhere. Read the allowed values from
the spec, never guess them.

## 202 means accepted, not done

The operation returns `202 Accepted` and Marriott publishes **no completion channel**: no callback,
no webhook, no polling location, no status resource, no AsyncAPI. Once you receive a 202 you cannot
learn from this API whether the event reached PACD. Do not build a confirmation loop against it;
treat the 202 as the end of your contract and reconcile out of band.

## Errors

This is the only Marriott document with a namespaced error-code convention. The envelope is
`{ "errors": [ { "code", "message", "timestamp", "source", "fields" } ] }` — note it is `errors`
(plural) with `code`/`message`, which is **not** the shape used by the Hotel Operations ARA API
(`errors` with `errorCode`/`errorMsg`) or the Loyalty Account Merge API (`error` with
`code`/`message`). Do not share a deserializer across Marriott APIs.

| Status | Meaning | Handling |
|---|---|---|
| `400` | Bad Request, e.g. code `OSD-DCS-3900` "Confirmation Number Missing" | Fix the request. Do not retry unchanged. |
| `404` | Request Not Found — the confirmation number is unknown | Do not retry. Verify the reservation exists. |
| `422` | Unprocessable Entity — body parsed but failed business validation | Do not retry unchanged. |
| `500` | Server Error | Retry with backoff. See the idempotency warning below. |
| `503` | Service Unavailable | Retry with backoff. |

`source` names the upstream system that actually produced the error (the spec example is
`hotelops-osd-data-collection`, and the field description notes it may be an Amadeus web-service
name) — log it, it is the fastest route to the right team.

## Idempotency and retries

Marriott declares **no idempotency key** on this operation. A retried `202` may publish a duplicate
stay event downstream. Because there is no read-back operation and no completion channel, a retry
after a timeout is genuinely ambiguous. Mitigations available to you:

- Reuse the **same `correlation-id`** across retries of the same logical event so a duplicate can at
  least be identified after the fact.
- Cap retries and record every attempt. Only `500` and `503` warrant a retry; `400`, `404` and `422`
  never do.

## Cross-cutting

- No rate limits are published — no `429`, no `Retry-After`, no `RateLimit-*` headers.
- No versioning or deprecation policy exists; the path carries `/osd/v1/` with no support window.
- Full detail: `conventions/marriott-conventions.yml`, `errors/marriott-problem-types.yml`,
  `data-model/marriott-data-model.yml`.
