---
name: vectranetworks-stream-events-to-siem
description: Continuously ingest Vectra entity-scoring, detection, and audit events into a SIEM using checkpoint cursors.
api: Vectra AI Platform API (RUX) v3.3
generated: '2026-07-21'
method: generated
source: openapi/vectranetworks-rux-v3.3-openapi.yml
operations:
- getEntityScoringEvents
- getDetectionEvents
- getAuditEvents
- getHealthData
---

# Stream events to a SIEM

The RUX `/events/*` endpoints are checkpoint-cursor streams built for
SIEM/SOAR ingestion — this is the pattern Vectra's own Splunk, Sentinel, and
Google SecOps integrations use.

## Steps
1. Start each stream from a stored checkpoint: `getEntityScoringEvents`
   (`GET /events/entity_scoring`), `getDetectionEvents`
   (`GET /events/detections`), and `getAuditEvents` (`GET /events/audits`)
   all take `from` (checkpoint) and `limit`; scoring events also accept
   `include_score_decreases` and `event_timestamp_gte/lte` windows.
2. Persist the checkpoint returned with each page and poll from it — do not
   re-window by wall clock, or you will duplicate or drop events.
3. Forward only what your SIEM needs; Vectra's value proposition is
   high-signal events, so avoid re-inflating ingestion volume downstream.
4. Monitor pipeline health with `getHealthData` (`GET /health`) — sensor and
   connectivity trouble shows up here before it shows up as silence in the
   event streams.

## Conventions
- OAuth2 client-credentials bearer token; refresh on 401.
- No documented 429/rate-limit headers — apply client-side politeness
  (fixed polling interval with backoff on errors).
