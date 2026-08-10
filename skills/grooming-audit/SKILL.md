---
name: grooming-audit
description: Audit a conversation for grooming risk using Tuteliq. Use when asked to review a chat log, DM thread, or message history for predatory behaviour toward a minor, to check whether an adult-to-child conversation is safe, or to assess grooming tactics across a conversation trajectory. Also covers bullying, self-harm and unsafe-content review of the same material.
---

# Auditing a conversation for grooming risk

Grooming is a trajectory, not a keyword. A single message rarely proves it; the
pattern across turns does. `detect_grooming` is built to assess a conversation,
so pass message sequences rather than isolated messages wherever possible.

## Running the audit

Call `detect_grooming` with the full message sequence. Supply `sender_age` per
message, and `childAge` / `participantAge` when known: age context materially
changes the assessment, and omitting it produces a weaker result.

For conversations longer than roughly 20-30 turns, chunk them and thread the
`continuation_token` from each response into the next call. The token carries
derived trajectory state without storing any message content server-side, so a
long thread is still assessed as one arc. Never restart a long conversation
without the token: you lose the escalation signal, which is the whole point.

## Reading the result

**Branch on `recommended_action`, never on `grooming_risk` alone.** It is a
stable five-value enum, ordered weakest to strongest:

| value | meaning |
|---|---|
| `none` | no signal |
| `monitor` | log it; no human needed |
| `flag_for_review` | route to a moderator |
| `block` | withhold or limit the content |
| `immediate_intervention` | crisis path: preserve evidence, follow your reporting procedure |

`flag_for_review` and above warrant human attention. A `grooming_risk` of
`"low"` is monitor-only and will over-alert if you treat it as actionable.

`action_detail` carries a human-readable expansion of the action. Show it to a
moderator; never branch on it, as the wording is not stable across releases.

`message_analysis` gives per-message risk and tactic flags; use it to show
*where* in the conversation the risk sits. In fast mode (`verdict_only: true`)
it is omitted, which is the right trade when screening a live stream: screen
everything fast, then re-run only what flags in standard mode for the detail.

The response's `flags` name the tactics the assessment identified. Report which
ones fired and where, rather than reciting scores. Treat the returned values as
the source of truth; do not hardcode a tactic list, and do not infer a result
the response did not state.

If a result looks like a false positive, inspect `message_analysis` before
escalating: per-message detail usually explains an aggregate that looks
surprising, and an assessment is not evidence on its own. Escalation is a human
decision.

## Choosing the right endpoint

- `detect_grooming`: adult-to-minor predatory patterns across a conversation
- `detect_bullying`: peer harassment, exclusion, intimidation
- `detect_unsafe`: self-harm, violence, hate, threats, sexual content
- `detect_coercive_control`: controlling dynamics between adults
- `analyse_multi`: run several detectors over the same content in one call

Grooming is scoped to adult-to-minor. If every participant is a known adult the
endpoint short-circuits and tells you so; use coercive-control or
social-engineering instead. For peer-to-peer conversations between minors,
`detect_bullying` or `detect_coercive_control` is the better fit.

## Reporting

Never quote the participants' message text back in a summary, report or ticket.
Tuteliq stores no user content, and its rationales describe *what kind* of
tactic occurred rather than what was said. Match that: report tactics, severity
and timing, not transcript.
