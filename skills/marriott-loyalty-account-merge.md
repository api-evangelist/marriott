---
name: marriott-loyalty-account-merge
description: >-
  Merge or transfer one Marriott Bonvoy member profile into another via the Loyalty Account Merge API,
  including the caller-identity headers, the source/destination account identifier structure, and the
  irreversibility rules that apply because Marriott publishes no idempotency contract and no reversal
  operation.
api: openapi/marriott-loyalty-account-merge-api-openapi.json
operations:
  - POST /loyalty/v1/account-merge
method: generated
generated: '2026-07-28'
---

# Marriott Bonvoy — merge or transfer a member account

## Before you start

This is an **internal Marriott loyalty operation**, not a partner API. Marriott publishes no
developer program and no documentation for it. The specification
(`openapi/marriott-loyalty-account-merge-api-openapi.json`, OpenAPI 3.0.3, version 1.0.2) is publicly
readable on SwaggerHub under the `marriott-api` owner, and the **only server it declares is a
SwaggerHub auto-mock** (`https://virtserver.swaggerhub.com/marriott-api/lylt-v1-merge-profile/1.0.1`).
No callable Marriott host is published anywhere for this operation. The document's own title says it
routes to Salesforce CLM.

The operation carries **no `operationId`** — reference it as `POST /loyalty/v1/account-merge`.

## Read this before calling it

A Bonvoy account merge moves a member's identity, points balance, tier and stay history from one
account to another. Marriott publishes:

- **no reversal or unmerge operation** — there is nothing in any Marriott artifact that undoes this;
- **no idempotency key** on this or any other operation in the estate;
- **no read-back operation** to confirm the resulting state;
- **no `409 Conflict` response** to signal that a merge already happened.

That combination makes this the single highest-consequence operation in Marriott's publicly readable
estate. Treat it as a one-shot, human-approved action. An agent must not call it autonomously; see
`agentic-access/marriott-agentic-access.yml`.

## The request

`POST /loyalty/v1/account-merge`

**Headers**

| Header | Required | Notes |
|---|---|---|
| `client_id` | yes | Caller identity. Note the **underscore** — the Hotel Operations ARA API spells the same concept `client-id` with a hyphen. There is no published rule for which form a given gateway route expects. |
| `operator_id` | yes | The human operator on whose behalf the merge is executed. Marriott requires it and publishes nothing about its format or provenance. Record it in your own audit log. |
| `correlation-id` | no | Optional here, required on two other Marriott APIs. Send it anyway and reuse the value across any retry. |

**Body** — `MergeRequest`, a four-level nest:

```
MergeRequest
  accountMerge (AccountMerge)
    sourceAccount (SourceMergeAccount)
      memberAccountUniqueIdentifier (SourceMemberAccountUniqueIdentifier)
        memberAccountTypeCode          e.g. "CSID"
        memberAccountUniqueIdValue
        accountStatusType (AccountStatusType)
          accountStatusTypeCode        e.g. "23"
          newAccountStatusTypeDescription
    destinationAccount (DestinationMergeAccount)
      memberAccountUniqueIdentifier (DestinationMemberAccountUniqueIdentifier)
        memberAccountTypeCode
        memberAccountUniqueIdValue
    mergeReason
```

Rules:

- **Direction matters and is not recoverable.** `sourceAccount` is merged **into**
  `destinationAccount`. Confirm both identifiers with a human before sending. Getting them backwards
  cannot be undone through any published Marriott operation.
- **Member identity is a composite**, not a single number: `memberAccountTypeCode` +
  `memberAccountUniqueIdValue`. `"CSID"` is the only type code Marriott publishes anywhere; there is
  no enum, no reference list and no validation pattern in the schema.
- **`accountStatusType` only exists on the source side.** The destination identifier schema is
  otherwise structurally identical but omits it. That asymmetry is Marriott's, not a transcription
  error. `"23"` is the only status code that appears in the document.
- **`mergeReason` is a free-string field.** Populate it with a real, auditable reason — it is the only
  provenance the request carries.

## Responses

| Status | Body | Handling |
|---|---|---|
| `200` | success | The merge was accepted. There is no confirmation operation to verify against. |
| `400` | `ErrorResponse` — `{ "error": [ { "code", "message" } ] }` | Invalid request. Fix and resend; a rejected merge did not occur. |
| `500` | `ErrorResponse` | **Outcome unknown.** Do not blind-retry — see below. |

Note the envelope wrapper here is singular **`error`** holding an array, with `code`/`message` and no
required fields and no timestamp — a third distinct error shape within Marriott's own estate. See
`errors/marriott-problem-types.yml`.

## Retry policy

Because there is no idempotency key, no `409`, no read-back and no unmerge:

- On `400` — safe to correct and resend. Nothing happened.
- On `500` or a timeout — **do not retry automatically.** Escalate to a human and reconcile through
  Marriott's internal loyalty tooling before any second attempt. A duplicated merge cannot be
  distinguished from a first merge by anything this API exposes.
- Reuse the same `correlation-id` on any human-authorised second attempt so the two requests can be
  correlated after the fact.

## Cross-cutting

- No rate limits, no `429`, no `Retry-After` are published.
- No versioning or deprecation policy exists.
- Full detail: `conventions/marriott-conventions.yml`, `data-model/marriott-data-model.yml`,
  `lifecycle/marriott-lifecycle.yml`.
