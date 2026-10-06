# mailbase command reference

Generated from the CLI's command table by `cli/scripts/gen-skill-reference.mjs`. Do not edit by hand.

Flags column: D = destructive (deletes or cannot be undone), O = outward (emails people or makes email go out).
Both need `--yes` when non-interactive; ask the user first. Scope is the API-key scope the command needs.
Run `mailbase <group> <command> --help` for every flag, or `mailbase commands --json` for all of them at once.

## Top-level commands

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase login` | Sign in with your browser (no API key needed). | - | local |
| `mailbase logout` | Sign out: revoke the browser session and remove the stored profile. | D | local |
| `mailbase whoami` | Show who you are signed in as: user, workspace, login kind, scopes, session expiry. | - | any key |
| `mailbase commands` | Print the whole command table (names, flags, methods, examples). | - | local |
| `mailbase api` | Call any /api/v1 endpoint directly (escape hatch). | D+O (any non-GET) | local |
| `mailbase send` | Send one transactional email (to up to 100 recipients) through the workspace's sender. | O | email:send |
| `mailbase deliverability` | Deliverability report: blocklist readings and DMARC summaries per sending domain. | - | analytics:read |
| `mailbase pipeline` | Show the pipeline board: conversations by stage with counts and value. | - | inbox:read |

## domains: Sending domains: connect, verify DNS, warm-up, remove.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase domains list` | List sending domains with verification status, warm-up and DNS records. | - | sending:read |
| `mailbase domains get` | Get one sending domain with the DNS records to publish. | - | sending:read |
| `mailbase domains connect` | Connect a domain you own for sending. | - | sending:write |
| `mailbase domains check` | Check any domain's DNS health (MX, SPF, DKIM, DMARC). | - | sending:read |
| `mailbase domains verify` | Re-check a domain's DNS and read the verdict back. | - | sending:write |
| `mailbase domains restart` | Start a failed domain's verification over (new DKIM records). | - | sending:write |
| `mailbase domains resume` | Lift a bounce-rate pause so the domain can send again. | O | sending:write |
| `mailbase domains delete` | Remove a sending domain. | D | sending:write |

## senders: Sender (From) addresses.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase senders list` | List sender (From) addresses and the domains you can add one on. | - | sending:read |
| `mailbase senders add` | Add a sender address. | - | sending:write |
| `mailbase senders update` | Rename a sender or make it the default. | - | sending:write |
| `mailbase senders delete` | Delete a sender address. | D | sending:write |

## mailboxes: Connected mailboxes and mailbox outreach for prospecting.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase mailboxes list` | List connected mailboxes (never returns passwords or tokens). | - | sending:read |
| `mailbase mailboxes get` | Get one mailbox. | - | sending:read |
| `mailbase mailboxes update` | Adjust a mailbox's display name and inbox-sync settings. | - | sending:write |
| `mailbase mailboxes test` | Test a mailbox's IMAP and SMTP login (and activate it if both work). | - | sending:write |
| `mailbase mailboxes delete` | Disconnect a mailbox. | D | sending:write |
| `mailbase mailboxes outreach get` | Get the workspace's mailbox-outreach settings. | - | sending:read |
| `mailbase mailboxes outreach set` | Set whether prospecting may fall back to the sending domain. | O | sending:write |
| `mailbase mailboxes set-outreach` | Control prospecting from one mailbox: on/off, daily limit, resume after a pause. | O | sending:write |

## sending: Sending status, quota and per-contact frequency caps.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase sending get` | Show sending status (plan, quota, pauses) and frequency caps. | - | sending:read |
| `mailbase sending caps set` | Update per-contact frequency caps and quiet hours. | - | sending:write |

## analytics: Workspace and prospecting analytics.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase analytics overview` | The workspace analytics dashboard: totals, rates, timelines, top campaigns. | - | analytics:read |
| `mailbase analytics prospecting` | The prospecting funnel (found to meeting), reply rate per step, first-email A/B test. | - | analytics:read |

