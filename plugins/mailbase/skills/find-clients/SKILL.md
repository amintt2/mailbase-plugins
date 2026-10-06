---
name: find-clients
description: "Use when someone wants to find new customers or prospects in Mailbase: turn an ideal-customer brief into French company targets (SIRENE register, NAF codes), manage the prospect pool, set the business profile and sequence, review the outreach emails drafted for each company, and only then switch sending on. France only."
---

# Find clients

Mailbase's prospecting engine searches the public French company register
(SIRENE) for companies that match an ideal-customer brief, finds a public
business contact for each, and drafts outreach emails for a person to approve.
Steps 1 to 6 contact nobody. Step 7 (approving emails, turning the sequence on)
emails real people, so each decision is the user's. Read the confirmation rules
in `get-started` first.

France only: if the user sells elsewhere, say so and stop.

## 1. Brief (ask only what is missing)

What they sell, who buys it (activity, size in staff, region), who inside the
company decides, and what a good first reply looks like. Check what exists:
`get_business_profile`, `list_prospect_targets`, `prospecting_counts`,
`list_prospect_exclusions` (the "never contact" list).

## 2. Business profile and exclusions

Drafts are written from the business profile, so it must be true.
`update_business_profile` (website, summary, offerings, ideal customer, tone,
segments) from what the user told you, never from guesses; show it back.
Ask for existing customers and competitors, then `add_prospect_exclusions`
(SIRENs, domains, emails) with `dry_run` first to show the counts.

## 3. Turn the brief into a target

1. `search_naf_codes` with the first letters of each activity. Show the
   candidates (code and label) and let the user pick. Never invent a code.
2. For a town or radius: `search_prospect_places` (looks up geo.api.gouv.fr).
3. Show the target you will create: name, NAF codes, départements or place,
   headcount bands. After the user agrees, `create_prospect_target`. It only
   searches the register and adds companies to the pool; it contacts nobody.
   Target names must be unique.
4. `run_prospect_discovery` runs one pass now instead of waiting for the
   scheduler. `update_prospect_target` pauses or resumes discovery;
   `delete_prospect_target` is permanent (the companies it found stay) and
   needs a yes.

## 4. Watch the pool fill

`get_prospect_progress` (per target: register page, site found, contact found,
verified) and `prospecting_counts`. Discovery and enrichment take minutes to
hours; report counts, do not loop. `prospect_pool_facets` shows what is in the
pool by stage, département, NAF code, headcount, verification and website.

## 5. Prune the pool

Look at it before any email is drafted: `search_prospect_pool` (or `list_prospect_companies` for one stage; filters: stage,
AI check, target, verification, département, NAF, headcount, has website, free
text; `total` gives the count) and `get_prospect_company`. Show a sample of
companies per segment and ask what looks wrong.

- `archive_prospect_companies` removes them from the pool and the engine and
  cancels their pending drafts; `restore_prospect_companies` undoes it. For a
  select-all ("everything matching these filters") state the exact filter and
  the count first.
- `restore_fit_rejections` puts back companies the AI check set aside wrongly.
- `delete_prospect_companies` is permanent and only touches archived ones;
  needs a yes, never as a clean-up default.

## 6. Sequence settings and drafts

- `get_prospect_sequence_settings`: mode (review or auto), daily cap, steps,
  send window, language, signature, call to action. `update_prospect_sequence_settings`
  changes them. Keep it on **review** mode. Turning the sequence on or to auto
  needs a signature and a call to action from the user, and an explicit yes.
- Per-target wording: `get_prospect_target_sequence`, `update_prospect_target_sequence`.
- Drafts appear as proposed. `list_prospect_drafts` (status proposed, oldest
  first). For each: company, contact, subject, full body, screening note. Check
  it is accurate about the company, makes one clear ask, has no invented claims
  and could not be mistaken for a scam.
- Fix wording: `regenerate_prospect_draft` (one, with a note such as "shorter"),
  `regenerate_pending_prospect_drafts` (one correction for the oldest 20), or
  pass an edited subject and body to `approve_prospect_draft`.

## 7. Decide: only the user decides

- `approve_prospect_draft` queues one email for the next sending window, within
  the daily cap and after scam screening. Only after the user read that exact
  email and said to approve it. `reject_prospect_draft` discards it.
- `bulk_decide_prospect_drafts`: approving (max 50) or rejecting (max 200) many
  at once. List exactly which emails and confirm. Bulk approval is refused until
  the user has approved one draft by hand: let them do that first.
- `resume_prospect_sequence` starts drafting and sending the approved emails
  and follow-ups. Do it only when the user explicitly asks. `pause_prospect_sequence`
  is always safe; offer it if anything looks wrong.
- Manual steps (a call, a LinkedIn visit): `list_prospect_tasks`, then
  `resolve_prospect_task` only when the user says it was done or skipped.

## 8. After

`prospecting_analytics` (funnel and reply rate per step). Replies land in the
inbox (see `triage-replies`). To email prospects as a campaign instead,
`count_prospect_audience_candidates` then `create_audience_from_prospects`
(skips unsubscribed, bounced and blocklisted), then `launch-campaign`.

CLI: `mailbase prospecting targets create`, `mailbase pool list`,
`mailbase drafts list|approve|reject`, `mailbase sequence get|pause|resume`.
