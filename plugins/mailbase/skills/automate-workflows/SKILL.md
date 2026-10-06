---
name: automate-workflows
description: "Use when someone wants an automated email sequence or journey in Mailbase (welcome series, nurture, follow-up after a tag or signup): design the workflow as a draft, review it, then activate or enroll contacts only after explicit approval."
---

# Automate with workflows

A workflow emails people by itself once it is active. So: design as a draft,
review it with the user node by node, and activate only after a clear yes.
Read the confirmation rules in `get-started` first.

## 1. Look before you build

`list_workflows` (status, trigger, enrollment counts) and `get_workflow` for
any that look related, so you extend instead of duplicating. `list_templates`
for email content, `list_audiences`, `list_tags` for triggers.

## 2. Design

Clarify: what starts it (trigger), what the person should experience step by
step, delays, when someone should leave it (for example on reply or on
purchase), and who may enter. Keep it small: three to five steps first.

Node types: send_email, wait, condition, split_test, add_tag, remove_tag,
notify_webhook, create_task. Edges default to a chain. Draw it in the chat as a
short numbered list with the delay between steps.

## 3. Create the draft

`create_workflow` makes a DRAFT; it sends nothing. To change it later use
`update_workflow` (it replaces the whole graph, refused while the workflow is
active; pause it first) and confirm because the old graph is lost.

Write every email in full and show subject and body to the user. Check wording
with `check_template` on the body (spam-prone words, missing unsubscribe).

## 4. Review checklist (show it, get answers)

- Who can enter, and how many people that is right now (audience size).
- Every email: sender, subject, body, delay before it.
- Exit conditions, and that unsubscribed or suppressed contacts never get it.
- Webhooks: the URL belongs to the user. Do not invent endpoints.

## 5. Activate or enroll: only after an explicit yes

- `set_workflow_status` to active makes it email every contact who enrolls or
  matches its trigger, and the emails cannot be recalled.
- `enroll_in_workflow` enrolls specific contacts or a whole audience in an
  ACTIVE workflow; state exactly who and how many first.
- Consider enrolling one test contact (the user themselves) before an audience.

## 6. Operate

`get_workflow` shows enrollments and log lines. `pause_workflow` stops new
enrollments and steps at once; it is safe, use it when something looks wrong
and tell the user. `set_workflow_status` to archived retires it.
`delete_workflow` is permanent (history included) and needs a yes.

CLI: workflows are not in the CLI; use these tools or the web app.
