# LEEO Insurance Services

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
