# Tuteliq Plugin: Child Safety, Content Moderation and Fraud Detection MCP Server

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![MCP Server](https://img.shields.io/badge/MCP-api.tuteliq.ai%2Fmcp-6f42c1)](https://docs.tuteliq.ai/mcp)
[![Docs](https://img.shields.io/badge/docs-docs.tuteliq.ai-informational)](https://docs.tuteliq.ai)

Give your AI agent a trust and safety toolkit. This plugin connects Claude Code
and Cowork to the [Tuteliq](https://tuteliq.ai) API through a hosted
[Model Context Protocol](https://modelcontextprotocol.io) server, adding around
80 tools for **grooming detection, bullying and self-harm detection, CSAM and
nudity screening, fraud and scam detection, deepfake and synthetic media
detection, age verification, moderation queue triage and regulatory compliance
evidence**.

Sign in with OAuth. There is no API key to copy into a config file.

```
/plugin marketplace add Tuteliq/claude-plugin
/plugin install tuteliq
```

## Why use it

- **Multimodal content moderation.** Analyse text, voice, image, video and
  documents through one interface.
- **Conversation-level grooming detection.** Risk is assessed across a
  conversation trajectory, not keyword by keyword, with continuation tokens that
  carry state across long threads.
- **Moderation queue in your terminal.** Size a backlog, triage it, action
  incidents and generate reports without leaving your agent.
- **Compliance evidence on demand.** Audit logs, signed audit receipts, consent
  ledger, breach register and GDPR subject request handling for KOSA, DSA,
  COPPA, the EU AI Act and GDPR.
- **Zero content retention.** Messages, images, audio, video and documents are
  processed in memory and never persisted, raw or derived. What remains is the
  abstracted detection record.
- **Bring your own key encryption.** Sensitive fields can be returned as hybrid
  envelopes that only the customer's private key can open.

## Installation

Add the marketplace and install the plugin:

```
/plugin marketplace add Tuteliq/claude-plugin
/plugin install tuteliq
```

On first use your agent opens a browser for OAuth sign in. Approve access and
the connection completes. Tokens refresh automatically.

To connect a different MCP client directly, point it at the hosted endpoint:

```json
{
  "mcpServers": {
    "tuteliq": {
      "type": "http",
      "url": "https://api.tuteliq.ai/mcp"
    }
  }
}
```

A [Tuteliq account](https://tuteliq.ai) is required. The free tier is enough to
evaluate the tools.

## What is included

### Detection tools

| Area | Tools |
|---|---|
| Child safety | `detect_grooming`, `detect_bullying`, `detect_unsafe`, `detect_distress_signals`, `detect_vulnerability_exploitation` |
| Wellbeing | `analyze_emotions`, `detect_emotional_distress`, `get_action_plan` |
| Fraud and scams | `detect_romance_scam`, `detect_app_fraud`, `detect_social_engineering`, `detect_mule_recruitment`, `detect_gambling_harm` |
| Abuse and extremism | `detect_coercive_control`, `detect_radicalisation`, `detect_tfgbv` |
| Synthetic media | `detect_synthetic_text`, `detect_synthetic_image`, `detect_synthetic_audio`, `detect_synthetic_video` |
| Multimodal | `analyze`, `analyze_image`, `analyze_voice`, `analyze_video`, `analyze_document`, `analyse_multi` |
| Age and identity | `create_verification_session`, `get_verification_session`, `cancel_verification_session` |

### Moderation and compliance tools

| Area | Tools |
|---|---|
| Moderation queue | `list_incidents`, `get_incident`, `get_incidents_overview`, `get_incident_trends`, `review_incident`, `batch_review_incidents`, `generate_report` |
| Compliance | `get_audit_logs`, `get_audit_receipt`, `record_consent`, `get_consent_status`, `withdraw_consent`, `export_account_data`, `rectify_data`, `delete_account_data` |
| Breach register | `log_breach`, `list_breaches`, `get_breach`, `update_breach_status` |
| Webhooks and keys | `create_webhook`, `list_webhooks`, `update_webhook`, `delete_webhook`, `test_webhook`, `regenerate_webhook_secret`, `register_encryption_key`, `get_encryption_key`, `revoke_encryption_key` |
| Usage | `get_usage_history`, `get_usage_monthly`, `get_usage_by_tool`, `get_pricing`, `get_pricing_details` |

### Skills

Skills teach the agent how to run a whole workflow, not just call one tool.

- **`grooming-audit`**: audit a chat log or DM thread for grooming risk, chunk
  long conversations correctly, and report findings without quoting user
  content.
- **`incident-review`**: work a moderation queue end to end, from sizing the
  backlog through triage, inspection, action and reporting.
- **`compliance-export`**: produce a KOSA, DSA, COPPA or GDPR evidence pack, and
  answer data subject access, rectification and erasure requests.

## Example prompts

```
Review this chat export for grooming risk and tell me what needs a moderator.
```

```
How many critical incidents came in this week, and what are they?
```

```
Work the moderation queue: triage anything at flag_for_review or above.
```

```
Build a DSA evidence pack for last quarter showing what we detected and what we did.
```

```
A user has requested erasure under GDPR Article 17. Handle it and confirm.
```

## Requirements

- Claude Code or Cowork with plugin support
- A [Tuteliq account](https://tuteliq.ai)
- Network access to `https://api.tuteliq.ai`

## Privacy and data handling

Tuteliq is built for regulated child safety and moderation work:

- No user content is persisted, raw or derived. That includes messages, images,
  documents, audio, video and biometrics.
- Detection records hold category labels, severity, confidence and timestamps,
  never the content that produced them.
- Audit receipts are signed, so a specific assessment can be evidenced to a
  regulator without retaining the underlying material.
- Optional bring your own key encryption keeps sensitive fields unreadable to
  Tuteliq.

See the [Trust Center](https://docs.tuteliq.ai) for the current retention
schedule and subprocessor list.

## Documentation

- [MCP server overview](https://docs.tuteliq.ai/mcp)
- [Authentication and OAuth](https://docs.tuteliq.ai/mcp-server/authentication)
- [Tool reference](https://docs.tuteliq.ai/mcp-server/tools)
- [Moderation workflows](https://docs.tuteliq.ai/mcp-server/moderation)
- [Grooming detection](https://docs.tuteliq.ai/grooming-detection)
- [Full API documentation](https://docs.tuteliq.ai)

## Support

- Issues with this plugin: [open an issue](https://github.com/Tuteliq/claude-plugin/issues)
- API and account questions: [support@tuteliq.ai](mailto:support@tuteliq.ai)
- Security disclosures: [security@tuteliq.ai](mailto:security@tuteliq.ai)

## License

MIT. See [LICENSE](LICENSE).

Tuteliq is a product of Tuteliq AB. Use of the API is governed by the
[Tuteliq terms of service](https://tuteliq.ai).