## sent: The sent log (one-off emails and thread replies).

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase sent list` | List the sent log: one-off emails and thread replies, newest first. | - | analytics:read |
| `mailbase sent events` | List the delivery events (opens, clicks, bounces) of one sent email. | - | analytics:read |

## campaigns: Campaigns: create, A/B test, launch or schedule, follow the send job.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase campaigns list` | List campaigns in the workspace. | - | campaigns:read |
| `mailbase campaigns get` | Read one campaign: variants with stats, A/B summary, rollup, latest send job. | - | campaigns:read |
| `mailbase campaigns create` | Create a DRAFT campaign (nothing is sent). | - | campaigns:write |
| `mailbase campaigns update` | Edit a draft campaign (or the A/B settings of a running one). | - | campaigns:write |
| `mailbase campaigns delete` | Delete a draft campaign. | D | campaigns:write |
| `mailbase campaigns duplicate` | Copy a campaign and its variants into a new draft. | - | campaigns:write |
| `mailbase campaigns archive` | Hide a campaign from the default list (reversible, keeps all data). | - | campaigns:write |
| `mailbase campaigns unarchive` | Bring an archived campaign back into the default list. | - | campaigns:write |
| `mailbase campaigns finalize` | Close out a campaign stuck at live whose send job has nothing left to do. | - | campaigns:write |
| `mailbase campaigns preflight` | Check which merge tags would be filled and how many people would receive the email. | - | campaigns:read |
| `mailbase campaigns recipients` | List who a campaign was sent to, with status and engagement. | - | campaigns:read |
| `mailbase campaigns links` | Clicks per link: human clicks, unique clickers and link-scanner clicks. | - | campaigns:read |
| `mailbase campaigns test` | Send one real test email of a variant to one address. | O | campaigns:send |
| `mailbase campaigns launch` | Send the campaign to its audience NOW. | O | campaigns:send |
| `mailbase campaigns schedule` | Schedule the campaign to be sent at a future time. | O | campaigns:send |
| `mailbase campaigns cancel-schedule` | Turn a scheduled campaign back into a draft. | - | campaigns:send |
| `mailbase campaigns job get` | Progress of the campaign's latest send job. | - | campaigns:read |
| `mailbase campaigns job pause` | Pause the active send job (nothing more goes out until resumed). | - | campaigns:send |
| `mailbase campaigns job resume` | Resume a paused send job: remaining emails go out again. | O | campaigns:send |
| `mailbase campaigns job cancel` | Cancel the active send job: every email not yet sent is dropped and the campaign ends. | D | campaigns:send |
| `mailbase campaigns job abandon-retries` | Stop retrying the send job's failed items. | D | campaigns:send |
| `mailbase campaigns ab get` | A/B test settings, per-variant results and whether a winner can be called yet. | - | campaigns:read |
| `mailbase campaigns ab winner` | Declare an A/B winner by hand. | - | campaigns:write |
| `mailbase campaigns ab rollout` | Mail the rest of the audience with the A/B winner. | O | campaigns:send |

## audiences: Audiences (static lists and rule-based segments).

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase audiences list` | List audiences with their contact counts. | - | audiences:read |
| `mailbase audiences create` | Create a static or rule audience, optionally importing contacts. | - | audiences:write |
| `mailbase audiences add-contacts` | Append contacts to an existing static audience. | - | audiences:write |
| `mailbase audiences get` | Fetch one audience with its live contact count. | - | audiences:read |
| `mailbase audiences update` | Rename, re-describe, re-rule or archive an audience. | - | audiences:write |
| `mailbase audiences delete` | Delete an audience permanently. | D | audiences:write |
| `mailbase audiences preview` | Count the contacts a set of segment rules would match. | - | audiences:read |
| `mailbase audiences remove-contact` | Remove one contact from a static audience (the contact itself is untouched). | D | audiences:write |
| `mailbase audiences bulk` | Tag, move, suppress or resubscribe a selection of an audience's members. | O | audiences:write |
| `mailbase audiences duplicates` | Find duplicate groups inside an audience (by e-mail, domain, company). | - | audiences:read |
| `mailbase audiences export` | List an audience's members as JSON pages or CSV. | - | audiences:read |

## contacts: Contacts: read, create or update, suppress.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase contacts list` | List contacts (keyset-paginated). | - | contacts:read |
| `mailbase contacts get` | Fetch one contact by id or email. | - | contacts:read |
| `mailbase contacts upsert` | Create a contact, or update it if the email exists. | - | contacts:write |
| `mailbase contacts update` | Update fields of an existing contact (id or email). | - | contacts:write |
| `mailbase contacts suppress` | Suppress (or unsubscribe) a contact so it is never emailed again. | D | contacts:write |
| `mailbase contacts profile` | Fetch the CRM profile of a contact: audiences, tags, sends, events, threads, notes. | - | contacts:read |
| `mailbase contacts note` | Add an internal note to a contact. | - | contacts:write |
| `mailbase contacts assign` | Set the owner and/or assignee of a contact. | - | contacts:write |
| `mailbase contacts erase` | Erase one contact for good (GDPR art. 17). Irreversible. | D | contacts:write |
| `mailbase contacts erasures` | List this workspace's contact-erasure log (opaque: no addresses). | - | contacts:read |

