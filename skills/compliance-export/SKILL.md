---
name: compliance-export
description: Produce compliance evidence from Tuteliq. Export account data, pull audit logs and receipts, manage consent records, log and track breaches, and answer GDPR subject requests. Use when asked to prepare a KOSA, DSA, COPPA or GDPR evidence pack, respond to a data subject access or erasure request, or show an auditor what was detected and what was done about it.
---

# Producing compliance evidence

Two different asks get called "compliance export", and they need different
tools:

- **"Show the regulator our moderation worked"**: detection volumes, review
  decisions, audit receipts
- **"A user invoked their GDPR rights"**: export, rectify or erase that
  person's data

## Evidence that moderation worked

`get_incidents_overview` and `get_incident_trends` give the volumes: what was
detected, at what severity, over what period. `list_incidents` filtered by
`status` shows the disposition: how much was confirmed, dismissed, escalated.

That combination answers the question a DSA or KOSA reviewer actually asks:
*did you detect, and did you act?*

`get_audit_logs` covers account-level actions. `get_audit_receipt` returns a
signed receipt for a specific request, and that is the artefact to cite when an
auditor wants proof a particular assessment happened, rather than a screenshot.

## GDPR subject requests

- `export_account_data`: Article 20, portability. Returns the account's data as
  JSON.
- `delete_account_data`: Article 17, erasure.
- `rectify_data`: Article 16, correction.
- `record_consent`, `get_consent_status`, `withdraw_consent`: the consent
  ledger.

Set expectations correctly on erasure: **Tuteliq stores no user content in the
first place.** Messages, images, documents, audio and biometrics are processed
in memory and never persisted, raw or derived. What exists is the abstracted
detection record: category labels, severity, confidence, timestamps. An erasure
request removes those records; it cannot remove message text, because none was
ever kept.

That is usually a stronger answer than a completed deletion, and worth stating
plainly in the response.

## Breach handling

`log_breach`, `list_breaches`, `get_breach` and `update_breach_status` maintain
the breach register. GDPR Article 33 gives 72 hours to notify a supervisory
authority, so log first with what you know and update the record as the picture
firms up rather than waiting for certainty.

## Encryption posture

If the account uses BYOK, exported fields arrive as hybrid envelopes
(`_e2e_envelope_fields` names them) and must be decrypted client-side with the
customer's private key. `register_encryption_key`, `get_encryption_key` and
`revoke_encryption_key` manage those keys.

Tuteliq cannot decrypt BYOK fields. When preparing an export for a third party,
either decrypt client-side first or state clearly that the envelopes require the
customer's key; handing over ciphertext without explanation fails the request.

## Writing the pack

Lead with what was detected and what was done, in that order, with volumes and
disposition. Cite audit receipts for specific claims. Describe categories and
severities, never message content; quoting user text into a compliance document
undermines the zero-retention position the rest of the pack rests on.
