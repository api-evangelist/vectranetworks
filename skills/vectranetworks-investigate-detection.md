---
name: vectranetworks-investigate-detection
description: Investigate a single Vectra detection down to the packet level, tag it, and mark it fixed.
api: Vectra AI Platform API (RUX) v3.3
generated: '2026-07-21'
method: generated
source: openapi/vectranetworks-rux-v3.3-openapi.yml
operations:
- getDetections
- getDetectionById
- getPCAP
- getDetectionTags
- addDetectionTags
- markDetectionAsFixed
---

# Investigate a detection

Drill into one attacker-behavior detection, gather evidence, and close it out.

## Steps
1. `getDetections` with `category`, `certainty_gte`, and `threat_gte` filters
   to locate the detection of interest.
2. `getDetectionById` (`GET /detections/{detection_id}`) for full detail:
   type, grouped events, source entity, and scoring.
3. `getPCAP` (`GET /detections/{id}/pcap`) to download the packet capture for
   forensic review (binary response).
4. `getDetectionTags` then `addDetectionTags`
   (`POST /tagging/detection/{detection_id}`, returns 201) to record triage
   context — tags are the platform's annotation mechanism (no metadata object).
5. If benign or remediated, `markDetectionAsFixed`
   (`PATCH /detections/{detection_id}`, returns 204); to close a batch, use
   `markDetectionsAsFixed` (`PATCH /detections`).

## Conventions
- Bearer token from the OAuth2 client-credentials flow.
- List responses paginate with `page`/`page_size` in a
  `count/next/previous/results` envelope.
- PATCH calls are not idempotency-keyed; confirm current state before retrying.
