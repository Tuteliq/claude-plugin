---
name: incident-review
description: Work a Tuteliq moderation queue. Triage incidents, inspect detail, action them, and produce incident reports. Use when asked to review flagged content, clear a moderation backlog, check what needs attention, escalate or dismiss incidents, or generate a report for a specific incident or time period.
---

# Working a moderation queue

Tuteliq records an incident whenever a detection warrants attention. A
moderation session is: size the queue, triage it, inspect what matters, action
it.

## 1. Size the queue

`get_incidents_overview` returns a snapshot over a time window: total,
`requires_review_count`, 24h/7d/30d counts, and breakdowns by category,
severity, source, status and platform. Start here; it tells you whether you
are dealing with ten items or ten thousand, and where they are concentrated.

`get_incident_trends` buckets counts by hour, day or week when the question is
"is this getting worse?" rather than "what is outstanding?".

## 2. Triage

`list_incidents` is the queue. Filter it rather than paging blindly:

- `status`: `new`, `reviewing`, `escalated`, `resolved`, `dismissed`
- `severity`: `low`, `medium`, `high`, `critical`
- `category`: e.g. `grooming`, `bullying`, `unsafe`, `romance_scam`
- `source`: `text`, `voice`, `image`, `video`
- `from` / `to`: ISO 8601 bounds on `created_at`
- `platform`, `external_id`, `customer_id`
- `limit` (max 100) and `cursor` for paging

Each row carries `recommended_actions`, so you can rank the queue without
fetching every incident. Work `immediate_intervention` first, then `block`,
then `flag_for_review`. Anything at `monitor` or `none` does not need a human.

`include_summary: true` decrypts the summary text per row, at an extra credit
per row. Use it when you genuinely need to read the queue; leave it off when
you are only counting or sorting.

## 3. Inspect

`get_incident` returns the full record: risk category and level, confidence,
`detected_patterns`, `recommended_actions`, source modality, `file_id`, review
state and the summary.

If the account has BYOK encryption enabled, encrypted fields come back as hybrid
envelopes and the response lists which ones in `_e2e_envelope_fields`. Those
must be decrypted client-side with the customer's private key; Tuteliq cannot
read them. Do not report an envelope as if it were missing data.

## 4. Action

`review_incident` records a moderator decision against one incident:

- `confirm`: the detection was right
- `dismiss`: false positive
- `escalate`: needs a higher tier or law enforcement
- `reclassify`: wrong category or severity; supply `new_risk_category` or
  `new_risk_level`
- `downgrade`: real but less severe than scored

Supply a reason code and, where your process requires it, a
`moderator_external_id` so the audit trail attributes the decision.

`batch_review_incidents` applies the same decision across many incidents. Use it
for a clear-cut sweep (a batch of confirmed false positives from one bad rule),
not as a way to clear a backlog without reading it.

Reviews are auditable. `get_audit_receipt` returns a signed receipt for a
request, which is what you cite in a DSA or KOSA evidence pack.

## 5. Report

`generate_report` produces an incident report suitable for professional review.
`get_action_plan` produces age-appropriate guidance for responding to a
situation rather than a record of it.

For a compliance evidence pack rather than a single report, see the
`compliance-export` skill.

## Rules of the road

**Branch on `recommended_action`, not on the presence of a detection.**
`detected` fires at any severity including monitor-only cases, so using it as
the trigger will bury a moderator in noise.

**Never quote user content into a report or ticket.** Incident summaries are
encrypted, rationales describe the kind of content rather than the content
itself, and reports should match. Describe tactics, severity, timing and
volume.

**Watch the credit weights when sweeping.** Text detection is 1-3 credits, an
image 7, a voice clip 21, a video 95. Re-running detection across a large
backlog is expensive; the incidents are already recorded, so read them rather
than re-detecting.
