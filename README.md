# LEEO Insurance Services

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

LEEO Insurance Services is a San Francisco-based managing general agent (MGA) for commercial auto
insurance, founded in 2017 and known as **Fairmatic** until its December 2025 rebrand. LEEO
underwrites and prices fleet policies using telematics collected from drivers' phones through its
mobile SDK, applying machine learning across underwriting, pricing, and claims, and rewarding safer
driving with renewal credits and cashback. It writes fleet classes including non-emergency medical
transport, light business auto, and last-mile delivery, selling through brokers.

Backed by: Battery Ventures, Foundation Capital, Aquiline Technology Growth ($91M raised)

## APIs

**LEEO Fleet Telematics API** — `https://api.leeoinsurance.com/api/v1`
([docs](https://docs.leeoinsurance.com/leeo-apis/))

A read-only REST API over a fleet's telematics data. Authenticated with a fleet-scoped API key sent
as `Authorization: APIKEY <fm_api_key>`, rate limited to 5000 requests per minute per fleet. All
timestamps are UTC; trips become queryable roughly four hours after they complete.

| Operation | Endpoint |
|---|---|
| `listDrivers` | `GET /drivers` |
| `listDriverTrips` | `GET /driver/{driver_id}/trips` |
| `getTripByTripId` | `GET /trip/trip_id/{trip_id}` |
| `getTripByTrackingId` | `GET /trip/tracking_id/{tracking_id}` |
| `getDailyDriverAggregate` | `GET /driver-aggregate/daily` |
| `getWeeklyDriverAggregate` | `GET /driver-aggregate/weekly` |
| `getReportLink` | `GET /report` |

## SDKs

First-party mobile telematics SDKs are published under both brands — the mature Fairmatic v3.x line
and a newer Leeo-branded line:

- npm — `react-native-fairmatic-sdk`, `leeo-react-native-sdk`, `leeo-ios-sdk`
- Maven Central — `com.fairmatic:sdk`
- NuGet — `FairmaticSDK.Android`, `FairmaticSDK.iOS` (MAUI bindings)
- CocoaPods — `LeeoSDK`; `FairmaticSDK` via git podspec
- Swift Package Manager — `fairmatic-sdk-spm`

## Artifacts

- `openapi/` — OpenAPI 3.1 spec for the Fleet Telematics API
- `overlays/` — API Evangelist enhancements over the spec
- `authentication/` — auth profile
- `conventions/` — pagination, rate limiting, error envelope, time semantics
- `errors/` — error catalog
- `lifecycle/` — versioning, deprecation posture, rebrand history
- `changelog/` — SDK release inventory
- `conformance/` — standards conformance
- `data-model/` — entity-relationship graph
- `packages/` — SDK and package inventory
- `mcp/` — candidate MCP tool surface
- `skills/` — packaged agent skills
- `llms/` — llms.txt
- `agentic-access/` — recommended agentic execution contracts
- `security/` — domain security probe
- `well-known/` — well-known probe results (none published)

## Notes

- LEEO publishes **no** status page, deprecation policy, vulnerability disclosure program, trust
  center, webhooks or event surface, CLI, sandbox, or public Postman collection. Those artifacts are
  intentionally absent rather than fabricated.
- The API surface is entirely read-only (GET), so there is no idempotency contract.
