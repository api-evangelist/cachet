---
name: Onboard a gig worker and record a task event
description: Register a gig-worker on Cachet Verify, then send a gig-task event tying activity to that worker for usage-based insurance coverage.
api: openapi/cachet-verify-openapi.yml
operations:
  - post-create-user
  - post-create-gig-event
---

# Onboard a gig worker and record a task event (Cachet Verify)

Use the Cachet Platform (Verify) API so a gig-work platform can register a worker
and then report the tasks that drive usage-based insurance coverage.

## Prerequisites
- `x-api-key` and `x-api-username` headers, both issued by the Cachet IT team
  (contact devs@cachet.me). Send both on every request.
- Base URL: `https://platform-api.cachet.me` (production) or
  `https://dev-platorm-api.cachet.me` (development).

## Steps

1. **Register the worker — `post-create-user`** (`POST /create-user`).
   Send `CreateUserInput`: `firstName`, `lastName`, `extUserId` (the worker's id
   on your platform), `email`, and `countryCode` are required; `personCode` and a
   free-form `data` object are optional. A `200` returns `{ status, message }`.

2. **Report task activity — `post-create-gig-event`** (`POST /create-gig-event`).
   Send `CreateGigEventInput`: `type` (`TASK_START`, `TASK_END`, or
   `COMPLETED_TASK`), `extUserId` (must match the registered worker), `time`
   (ISO 8601), and a `data` object with `taskId` (and optional `location`).
   Fire this on both the start and the end of a job.

## Rules
- Always send create-user for a worker before sending gig events for them.
- `extUserId` is the join key between the two calls — keep it stable per worker.
- On `401` re-check both auth headers; on `500 ValidationError` inspect the
  `message` field for the missing/invalid property. See
  errors/cachet-problem-types.yml and conventions/cachet-conventions.yml.
- No idempotency key is supported — do not blindly retry writes.