## suppressions: People blocked from marketing sends (unsubscribed, bounced, suppressed).

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase suppressions list` | List contacts blocked from marketing sends. | - | contacts:read |
| `mailbase suppressions add` | Suppress one contact, or many from a list or CSV. | - | contacts:write |
| `mailbase suppressions remove` | Lift a suppression: the contact becomes mailable again. | D+O | contacts:write |

## templates: Email templates (MJML): edit, version, preview, check, test-send.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase templates list` | List email templates. | - | templates:read |
| `mailbase templates create` | Create an MJML template. | - | templates:write |
| `mailbase templates get` | Read one template (built-ins included) with its builder schema and version. | - | templates:read |
| `mailbase templates update` | Edit a template (writes a new version). | - | templates:write |
| `mailbase templates delete` | Delete a template. | D | templates:write |
| `mailbase templates duplicate` | Copy a template (built-in or yours) into an editable one. | - | templates:write |
| `mailbase templates versions` | Version history of a template, newest first. | - | templates:read |
| `mailbase templates restore` | Make a past version the current one (history is kept). | - | templates:write |
| `mailbase templates render` | Compile MJML to HTML for a preview (nothing is stored or sent). | - | templates:read |
| `mailbase templates check` | Deliverability checklist for a template or draft body (spam wording, unsubscribe, links). | - | templates:read |
| `mailbase templates check-links` | Fetch a template's links (public hosts only) and report broken ones. | - | templates:read |
| `mailbase templates test` | Send one real test email of a template or draft body to one address. | O | campaigns:send |

## forms: Signup forms that feed an audience.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase forms list` | List signup forms. | - | forms:read |
| `mailbase forms get` | Fetch one signup form with its recent submissions. | - | forms:read |
| `mailbase forms create` | Create a signup form that feeds an audience. | - | forms:write |
| `mailbase forms update` | Edit, pause or restore a signup form. | - | forms:write |
| `mailbase forms delete` | Archive a signup form (submissions are kept). | D | forms:write |

## topics: Subscription topics (preference center categories).

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase topics list` | List subscription topics (the opt-in categories of the preference center). | - | forms:read |
| `mailbase topics create` | Create a subscription topic. | - | forms:write |
| `mailbase topics update` | Edit or restore a subscription topic. | - | forms:write |
| `mailbase topics delete` | Archive a subscription topic (opt-outs are kept). | D | forms:write |

## threads: The reply inbox: threads, replies, notes, tags, pipeline stage, follow-ups.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase threads list` | List reply threads (the reply inbox), newest first. | - | inbox:read |
| `mailbase threads get` | Get a thread: messages (plain text), notes and follow-ups. | - | inbox:read |
| `mailbase threads update` | Archive, star or assign a thread. | - | inbox:write |
| `mailbase threads reply` | Send a reply on a thread. | O | email:send |
| `mailbase threads notes list` | List a thread's internal notes. | - | inbox:read |
| `mailbase threads notes add` | Add an internal note to a thread (never sent to the contact). | - | inbox:write |
| `mailbase threads tag` | Add and remove tags on a thread. | - | inbox:write |
| `mailbase threads stage` | Set a thread's pipeline stage (new, qualified, meeting, proposal, won, lost). | - | inbox:write |
| `mailbase threads follow-ups list` | List a thread's follow-ups. | - | inbox:read |
| `mailbase threads follow-ups add` | Schedule a follow-up reminder (a dated to-do for the team). | - | inbox:write |

