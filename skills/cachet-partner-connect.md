---
name: Generate a prefilled Cachet onboarding link
description: Send customer and vehicle data to Cachet Partners and receive a prefilled redirect link that streamlines customer onboarding.
api: openapi/cachet-partners-openapi.yml
operations:
  - post-connect
---

# Generate a prefilled Cachet onboarding link (Cachet Partners)

Use the Cachet Partners API to embed Cachet into a partner application: send the
agreed customer data and get back a redirect link with details prefilled.

## Prerequisites
- `x-api-key` and `x-api-username` headers issued by the Cachet IT team
  (contact sales@cachet.me). Send both on every request.
- Base URL: `https://partners.cachet.me` (production) or
  `https://dev-partners.cachet.me` (development).

## Steps

1. **Create the connect link — `post-connect`** (`POST /connect`).
   Send `ConnectInput`: a required `person` object (`firstName`, `lastName`,
   `email` required; `personCode` optional), an optional `vehicle` object
   (`licenseNr` required when present, plus `make`/`model`/`year`/`vinNumber`),
   and optional platform-specific params (e.g. `bolt.insuranceKey`). A `200`
   returns `{ redirectUrl }`.

2. **Redirect the customer** to `redirectUrl` (it carries a
   `connectId`/`connectSecret`); Cachet handles the rest of onboarding.

## Rules
- `person` is the only top-level required field; include `vehicle` when you have
  it to prefill more of the flow.
- On `401` re-check both auth headers; on `500 ValidationError` read `message`.
  See errors/cachet-problem-types.yml.
- Treat `connectSecret` in the returned URL as sensitive.
