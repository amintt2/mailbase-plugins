---
name: audiences-and-contacts
description: "Use when someone works on people data in Mailbase: build segments or static audiences, import or update contacts, handle unsubscribes and suppressions, set up signup forms and topics, or process a GDPR erasure request. Includes the rules for consent and deletion."
---

# Audiences and contacts

Read the confirmation rules in `get-started` first. People data is personal
data: collect the minimum, and never use it to evade an opt-out.

## Find and read

`search_contacts` (email, name, company, with a status filter),
`get_contact_profile` (audiences, tags, sends, events, replies, notes),
`list_audiences`, `get_audience`, `export_audience_contacts` (paged, optional
status filter), `list_audience_duplicates` (by email, domain, company; nothing
is merged).

## Segments and static lists

- `preview_audience_rules` counts what a set of rules would match with a small
  sample. Always preview before creating or changing a rule audience.
- `create_audience` (static or rule) and `update_audience` (rename, rules,
  archive or restore; archiving is reversible).
- `add_audience_contacts` imports into a static audience (dedupes by email).
  Rule audiences are computed and cannot take manual contacts.
- `remove_audience_contacts` removes membership only (the contact stays) and
  needs a yes. `delete_audience` is permanent and also removes its signup
  forms; ask first.
- `bulk_audience_action` tags, moves, suppresses or resubscribes a selection.
  Tagging or moving can trigger workflows that send mail, so confirm the
  selection and the action.

## Contacts

`upsert_contact` (create or update by email; never changes subscription
status), `update_contact`, `assign_contact` (owner/assignee),
`add_contact_note` (internal only).

## Opt-outs and suppressions

- `list_suppressions` shows who is blocked from marketing sends.
- `suppress_contact` marks unsubscribed, bounced, complained or suppressed.
  Do this immediately on request, with no pushback.
- `resubscribe_contact` only when the user states consent was verified (the
  person asked to come back). Mailbase refuses to resubscribe spam complainers.
  Never use it to get around an opt-out.

## Signup forms and topics

`list_forms`, `get_form` (hosted URL, embed snippet, recent submissions),
`create_form` (feeds a static audience; double opt-in sends a confirmation
email), `update_form` (pause or restore), `delete_form` (archives; the consent
trail is kept). Topics are opt-in categories: `list_topics`, `create_topic`,
`update_topic`, `delete_topic` (archives). A campaign can name a topic so
people who opted out of it are skipped.

## GDPR erasure (art. 17)

Only when the person or the user explicitly asks for that contact to be erased:

1. `get_contact_profile` and tell the user what will go: the record, send
   history, notes, and that the address is blocked from future imports.
2. Explain the difference: `suppress_contact` keeps a record and stops mail;
   `erase_contact` is irreversible. If the goal is only "stop emailing",
   suppress instead.
3. After an explicit yes, `erase_contact` with the contact's address retyped
   as confirmation (needs an owner or admin). One contact per request.
4. `list_contact_erasures` shows the log (opaque: no addresses).

Never erase in bulk or "to clean up".

CLI: `mailbase contacts list|get|upsert|update|suppress`, `mailbase audiences
list|create|add-contacts`.