## follow-ups: Follow-up reminders on threads.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase follow-ups update` | Complete, dismiss or reopen a follow-up. | - | inbox:write |

## tags: Thread tags.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase tags list` | List thread tags. | - | inbox:read |
| `mailbase tags create` | Create a tag. | - | inbox:write |
| `mailbase tags update` | Rename or recolour a tag. | - | inbox:write |
| `mailbase tags delete` | Delete a tag (removes it from every thread that carries it). | D | inbox:write |

## workflows: Automations: create, activate, pause, enroll contacts, fire events.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase workflows list` | List workflows (automations). | - | workflows:read |
| `mailbase workflows get` | Get a workflow with its nodes, edges, latest enrollments and log lines. | - | workflows:read |
| `mailbase workflows create` | Create a workflow (a draft unless --status says otherwise). | O (some inputs) | workflows:write |
| `mailbase workflows update` | Update a workflow (name, trigger, graph, status). | O (some inputs) | workflows:write |
| `mailbase workflows activate` | Activate a workflow: it starts emailing contacts. | O | workflows:write |
| `mailbase workflows pause` | Pause a workflow: nothing more is sent by it. | - | workflows:write |
| `mailbase workflows delete` | Delete a workflow with its enrollments and history. | D | workflows:write |
| `mailbase workflows enroll` | Enroll contacts in an active workflow (starts emailing them). | O | workflows:send |
| `mailbase workflows event` | Fire a workflow api_event for one contact. | O | workflows:send |

## events: Delivery and engagement events (delivered, opened, clicked, replied, ...).

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase events list` | List delivery and engagement events (keyset-paginated). | - | events:read |

## contact-events: Behavioural events you record about contacts (signup, purchase, ...).

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase contact-events track` | Record a behavioural event for a contact (signup, purchase, ...). | - | events:write |
| `mailbase contact-events list` | List stored behavioural events (keyset-paginated). | - | events:read |
| `mailbase contact-events names` | List the event names this workspace records. | - | events:read |
| `mailbase contact-events summary` | Aggregate event counts and conversion value. | - | events:read |

## inbound: Reply address and sending-domain authentication status.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase inbound status` | Show the reply address and sending-domain authentication (SPF/DKIM/DMARC). | - | any key |

## prospecting: Prospecting: targets, progress, NAF and town lookup, business profile, blocklist, audiences from prospects, manual tasks.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase prospecting targets list` | List prospecting targets (saved searches over the French company registry). | - | prospecting:read |
| `mailbase prospecting targets create` | Create a prospecting target. | - | prospecting:write |
| `mailbase prospecting counts` | Counts for the pool, drafts, metrics, quota and sequence. | - | prospecting:read |
| `mailbase prospecting targets pause` | Pause a target: the engine stops paging the company register for it. | - | prospecting:write |
| `mailbase prospecting targets resume` | Resume a paused target: the engine pages the company register again. | - | prospecting:write |
| `mailbase prospecting targets delete` | Delete a target (companies it found stay in the pool). | D | prospecting:write |
| `mailbase prospecting targets sequence get` | Get a target's sequence overrides next to the workspace defaults. | - | prospecting:read |
| `mailbase prospecting targets sequence set` | Replace a target's sequence overrides (tone, signature, steps, A/B). | - | prospecting:write |
| `mailbase prospecting progress` | Watch targets work: where the register pager is and how many companies reached each step. | - | prospecting:read |
| `mailbase prospecting run` | Run company-register discovery now (instead of waiting for the scheduler). | - | prospecting:write |
| `mailbase prospecting naf` | Find NAF activity codes from the first letters ('plom' finds Plombier). | - | prospecting:read |
| `mailbase prospecting places` | Find French towns by name or postal code. | - | prospecting:read |
| `mailbase prospecting profile get` | Get the business profile the sequence drafts from (offer, ideal customer, tone). | - | prospecting:read |
| `mailbase prospecting profile set` | Update the business profile (only the flags you pass change). | - | prospecting:write |
| `mailbase prospecting exclusions list` | List the blocklist (SIRENs, domains, addresses Mailbase never writes to). | - | prospecting:read |
| `mailbase prospecting exclusions add` | Block a company (SIREN), domain or address. | - | prospecting:write |
| `mailbase prospecting exclusions import` | Block a pasted list (SIRENs, domains, addresses; customers, competitors). | - | prospecting:write |
| `mailbase prospecting exclusions remove` | Remove a block, or undo a bulk import. | D+O | prospecting:write |
| `mailbase prospecting audiences count` | How many prospects of some targets could become an audience, and why the rest cannot. | - | prospecting:read |
| `mailbase prospecting audiences create` | Create a static audience from verified prospects, so a campaign can be sent to them. | - | prospecting:write |
| `mailbase prospecting tasks list` | List the sequence's manual steps (a call, a LinkedIn visit) with phone and site. | - | prospecting:read |
| `mailbase prospecting tasks update` | Mark a manual step done or skipped (the sequence moves on). | - | prospecting:write |

