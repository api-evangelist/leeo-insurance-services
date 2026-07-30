---
name: LEEO driver safety review
description: >-
  Pull a fleet's driver roster from LEEO and produce a safety review for a chosen period, using
  daily and weekly aggregate scorecards to rank drivers and identify coaching focus areas.
api: openapi/leeo-insurance-services-fleet-openapi.yml
operations:
  - listDrivers
  - getDailyDriverAggregate
  - getWeeklyDriverAggregate
generated: '2026-07-19'
method: generated
source: https://docs.leeoinsurance.com/leeo-apis/
---

# LEEO driver safety review

Use the LEEO Fleet Telematics API to review how safely a fleet's drivers are driving.

## Before you start

- Base URL is `https://api.leeoinsurance.com/api/v1`.
- Every request needs the header `Authorization: APIKEY <fm_api_key>`. The key is fleet-scoped and
  comes from the LEEO dashboard Settings page. Also send `Content-Type: application/json`.
- All dates and timestamps are UTC.
- The fleet is limited to 5000 requests per minute, and failed calls count against it. Batch driver
  ids rather than looping one driver at a time.

## Steps

1. **Get the driver roster.** Call `listDrivers` (`GET /drivers?limit=100&page=1`). Page through
   using the response `next_page` until it is `null`. `limit` maxes out at 100. Every field except
   `driver_id` may be `null`, so never key your report on names — key on `driver_id` and fall back
   to `alias`, then `first_name`/`last_name`, then the id itself for display.

2. **Choose the window.** `start_date` must be within the last 6 months and cannot be today's date.
   For a weekly review, `start_date` must be a Monday.

3. **Pull the scorecards in batches of 20.** Both aggregate operations accept between 1 and 20
   repeated `driver_id` query parameters. Chunk the roster into groups of 20 and call:
   - `getDailyDriverAggregate` — `GET /driver-aggregate/daily?start_date=2024-05-20&driver_id=…&driver_id=…`
   - `getWeeklyDriverAggregate` — `GET /driver-aggregate/weekly?start_date=2024-05-20&driver_id=…&driver_id=…`

   The weekly response embeds `daily_summary_breakdown`, so prefer the weekly call when you need
   both the week total and the day-by-day trend — it saves a round trip.

4. **Rank and summarize.** Each driver's `driving_summary` carries `fm_score` (the LEEO safety
   score) and `fm_grade` (its band, e.g. `excellent`, `good`), plus `trip_count`,
   `distance_miles`, `duration_seconds`, `night_driving_seconds`, and `highway_miles`.

5. **Call out coaching focus areas.** `focus_area` flags which behaviors to coach:
   `is_phone_use`, `is_overspeeding`, `is_hard_brake`, and `is_acceleration` (the trip-level
   variant of this object uses `is_rapid_acceleration`). Back each flag with the matching counts
   from `events_summary`: `phone_use_count`, `rapid_acceleration_count`, `hard_brake_count`,
   `overspeeding_count`, `phone_use_time_seconds`, `overspeeding_time_seconds`.

## Rules

- Aggregation is done on UTC dates — do not shift dates into a local timezone.
- A driver with no trips in the window may return no summary; treat that as "no data", not a zero
  score.
- Do not request today's date; the API rejects it.
- On `429`, back off — the limit is per fleet per minute and every authenticated call counts,
  including non-2xx responses.
- Errors come back as `{"message": "..."}`; there is no RFC 9457 problem+json envelope.
