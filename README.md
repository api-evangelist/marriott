# Marriott International (marriott)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Marriott International is the world's largest hotel group by room count, headquartered in Bethesda, Maryland, United States, operating and franchising roughly thirty brands from The Ritz-Carlton, St. Regis and W through Marriott Hotels, Sheraton, Westin and Courtyard down to Fairfield, Moxy and StudioRes, plus the Homes & Villas by Marriott Bonvoy home-rental marketplace and the Marriott Bonvoy loyalty program. It sits on the supply side of the travel distribution chain: it publishes rates and availability into every major global distribution system — Marriott's own travel-agent site states it "participates in the following GDS: Amadeus, Sabre, Travelport (Apollo/Galileo, and Worldspan)" — sells through the OTAs, connects short-term-rental supply through a channel-connectivity partner program, and spends heavily to pull demand back to direct booking on Marriott.com and the Bonvoy app. Its API posture is closed. A Broadcom Layer7 developer portal exists at devportalprod.marriott.com and returns HTTP 200, but its anonymous API catalog is literally empty and the portal's own home content returns HTTP 401 — there is no self-serve signup, no public API reference, no published rates/availability/booking API, no sandbox, no SDK, no changelog and no exit path. The only Marriott OpenAPI documents that can be read without a contract are eight internal and partner-facing specifications left publicly readable on SwaggerHub under the "marriott-api" owner; six are mirrored here verbatim as evidence. Everything a developer would actually want is behind a partner relationship, a travel-agent registration, or a GDS or channel-manager contract.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/marriott/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/marriott/refs/heads/main/apis.yml)

## Tags

- Travel
- United States
- Hospitality
- Hotels
- Booking
- Distribution
- Loyalty
- Short Term Rental
- Corporate Travel

## Timestamps

- **Created:** 2026-07-28
- **Modified:** 2026-07-28

## APIs

### Marriott TIP Internet Portal API

Partner-facing API used by Marriott's in-hotel internet (TIP) provider integration to generate a guest landing-page URL, validate a guest's room number and last name against the cloud PMS, and post an internet purchase back to the PMS. Bearer and Basic security schemes are declared and an SSO token operation is included. This is not part of any public Marriott developer program; the OpenAPI document is readable on SwaggerHub under the "marriott-api" owner and the only server it names is a Marriott UAT gateway host that is not open to the public.

