---
description: "Plan, draft, test and (after your yes) send an email campaign in Mailbase"
argument-hint: "<goal, e.g. 'announce our new reporting feature to active customers'>"
---

Follow the `launch-campaign` skill for this goal:

$ARGUMENTS

If the goal is empty, ask me what the campaign is for and who gets it.

Stop points: draft only until I say otherwise; run `campaign_preflight` and
show me the real recipient count; `send_test` only to an address I give you;
`launch_campaign` or `schedule_send` only after I confirm audience, count,
subject and time with a clear yes. Report any refusal from Mailbase honestly.
