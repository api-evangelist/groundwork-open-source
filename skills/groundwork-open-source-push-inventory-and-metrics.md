---
name: Push inventory and metrics to GroundWork
description: Register hosts/services then push metrics into GroundWork Monitor via the TCG Controller API.
api: openapi/groundwork-open-source-tcg-openapi-original.yml
operations:
  - "POST /inventory"
  - "POST /metrics"
  - "GET /status"
  - "GET /version"
---

# Push inventory and metrics to GroundWork Monitor

Feed a monitored host/service tree and its metrics into the GroundWork
Foundation server through the TCG Controller.

## Auth
Every protected request requires two headers (see
`authentication/groundwork-open-source-authentication.yml`):

- `GWOS-APP-NAME`: the connector's registered application name
- `GWOS-API-TOKEN`: the API token from the Foundation server

Content type is `application/json`.

## Steps
1. `GET /version` — confirm the connector build (config.BuildInfo) is reachable.
2. `GET /status` — check the connector is running before pushing.
3. `POST /inventory` — send the host/service inventory (upsert into Foundation).
4. `POST /metrics` — push the metric samples for the inventoried resources.

## Rules
- Inventory/metrics are last-writer-wins upserts; there is no idempotency key
  (see `conventions/groundwork-open-source-conventions.yml`). Do not assume
  retried pushes are deduplicated.
- On `401` re-fetch the API token and resend both auth headers
  (`errors/groundwork-open-source-problem-types.yml`).
- On `500` for `POST /metrics`, inspect the agent/NATS transport and retry after
  recovery.
