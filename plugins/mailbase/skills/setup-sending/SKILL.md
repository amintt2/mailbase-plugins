---
name: setup-sending
description: "Use when someone needs to get Mailbase ready to send or is hitting deliverability problems: connect a sending domain, publish and verify DNS records, add sender addresses, connect mailboxes, set sending limits and read the deliverability report."
---

# Set up sending

Goal: a verified domain, at least one sender on it, healthy limits, and a
clean deliverability check. Read the confirmation rules in `get-started`.

## 0. Where things stand

`list_domains`, `list_senders`, `list_mailboxes` and `get_sending_settings`
(plan, quota left, whether sending is paused and why, frequency caps, quiet
hours). Tell the user what exists and what is missing before changing anything.

## 1. Connect the domain

Ask which domain they own and which they want to send from (a subdomain such
as `mail.example.com` protects the main domain's reputation).

- Optionally `check_domain_dns` first: reads MX, SPF, DKIM and DMARC from
  public DNS; works for domains that are not connected yet. Say that it does
  a DNS lookup.
- `connect_domain` asks the sending provider for the DNS records and returns
  them. It sends no email and buys nothing. Buying a domain is done in the
  Mailbase web app.

## 2. Show the DNS records

Present the records exactly as returned (type, name, value), as a table the
user can copy to their DNS host. Say which are for DKIM, SPF and DMARC and
that propagation can take minutes to hours. You cannot publish DNS for them.
Do not guess or shorten values.

## 3. Verify

When the user says the records are published, `verify_domain`. If it is still
pending, wait and retry later rather than looping. If verification failed
after the provider's time window, `restart_domain_verification` creates a
new identity and returns NEW records to publish (the old ones stop being valid);
explain that before calling it. `get_domain` shows the live status and warm-up.

New domains warm up gradually: daily volume ramps over weeks. Say so; do not
promise a large first send. If warm-up paused a domain for bounces,
`resume_domain` lets it send again one stage back; a pause for spam complaints
cannot be lifted here.

## 4. Sender addresses

`create_sender` adds a From address on a verified domain (or the workspace's
shared domain). `update_sender` renames it or makes it the default.
`delete_sender` needs a yes (never the last one). Suggest a real, monitored
address such as a person's name, not a no-reply: replies feed the inbox.

## 5. Mailboxes

Mailboxes (a Google, Microsoft or IMAP account used for inbox sync and for
prospecting outreach) are connected in the Mailbase web app because that needs
OAuth consent or a password; send the user to Settings. Never take a password
or token in the chat. After that: `list_mailboxes` and `get_mailbox`, `test_mailbox` (logs in to
their mail servers; say so), `update_mailbox`, `update_mailbox_outreach` (on or
off for prospecting, daily limit 1 to 50), `update_outreach_settings`.
`delete_mailbox` destroys stored credentials and needs a yes.

## 6. Limits and deliverability

- `update_frequency_caps`: per-recipient caps per day and per week, and quiet
  hours. Only the fields you send change.
- `deliverability_report`: blocklist readings and DMARC summaries per domain
  (returns disabled while monitoring is off; say so).
- `analytics_report` for bounce and complaint rates by provider. A bounce or
  complaint rate going up is a reason to slow down and clean the list, not to
  send more.
- Finish with a checklist: domain verified, sender default set, DMARC present,
  caps set, mailbox connected if replies should sync.

CLI: no domain commands; `mailbase whoami` shows the workspace and key scopes.
