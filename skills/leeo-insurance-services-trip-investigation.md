---
name: LEEO trip investigation
description: >-
  Investigate a specific LEEO trip end to end — locate it from a driver's trip history, then pull
  its full trail path and driving events to reconstruct what happened, for claims triage or
  incident review.
api: openapi/leeo-insurance-services-fleet-openapi.yml
operations:
  - listDrivers
  - listDriverTrips
  - getTripByTripId
  - getTripByTrackingId
generated: '2026-07-19'
method: generated
source: https://docs.leeoinsurance.com/leeo-apis/
---

# LEEO trip investigation

Reconstruct a single trip from LEEO telematics — the route driven and the driving events detected
along it.

## Before you start

- Base URL is `https://api.leeoinsurance.com/api/v1`, header
  `Authorization: APIKEY <fm_api_key>`, plus `Content-Type: application/json`.
- Trips are not queryable until roughly **4 hours after the trip ends**. If a recent trip is
  missing, that delay is the first thing to check.
- All timestamps are UTC. Trip and event times are **epoch milliseconds**; dates are `YYYY-MM-DD`.

## Steps

1. **Identify the driver.** If you only have a name, call `listDrivers`
   (`GET /drivers?limit=100&page=1`) and match on `alias` or `first_name`/`last_name` — remembering
   any of those may be `null`. For SDK integrations the `driver_id` is whatever id was supplied at
   SDK initiation, so you may already have it.

2. **Find the trip.** Call `listDriverTrips`
   (`GET /driver/{driver_id}/trips?start_date=2024-05-01&limit=100&page=1`). `start_date` is
   required and must be within the last 6 months. Page with `next_page` until it is `null`. Narrow
   candidates using `trip_start_time` / `trip_end_time`, `distance_miles`, and the start/end
   latitude and longitude. This list deliberately omits the path and events.

3. **Pull the detail.** Use whichever identifier you hold:
   - `getTripByTripId` — `GET /trip/trip_id/{trip_id}?page=1`, where `trip_id` came from step 2.
   - `getTripByTrackingId` — `GET /trip/tracking_id/{tracking_id}?page=1`, where `tracking_id` is
     the value your own app set when it started the trip through the LEEO SDK.

   Both are paginated at a **fixed 5 records per page**. Follow `next_page` until `null` to collect
   the whole result.

4. **Reconstruct the route.** `trail_path` is an ordered list of points, each with `timestamp`,
   `latitude`, `longitude`, and `course` — the heading in degrees relative to North.

5. **Analyze the events.** `events` lists each detected behavior with start/end timestamps and
   start/end coordinates:
   - `event_type` 1 → `RAPID_ACCELERATION` (no extra data)
   - `event_type` 2 → `HARD_BRAKE` (no extra data)
   - `event_type` 7 → `PHONE_USE` (no extra data)
   - `event_type` 9 → `OVERSPEEDING`, whose `event_data` carries `user_speed_mph`,
     `speed_limit_mph`, and `max_user_speed_mph`

   Correlate each event's timestamps against `trail_path` to place it on the route.

6. **Check the insurance period.** `insurance_period` is `1`, `2`, or `3` per the California Public
   Utilities Commission definitions — 1: logged in and available but unmatched; 2: match accepted,
   vehicle not yet occupied; 3: pick-up complete, vehicle occupied. It is `null` for fleets that do
   not require insurance periods. For a claim, this determines which coverage applies, so read it
   before drawing conclusions.

## Rules

- Never infer a trip's events from the trip list — `event_summary` there is only counts. Always
  fetch the detail operation for the actual `events` array.
- Do not treat `insurance_period: null` as "no coverage"; it means the fleet does not use periods.
- Errors are `{"message": "..."}`. The fleet-wide rate limit is 5000 requests per minute and counts
  non-2xx responses, so honor backoff when paging large trail paths.
