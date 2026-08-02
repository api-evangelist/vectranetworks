---
name: vectranetworks-triage-prioritized-entities
description: Triage the highest-urgency accounts and hosts on the Vectra AI Platform API (RUX) and route them to analysts.
api: Vectra AI Platform API (RUX) v3.3
generated: '2026-07-21'
method: generated
source: openapi/vectranetworks-rux-v3.3-openapi.yml
operations:
- getEntities
- getDetections
- getAssignments
- resolveAssignment
---

# Triage prioritized entities

Work the Vectra prioritization queue: pull the entities the AI has scored as
urgent, understand why, and resolve or route them.

## Auth
Obtain an OAuth2 access token via client credentials (`POST
https://{vectra_portal_url}/oauth2/token`). API clients are role-scoped; there
are no granular OAuth scopes. Send `Authorization: Bearer <token>`.

## Steps
1. `getEntities` with `is_prioritized=true&ordering=-last_detection_timestamp`
   — page with `page`/`page_size`; the response envelope is
   `count/next/previous/results`.
2. For each entity, `getDetections` filtered by the entity and
   `state=active&threat_gte=50` to see the driving detections.
3. `getAssignments` to check whether the entity is already owned by an
   analyst; unassigned high-urgency entities are your queue.
4. When an investigation concludes, `resolveAssignment`
   (`PATCH /assignments/{assignment_id}`) with the outcome. A 204 has no body.

## Conventions
- Errors are plain JSON `{detail}` bodies, not RFC 9457 (see
  errors/vectranetworks-problem-types.yml).
- No idempotency keys — do not blind-retry PATCH calls; re-read state first
  (see conventions/vectranetworks-conventions.yml).
