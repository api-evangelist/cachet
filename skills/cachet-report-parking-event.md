---
name: Report a car-sharing vehicle event for parking
description: Send a car-sharing vehicle event (trip start/end, vehicle added) with coordinates to Cachet so it manages parking for the fleet.
api: openapi/cachet-parking-openapi.yml
operations:
  - post-vehicle-events
---

# Report a car-sharing vehicle event (Cachet Parking)

Use the Cachet Parking API so a car-sharing operator can hand off all
parking-related handling to Cachet by streaming vehicle events.

## Prerequisites
- `x-api-key` and `x-api-username` headers issued by the Cachet IT team
  (contact kalle@cachet.me). Send both on every request.
- Base URL: `https://platform-api.cachet.me` (production) or
  `https://dev-platorm-api.cachet.me` (development).

## Steps

1. **Send the vehicle event — `post-vehicle-events`** (`POST /vehicle-events`).
   Send a `VehicleEvent`: `type` (`TRIP_START`, `TRIP_END`, or `VEHICLE_ADDED`),
   `licenseNr` (registration number), `time` (ISO 8601 with offset), and a `data`
   object with the required `latitude`/`longitude` and optional
   `isElectricVehicle` (set true to exclude EV-free zones). A `200` returns
   `{ success: true }`.

## Rules
- Send one event per real-world occurrence; Cachet handles the parking logic.
- On `401` re-check both auth headers; on `500 ValidationError` read `message`
  for the offending field. See errors/cachet-problem-types.yml.
- No idempotency key is supported — do not blindly retry.