## pool: The prospect pool: companies found by your targets.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase pool list` | List companies in the prospect pool. | - | prospecting:read |
| `mailbase pool facets` | Counts per stage, département, NAF code, headcount band and verification result. | - | prospecting:read |
| `mailbase pool archive` | Archive pool companies (the pool's Delete): they leave the pool and the engine. | D | prospecting:write |
| `mailbase pool restore` | Bring archived companies back into the pool. | - | prospecting:write |
| `mailbase pool delete` | Permanently delete ARCHIVED companies. | D | prospecting:write |
| `mailbase pool overrule` | Overrule the AI's off-target verdict: put companies back in the pipeline. | - | prospecting:write |
| `mailbase pool get` | Get one prospect company with its drafts. | - | prospecting:read |

## drafts: Prospect email drafts waiting for review.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase drafts list` | List prospect email drafts (default: proposed, awaiting review). | - | prospecting:read |
| `mailbase drafts approve` | Approve a proposed draft so the sequence will email it. | O | prospecting:write |
| `mailbase drafts reject` | Reject a proposed draft. | D | prospecting:write |
| `mailbase drafts bulk-approve` | Approve several proposed drafts at once so the sequence will email them. | O | prospecting:write |
| `mailbase drafts bulk-reject` | Reject several drafts at once. | D | prospecting:write |
| `mailbase drafts regenerate` | Rewrite one draft with the app's writing model, optionally following feedback. | - | prospecting:write |
| `mailbase drafts regenerate-pending` | Apply one note to all pending (proposed) drafts. | - | prospecting:write |

## sequence: The prospecting sequence (the sender that works through approved drafts).

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase sequence get` | Show the prospecting sequence state (enabled, daily cap, sent today). | - | prospecting:read |
| `mailbase sequence pause` | Pause the prospecting sequence: nothing more is sent until resumed. | - | prospecting:write |
| `mailbase sequence resume` | Resume the prospecting sequence: approved drafts start going out. | O | prospecting:write |
| `mailbase sequence set` | Change the prospecting sequence settings (daily cap, window, language, signature, mode). | O (some inputs) | prospecting:write |

## profiles: Stored logins for several workspaces.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase profiles list` | List stored profiles (never prints keys). | - | local |
| `mailbase profiles use` | Make a stored profile the default. | - | local |

## skill: Teach your coding agent (Claude Code, Codex) to use this CLI: install the bundled skill.

| Command | What it does | Flags | Scope |
| --- | --- | --- | --- |
| `mailbase skill install` | Install the mailbase skill so Claude Code and/or Codex know how to use this CLI. | - | local |
| `mailbase skill print` | Print the bundled SKILL.md to stdout. | - | local |
| `mailbase skill path` | Print the folder of the bundled skill inside the installed CLI. | - | local |
