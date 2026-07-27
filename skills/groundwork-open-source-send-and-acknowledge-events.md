---
name: Send and acknowledge GroundWork events
description: Raise monitoring alert events and acknowledge/clear them through the TCG Controller API.
api: openapi/groundwork-open-source-tcg-openapi-original.yml
operations:
  - "POST /events"
  - "POST /events-ack"
  - "POST /events-unack"
  - "POST /downtime-set"
  - "POST /downtime-clear"
---

# Send and acknowledge GroundWork events

Raise alert/monitoring events into GroundWork, acknowledge them, and manage
downtime windows through the TCG Controller.

## Auth
Send `GWOS-APP-NAME` + `GWOS-API-TOKEN` headers on every request
(`authentication/groundwork-open-source-authentication.yml`). Bodies are
`application/json`.

## Steps
1. `POST /events` — submit alert/monitoring events to Foundation.
2. `POST /events-ack` — acknowledge events an operator is handling.
3. `POST /events-unack` — reverse an acknowledgement if handling is handed back.
4. `POST /downtime-set` — suppress alerting for planned maintenance windows.
5. `POST /downtime-clear` — remove downtime when maintenance completes.

## Rules
- All five operations return `401` on missing/expired auth headers; re-fetch the
  token and retry (`errors/groundwork-open-source-problem-types.yml`).
- Error bodies are plain strings, not RFC 9457 problem+json.
- Pair every `downtime-set` with a `downtime-clear` so alerting resumes.
