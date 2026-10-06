---
name: triage-replies
description: "Use when someone wants to work through their Mailbase reply inbox: see which replies need attention by intent, read threads, add notes, tags, a pipeline stage or follow-ups, and draft a reply for approval. Never sends a reply without explicit approval."
---

# Triage replies

Goal: leave the user with a short list of what to do, and drafts they can
approve. Read-only until step 5. Read the confirmation rules in `get-started`.

## 1. Survey

- `list_replies` with no filter, then by intent: hot, warm, objection, cold,
  ooo (out of office). Start with hot and warm.
- `get_pipeline` and `list_follow_ups` show what is already being handled.
- Present a compact list: contact, company, subject, intent, age, what they
  asked in one line. Offer to take them in priority order.

## 2. Read one thread

`get_reply` returns every message and the agent's suggestion. Also
`list_thread_notes` for what the team already said, and `get_contact_profile`
when history matters (previous campaigns, opens, tags). Summarise what the
person wants in one or two lines, in the user's language.

## 3. Classify and record (internal only, nothing is sent)

Offer these, and do them when the user agrees:

- `add_thread_note` for a team-only note (never emailed to the contact).
- `tag_thread` with tags from `list_tags` (`create_tag` if none fits).
- `set_pipeline_stage`: new, qualified, meeting, proposal, won, lost, with a
  value if known.
- `schedule_follow_up` for a dated reminder; `cancel_follow_up` to complete or
  dismiss one.
- `update_thread` to archive, star or assign to a teammate.
- Unsubscribe or "stop emailing me" requests: `suppress_contact` right away,
  and tell the user. Do not write a persuasive reply to someone who opted out.

## 4. Draft the reply in the chat

Write the reply as plain text and show it with the recipient and the thread
subject. Match the contact's language and tone, answer exactly what they
asked, one clear next step (a time, a link the user gave you). Do not invent
prices, dates, features or promises; leave a bracketed placeholder and ask.
Offer a shorter or warmer variant if useful.

## 5. Send: only after approval of the exact text

`reply_to_thread` with the approved text. It emails the contact from the
workspace sender and cannot be recalled. If the user edits the draft, show the
final text again before sending. One thread at a time; never batch-send replies.

If it is refused (suppressed address, no sender, quota), explain why.

## 6. Wrap up

After each thread, propose the next one. At the end, summarise: replied,
noted, follow-ups scheduled, still waiting on the user.

CLI: `mailbase events list` shows reply events; answering a reply is done with
these tools or in the web app.