- **Human URL:** [https://api.swaggerhub.com/apis/marriott-api/tip-internet-portal-api-spec/1.0.2](https://api.swaggerhub.com/apis/marriott-api/tip-internet-portal-api-spec/1.0.2)

#### Tags

- Partners
- Property Management System
- Guest Services

#### Properties

- [OpenAPI](openapi/marriott-tip-internet-portal-api-openapi.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [API Reference](https://api.swaggerhub.com/apis/marriott-api/tip-internet-portal-api-spec/1.0.2)

### Marriott Loyalty Account Merge API

Internal Marriott Bonvoy loyalty operation that merges or transfers one member profile into another, keyed on a member account type code and member account unique identifier. Documented only as an OpenAPI document publicly readable on SwaggerHub under the "marriott-api" owner; the only server listed is a SwaggerHub auto-mock, so no callable Marriott host is published.

- **Human URL:** [https://api.swaggerhub.com/apis/marriott-api/account-merge/1.0.2](https://api.swaggerhub.com/apis/marriott-api/account-merge/1.0.2)

#### Tags

- Loyalty
- Member Profile

#### Properties

- [OpenAPI](openapi/marriott-loyalty-account-merge-api-openapi.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [API Reference](https://api.swaggerhub.com/apis/marriott-api/account-merge/1.0.2)

### Marriott Data Collection API

Internal Marriott stay-event API that captures and publishes additional event data for a reservation confirmation number, described by its own OpenAPI as feeding a downstream consumer called PACD and associated with digital-key events. Servers named are SwaggerHub auto-mocks plus Marriott dev and test gateway hosts; there is no production or public endpoint.

- **Human URL:** [https://api.swaggerhub.com/apis/marriott-api/Data_Collection_API/1.0.0](https://api.swaggerhub.com/apis/marriott-api/Data_Collection_API/1.0.0)

#### Tags

- Stays
- Events
- Digital Key

#### Properties

- [OpenAPI](openapi/marriott-data-collection-api-openapi.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [API Reference](https://api.swaggerhub.com/apis/marriott-api/Data_Collection_API/1.0.0)

### Marriott Commerce Payment Processor API

Internal Marriott commerce API that submits a payment to a payment processor over an XML FreedomPay Freeway service operation. Its OpenAPI info block names the "Marriott API Team" with contact URL https://api.marriott.com/ and email apidevteam@marriott.com and a license named "Marriott Consumer License" — the only place Marriott's API-team identity is publicly asserted. Declares Basic, Bearer and OAuth2 authorization-code security. No public server is listed.

- **Human URL:** [https://api.swaggerhub.com/apis/marriott-api/commerce-payment-processor/1.0.0](https://api.swaggerhub.com/apis/marriott-api/commerce-payment-processor/1.0.0)

#### Tags

- Payments
- Commerce

#### Properties

- [OpenAPI](openapi/marriott-commerce-payment-processor-api-openapi.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [API Reference](https://api.swaggerhub.com/apis/marriott-api/commerce-payment-processor/1.0.0)

### Marriott Finance Status Notifier API

Internal Marriott finance application API that receives processing status for files generated by Marriott's finance adapters, with status, data, config-watcher and Spring Boot actuator logger operations, secured with an OAuth2 client-credentials flow. One of only two Marriott SwaggerHub documents marked published rather than draft. No Marriott host is listed as a server.

- **Human URL:** [https://api.swaggerhub.com/apis/marriott-api/finance-all-statusnotifier/1.0.2](https://api.swaggerhub.com/apis/marriott-api/finance-all-statusnotifier/1.0.2)

#### Tags

- Finance
- Operations

#### Properties

- [OpenAPI](openapi/marriott-finance-status-notifier-api-openapi.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [API Reference](https://api.swaggerhub.com/apis/marriott-api/finance-all-statusnotifier/1.0.2)

### Marriott Hotel Operations ARA Preview Submit API

Internal Marriott hotel-operations API that submits a preview request for ARA automated room assignment. The OpenAPI document declares no servers at all, which is itself the finding — the operation is real but no host, environment or access route is published anywhere.

- **Human URL:** [https://api.swaggerhub.com/apis/marriott-api/hotel-operations-ara-preview-submit-api/1.0.0](https://api.swaggerhub.com/apis/marriott-api/hotel-operations-ara-preview-submit-api/1.0.0)

#### Tags

- Hotel Operations
- Room Assignment

#### Properties

- [OpenAPI](openapi/marriott-hotel-operations-ara-api-openapi.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [API Reference](https://api.swaggerhub.com/apis/marriott-api/hotel-operations-ara-preview-submit-api/1.0.0)

## Common Properties

- [Website](https://www.marriott.com/)
- [Developer Portal](https://devportalprod.marriott.com/)
- [Travel Agent Portal](https://www.travelagents.marriott.com/)
- [GDS Chain Codes](https://www.travelagents.marriott.com/travelagents/GDSResInfo.mi)
- [Connectivity Partners](https://homes-and-villas.marriott.com/en/connectivity-partners)
- [Terms of Service](https://www.marriott.com/en-us/about/terms-of-use.mi)
- [Loyalty Program Terms](https://www.marriott.com/loyalty/terms/default.mi)
- [Privacy Policy](https://www.marriott.com/about/us-consumer.mi)
- [Vulnerability Disclosure](https://www.marriott.com/.well-known/security.txt)
- [Bug Bounty](https://hackerone.com/marriott)
- [LinkedIn](https://www.linkedin.com/company/marriott-international)

## Switching Cost

The central question for the travel and aviation sector is what it would cost a customer to leave. For Marriott the answer is asymmetric, and it is recorded in full in [review.yml](review.yml).

- **Interface shape:** `proprietary-undocumented`. No OpenTravel/OTA, HTNG, HEDNA or GIATA conformance is referenced anywhere on any Marriott property, and none appears in any harvested specification. The harvested documents are plain OpenAPI 3.0.x REST with Marriott-specific path namespaces, headers and enumerations.
- **Second source:** `few-alternatives`. Marriott inventory is reachable through Sabre, Amadeus, Travelport, Expedia Rapid, Booking.com Demand API and the bedbanks. Marriott Bonvoy member rates, member profile, points and digital key have no second source at all.
- **Exit path:** `export-on-request`. No bulk export operation exists. The only published exit is the consumer privacy right — "a copy of the Personal Data you provided to us in a portable format."
- **Identifier portability:** shared GDS two-letter chain codes at the distribution edge (master and per-brand, e.g. Marriott Hotels MC, Ritz-Carlton RZ, Sheraton SI, Westin WI, W Hotels WH); Marriott-proprietary MARSHA property codes, Bonvoy member numbers, member account type codes and confirmation numbers everywhere behind it.
- **Contractual lock-in:** the Terms of Use prohibit using "robots, spiders, or other automatic or high-volume methods or devices" to "access, search, retrieve, index, monitor, scrape, copy or collect data" without written permission. The Bonvoy program rules forfeit unredeemed points on account cancellation or program termination, with no cash value and no transferability.
- **Access gate:** `partner-only`. Nothing signable is published — no developer agreement, API licence or partner contract is downloadable from any Marriott host.
- **Distribution model:** `gds-intermediated`, with a direct-booking counterweight and a channel-connectivity program for home-rental supply. NDC does not apply — Marriott is a hotel group, not an airline.

## Note on the harvested specifications

The six OpenAPI documents in `openapi/` were fetched verbatim from SwaggerHub's public registry under the owner `marriott-api` on 2026-07-28, each returning HTTP 200 anonymously. They are Marriott's internal and partner-facing estate, not a developer offering, and they are recorded here as evidence of Marriott's real API surface. Two further documents in the same account describe credential distribution against Marriott dev and test hosts; their existence and provenance are documented in `review.yml` but they are deliberately not mirrored.
