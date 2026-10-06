---
description: "Mailbase status: workspace, replies needing attention, campaigns in flight, prospecting counts and deliverability warnings"
allowed-tools: mcp__plugin_mailbase_mailbase__current_workspace, mcp__plugin_mailbase_mailbase__list_replies, mcp__plugin_mailbase_mailbase__list_campaigns, mcp__plugin_mailbase_mailbase__prospecting_counts, mcp__plugin_mailbase_mailbase__get_sending_settings, mcp__plugin_mailbase_mailbase__list_domains, mcp__plugin_mailbase_mailbase__deliverability_report, mcp__plugin_mailbase_mailbase__list_follow_ups
---

Give me a short status of my Mailbase workspace. Read-only: do not change or
send anything. Follow the `get-started` skill for the tool names and rules.

1. `current_workspace`: name the workspace.
2. `list_replies` (hot and warm first): how many need attention, and the top
   three (contact, subject, intent).
3. `list_campaigns`: any campaign live or scheduled, with progress, and the
   latest finished one with its headline open, click and reply numbers.
4. `prospecting_counts`: pool by stage, drafts waiting for review, whether the
   sequence is running or paused.
5. `get_sending_settings`, `list_domains` and `deliverability_report`: whether
   sending is paused, quota left, any domain not verified or paused, any
   blocklist hit or rising bounce or complaint rate.

Finish with a "Do next" list of at most four items, most urgent first, each
mapped to the matching skill (`triage-replies`, `find-clients`,
`launch-campaign`, `setup-sending`). Extra focus from me, if any: $ARGUMENTS
