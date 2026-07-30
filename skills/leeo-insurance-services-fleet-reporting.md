---
name: LEEO fleet reporting export
description: >-
  Retrieve LEEO's generated fleet report for a given report time, handling the not-ready case, and
  fall back to assembling an equivalent export from the driver and aggregate operations.
api: openapi/leeo-insurance-services-fleet-openapi.yml
operations:
  - getReportLink
  - listDrivers
  - getWeeklyDriverAggregate
generated: '2026-07-19'
method: generated
source: https://docs.leeoinsurance.com/leeo-apis/
---

# LEEO fleet reporting export

Get LEEO's pre-generated fleet report, or build the equivalent yourself when it is not available.

## Before you start

- Base URL is `https://api.leeoinsurance.com/api/v1`, header
  `Authorization: APIKEY <fm_api_key>`, plus `Content-Type: application/json`.
- All dates are UTC.

## Steps

1. **Request the report link.** Call `getReportLink`
   (`GET /report?report_time=2024-07-01T12:00:00`). The `report_time` format is
   `YYYY-MM-DDTHH:MM:SS`.

2. **Handle the two documented outcomes.**
   - `200` returns `{"report_link": "https://…?#presigned_url"}` — a presigned URL to the generated
     report file.
   - `404` returns `{"message": "Report not ready yet"}` — the report has not finished generating.
     Treat this as a retry-later condition, not a hard failure.

3. **Download promptly.** The link is presigned and therefore time-limited. Fetch the file as soon
   as you receive the URL rather than storing the URL for later use.

4. **Handle the presigned URL carefully.** It grants direct access to fleet-wide driver data with no
   further authentication. Do not log it, embed it in a ticket, or pass it to a third party.

5. **Fall back to assembling the export.** If the report is not ready and you need data now, build
   an equivalent from the API: call `listDrivers` to get the roster, then
   `getWeeklyDriverAggregate` in batches of up to 20 `driver_id` values with a Monday `start_date`.
   That yields per-driver `fm_score`, `fm_grade`, trip counts, distance, and event summaries — plus
   `daily_summary_breakdown` for the day-by-day view.

## Rules

- `start_date` on the aggregate fallback must be within the last 6 months, must not be today, and
  must be a Monday for the weekly operation.
- Respect the 5000 requests per minute per-fleet limit; non-2xx responses count toward it.
- Retry a `404` on the report endpoint with backoff rather than escalating — it is the documented
  not-yet-generated signal, not an error in your request.
