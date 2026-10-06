---
name: get-started
description: "Use for anything in Mailbase, the email workflow app (campaigns, reply inbox, contacts and audiences, templates, workflows, sending domains, prospecting). Start here to learn what the Mailbase tools can do, which ones email real people or delete data, and which workflow skill to follow next."
---

# Mailbase

Mailbase is an email workflow app: campaigns with A/B variants, a reply inbox
sorted by intent, contacts and audiences, templates, automation workflows,
sending domains and mailboxes, deliverability monitoring, and a prospecting
engine that finds French companies, drafts outreach and waits for a person to
approve it.

The connection is bound to the one workspace the user picked when they signed
in. Call `current_workspace` (or `list_workspaces`) to say which one; to work in
another workspace the user reconnects Mailbase and picks it. `create_workspace`
makes a new one but does not switch this connection to it. Every tool re-checks the user's current
role, so a refusal like "your role does not allow..." is final: tell the user.

## Which skill to follow

| The user wants to... | Follow |
| --- | --- |
| Find new customers (French companies) and review the outreach drafted for them | `find-clients` |
| Build, test and send a campaign, then read A/B results | `launch-campaign` |
| Work through the reply inbox | `triage-replies` |
| Connect a domain, add senders, check deliverability | `setup-sending` |
| Build or run an automation workflow | `automate-workflows` |
| Segments, imports, suppressions, signup forms, GDPR erasure | `audiences-and-contacts` |

Quick answers without a workflow:

- "What needs my attention?" -> `list_replies` (hot and warm first), then `get_reply`.
- "How are my emails doing?" -> `list_campaigns` then `get_campaign_stats`, or `analytics_report` for the whole workspace.
- "Who is this person?" -> `search_contacts` then `get_contact_profile`.
- "Is sending healthy?" -> `get_sending_settings`, `deliverability_report`, `list_domains`.
- "What did we send?" -> `list_sent_emails`, `get_sent_email_events`.

## Confirmation rules (apply to every skill)

Read-only tools (`list_*`, `get_*`, `search_contacts`, `analytics_report`,
`campaign_preflight`, `render_template`, `preview_audience_rules`, ...) need no
permission. Everything below needs an explicit "yes" from the user in this
conversation, for this exact action, after you showed them what will happen.
A yes for one action is not a yes for the next one.

**Emails real people (cannot be recalled):**
`send_test`, `send_template_test`, `schedule_send`, `launch_campaign`,
`rollout_ab_winner`, `control_send_job` (resuming), `reply_to_thread`,
`approve_prospect_draft`, `bulk_decide_prospect_drafts` (approving),
`resume_prospect_sequence`, `update_prospect_sequence_settings` (switching the
sequence on or to automatic), `set_workflow_status` (to active),
`enroll_in_workflow`, `bulk_audience_action` (tagging or moving members can
fire a workflow that sends).
Show the exact recipients (or the count and the audience), subject and text
first. Prefer a draft and a test to the user's own address before any live send.

**Deletes or cannot be undone:**
`erase_contact` (GDPR erasure, irreversible), `delete_campaign`,
`delete_template`, `delete_audience`, `remove_audience_contacts`,
`delete_workflow`, `update_workflow`, `delete_domain`, `delete_sender`,
`delete_mailbox`, `delete_form`, `delete_topic`, `delete_tag`,
`delete_prospect_companies`, `delete_prospect_target`,
`remove_prospect_exclusion`, `reject_prospect_draft`, and cancelling a send
with `control_send_job`. Name what will be lost. Never run a delete in bulk,
loop over a list of deletions, or delete "everything"; one confirmed item at a
time (archiving prospects with `archive_prospect_companies` is reversible but a
select-all still needs a yes).

**Reaches outside the workspace (no email to people, but say what you do):**
`connect_domain`, `verify_domain`, `restart_domain_verification`,
`check_domain_dns` (DNS lookups), `test_mailbox` (logs in to the user's mail
servers), `check_template_links` (fetches the links in a template),
`create_prospect_target`, `run_prospect_discovery` and `search_prospect_places`
(query the public French company register and geo.api.gouv.fr; they contact
nobody), `regenerate_prospect_draft` (calls the writing model; sends nothing).

Never ask for, accept or store passwords, API keys or OAuth tokens in the chat.
Mailbox passwords and OAuth consent happen in the Mailbase web app.

## When Mailbase refuses

Mailbase has its own safety checks: suppressed, bounced or complained
addresses are skipped, daily caps and plan quotas apply, scam screening can
block a message, and a sender or domain may not be set up. When a send is
refused, tell the user the reason in plain words and what they can do. Do not
try to work around it (another tool, another sender, rewording to evade
screening).

Do not send misleading email: no impersonation, fake urgency, or requests for
passwords or payment details. Refuse and say why.

## Limits

- Prospecting searches the French company register (SIRENE) only.
- Mailboxes (IMAP or Google/Microsoft OAuth) and buying domains are done in the web app.
- Sending needs a sender address on a verified domain (see `setup-sending`).

## The Mailbase CLI

The same account is scriptable from a terminal with `mailbase` (npm package,
no AI inside): groups like `campaigns`, `audiences`, `contacts`, `drafts`,
`pool`, `sequence`; `--json` for machine output, `--yes` to confirm outward
commands (only after the user said yes), `--no-input` to never prompt.
`mailbase --help` and `mailbase commands --json` list everything; it covers the
REST API, which is narrower than this tool set.
