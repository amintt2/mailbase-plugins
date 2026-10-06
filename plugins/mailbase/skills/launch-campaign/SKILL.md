---
name: launch-campaign
description: "Use when someone wants to write, test and send an email campaign in Mailbase, run an A/B test, or read campaign results. Covers audience, template, draft with variants, preflight, a test to the user, launch or schedule after an explicit yes, send progress and the A/B winner rollout."
---

# Launch a campaign

A campaign is a draft until the user says to send it. Nothing in steps 1 to 5
emails anyone except the user's own test address. Read the confirmation rules
in `get-started` first.

## 1. Understand the goal

Ask (only what you cannot infer): who it goes to, what the one action is
(reply, book, buy), the offer, tone, and whether to A/B test the subject or the
body. Check `list_campaigns` so you do not duplicate a recent campaign.

## 2. Audience

- `list_audiences` and pick one. Offer `get_audience` for a sample.
- No fit: `preview_audience_rules` to count what a segment would match, then
  `create_audience` (kind rule) or a static one with imported contacts
  (`create_audience` with contacts, or `add_audience_contacts` later).
- Addresses erased under GDPR are skipped on import; report the skipped count.
- Only email people the user has a legitimate reason to contact. Imports are
  not a licence to mail bought lists.

## 3. Content

- `list_templates` / `get_template` for a starting point, or `create_template`
  with an MJML body. `duplicate_template` before editing a built-in one;
  `update_template` edits a workspace template (every edit is a new version,
  see `list_template_versions` and `restore_template_version`).
- Write short, specific copy with a clear single call to action. Merge tags
  look like `{{first_name}}`; always give a fallback for people who lack the
  field (see `campaign_preflight`).
- `check_template` runs the deliverability checklist (spam-prone wording,
  missing unsubscribe, subject length); fix what it flags.
  `check_template_links` fetches the links, tell the user it will.
  `render_template` gives an HTML preview.

## 4. Create the draft (still sends nothing)

`create_campaign` with a name and one variant per subject/body idea. For an A/B
test use two variants and set the test with `update_campaign` (metric, pilot
share, confidence, winner strategy). `update_campaign` also sets the audience
and merge defaults, and only works while the campaign is a draft.
`duplicate_campaign` copies one to edit; `archive_campaign` and
`unarchive_campaign` hide or bring back old ones (reversible).

Show the user a table: name, audience and size, each variant's subject and
first lines, sender, and what the A/B test will measure.

## 5. Preflight and test

1. `campaign_preflight`: which merge tags would be empty, and how many people
   would really receive it (deliverable versus suppressed). Report the number.
2. `get_sending_settings`: plan quota left, whether sending is paused,
   frequency caps. If sending is not set up, switch to `setup-sending`.
3. `send_test` to the user's own address, one variant at a time. Ask which
   address first; this is a real email. Do not send tests to anyone else.

## 6. Launch or schedule: only after an explicit yes

Summarise one last time: audience name, recipient count from preflight,
subject(s), sender, and when. Then wait for a clear yes.

- Now: `launch_campaign` with the user's confirmation. A multi-variant
  campaign starts with an A/B pilot to a small share of the audience.
- Later: `schedule_send` with an ISO 8601 time and the user's timezone.
  `cancel_scheduled_campaign` returns it to a draft if they change their mind.
- If Mailbase refuses (quota, sender, screening, suppression), report the
  reason; do not retry with workarounds.

## 7. Watch it

`get_send_job_status` for progress and failed recipients. `control_send_job`
can pause or resume (resuming emails people again) or cancel (drops everything
not yet sent, for good); confirm first. `finalize_campaign` closes a campaign
stuck at live when its job has nothing left.

## 8. Results and the A/B winner

- `get_campaign_stats`, `get_ab_test_results` (per-variant rates and the
  verdict), `get_campaign_link_clicks` (human clicks apart from link scanners),
  `list_campaign_recipients`.
- If a variant clearly won: `pick_ab_winner` only records it. `rollout_ab_winner`
  mails the rest of the audience, so ask first and give the remaining count.
- Report honestly when the sample is too small to call a winner.

CLI: `mailbase campaigns list`, `mailbase audiences create`, `mailbase send`.
