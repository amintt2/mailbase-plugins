---
name: mailbase-cli
description: "Use when the user wants to work in Mailbase (the email workflow app: campaigns, reply inbox, contacts, templates, workflows, sending domains, French prospecting) from the shell, scripts or CI, or when the mailbase command is installed, or when no Mailbase MCP connection is available. Drives the mailbase CLI: install, login check, command discovery, JSON output, confirmation rules and recipes. If the Mailbase MCP tools are connected, prefer them over this skill."
---

# Mailbase CLI

`mailbase` is a thin command-line client for the Mailbase REST API. It has no AI
inside: you drive it, read its JSON, and chain commands. Everything the app can
do through its API is a command. Prefer the Mailbase MCP tools when they are
connected in this session; use the CLI when they are not, or for scripts and CI.

## 1. Check the install

Run `mailbase --version`. If the command is missing, tell the user and, once
they agree, run `npm i -g mailbase` (or use `npx mailbase <command>` without
installing). It needs Node 20 or newer.

## 2. Check authentication

Run `mailbase whoami --json --no-input`. It prints the user, the workspace, the
login kind and the scopes. Say which workspace you act on before you change
anything.

- Exit 3 (not signed in, or the session expired): sign in with the browser. No
  API key is involved and you never see a token.
  - On the user's own machine, run `mailbase login --browser`. It opens their
    browser. Tell them to sign in, pick the workspace and the permissions, and
    approve; the command finishes by itself when they do.
  - In a sandbox, a remote shell or over SSH, or if it reports no browser, run
    `mailbase login --device --no-wait --json`. It prints verification_uri_complete
    and user_code and returns. Show the user the link and the code, ask them to
    open it on any device and approve, then run `mailbase login --device --continue`
    (it waits until they do; exit 6 means they denied it, exit 3 means the code
    expired and you start again).
  - A device login leaves the email-sending permissions (email:send,
    campaigns:send, workflows:send, sending:write) unticked by default, so a
    session started that way can read and prepare things but cannot send.
  - Never read or print the config file (`~/.config/mailbase/config.json`) or any
    token. The CLI refreshes the session itself.
- CI and servers only: set `MAILBASE_API_KEY` (and `MAILBASE_API_URL` if the
  server is not the default), or the user pipes a key into
  `mailbase login --api-key-stdin`. Keys are created in the web app under
  Settings, API (preset "Everything (CLI & agents)"); a narrower key is safer for
  unattended jobs. Do not ask the user to paste a key to you.
- Exit 5 (permission denied): the session or key lacks a scope, or the user's
  role is too low. The error names the scope. If it is one of email:send,
  campaigns:send, workflows:send or sending:write, tell the user to run
  `mailbase login` again and tick the "sends email" permissions on the consent
  screen (then repeat the command); never work around it. For another scope the
  user ticks it the same way; for an API key they create a broader key.

## 3. Discover commands

- `mailbase commands --json` lists every command with its flags, types,
  whether it is destructive or outward, the REST call and examples. Run it once
  and reuse it.
- `mailbase <group> <command> --help` gives one command in detail, for example
  `mailbase campaigns launch --help`.
- The full list is in [references/commands.md](references/commands.md).

## 4. Rules that always apply

1. Pass `--json --no-input` on every call. Parse stdout as JSON. Stderr holds
   diagnostics (warnings, hints); it is not data.
2. Branch on the exit code, not on message text (table below). With `--json` an
   error is also printed on stdout as an object with status, message, code and
   exit_code.
3. Commands marked destructive (D) delete or cannot be undone. Commands marked
   outward (O) email real people or make email go out later. Both refuse to run
   without `--yes` and exit 9. NEVER add `--yes` until the user has confirmed
   that exact action in this conversation. Before asking, show what will
   happen: the recipients or the audience and count, the subject and the text,
   the campaign or draft id. A yes for one action is not a yes for the next.
4. Exit 9 means "ask the user", not "retry with --yes".
5. Add `--idempotency-key <stable-key>` to any POST you may retry (send, launch,
   reply, create). The same key never repeats the action.
6. Use `--quiet` to get ids, one per line, when chaining:
   `mailbase campaigns list --quiet | head -1`.
7. Lists are paged: pass `--limit` and `--offset` (or `--cursor` where the
   command has it) and keep going while the response says has_more is true.
8. `--data @file.json` (or inline JSON, or `-` for stdin) sends a raw body for
   rich payloads; explicit flags win over it. `--flag @file` reads a file where
   the help says "accepts @file". `none` clears a nullable field.
9. Never use `mailbase api` for something a named command already does. It
   treats every non-GET call as destructive and outward.

## Exit codes

| Code | Meaning | What to do |
| --- | --- | --- |
| 0 | ok | read stdout |
| 1 | unexpected error (or the user said no at a prompt) | report it |
| 2 | usage error, or HTTP 400 | fix the flags; the message lists what is wrong |
| 3 | not signed in, session expired or key invalid | run the login steps in section 2 |
| 4 | not found | check the id with the matching list command |
| 5 | permission denied | tell the user which scope or role is missing |
| 6 | refused by the app's rules or a conflict | read the message; do not work around it |
| 7 | rate limited | wait the printed Retry-After seconds, then retry |
| 8 | network, timeout or server error | retry once, then report |
| 9 | confirmation required | show the action to the user and ask |

## Recipe: find clients (France only)

Prospecting searches the French company register (SIRENE). Steps up to the
drafts contact nobody; approving drafts and resuming the sequence email people.

1. Find activity codes: `mailbase prospecting naf plombier --json`. Show the
   candidates and let the user pick; never invent a code. For a town:
   `mailbase prospecting places Macon --json`.
2. Show the target, then create it after the user agrees:
   `mailbase prospecting targets create --name "Plumbers 71" --naf-codes 43.22A --departments 71 --json`.
3. Watch discovery: `mailbase prospecting progress --json`,
   `mailbase pool facets --target <id> --json`,
   `mailbase pool list --target <id> --status verified --total --json`.
4. Prune with the user: `mailbase pool archive --ids a,b --yes` (or `--all`
   with filters, such as `--target <id> --fit rejected`; it is destructive, so
   confirm first), `mailbase pool restore`, `mailbase pool delete` (archived
   only, permanent), `mailbase pool overrule --ids ...` for companies the AI
   wrongly put aside.
5. Block customers and competitors:
   `mailbase prospecting exclusions import --text @list.txt --dry-run` first.
6. Make the sequence ready: `mailbase prospecting profile set --icp "..." --tone "..."`,
   `mailbase sequence set --daily-cap 20 --signature @signature.txt --call-to-action "..."`.
7. Review: `mailbase drafts list --json`, then with the user's decision
   `mailbase drafts approve <id> --yes` or `mailbase drafts reject <id> --reason "..." --yes`.
   Rewrite with `mailbase drafts regenerate <id> --feedback "shorter"`.
8. Only after explicit approval of the emails: `mailbase sequence resume --yes`.

## Recipe: set up sending

1. `mailbase domains connect mail.acme.com --json`. Show the user the DNS records
   from the response (type, name, value); they publish them at their DNS host.
2. Later: `mailbase domains verify <id> --json` until the status is verified.
   `mailbase domains restart <id>` issues new records if verification failed.
3. `mailbase senders add hello@mail.acme.com --name "Acme" --set-default --json`.
4. Check health: `mailbase domains check mail.acme.com --json`,
   `mailbase sending get --json`, `mailbase deliverability --json`.

Buying a domain, billing and connecting a mailbox (password or Google or
Microsoft consent) happen in the web app, not here.

## Recipe: build and send a campaign

1. Audience: `mailbase audiences list --json`, or create one with
   `mailbase audiences create --name "Beta" --contacts @emails.txt`. Check
   `mailbase audiences preview --rules @rules.json` for rule audiences.
2. Template or content: `mailbase templates list --json`,
   `mailbase templates check --template-id <id> --json`.
3. Create the draft (nothing is sent):
   `mailbase campaigns create --name "Oct" --subject "Hi {{first_name}}" --body-file mail.mjml --audience <audience-id> --json`
   (one variant "A"). For an A/B test pass `--data @campaign.json` with a
   variants array of label, subject and body_mjml objects, plus ab_metric and
   ab_pilot_percent.
4. `mailbase campaigns preflight <id> --json`: merge tags and deliverable count.
5. Test to the user's OWN address only, after their yes:
   `mailbase campaigns test <id> --to me@acme.com --yes`.
6. After the user explicitly confirms the audience, count and subject:
   `mailbase campaigns launch <id> --yes --idempotency-key launch-<id>` or
   `mailbase campaigns schedule <id> --scheduled-for 2026-10-12T08:00:00Z --yes`.
   The CLI sets the API's confirm flag for you.
7. Follow it: `mailbase campaigns job get <id> --json`; pause with
   `job pause`; `job cancel` is destructive.
8. A/B: `mailbase campaigns ab get <id> --json`; if the winner is manual,
   `mailbase campaigns ab winner <id> --variant-id <vid>`; then, after a yes,
   `mailbase campaigns ab rollout <id> --yes`.

## Recipe: triage replies

1. `mailbase threads list --view needs_reply --intent hot --json`, then
   `mailbase threads get <id> --json` (this marks it read).
2. Organise: `mailbase threads notes add <id> --body "..."`,
   `mailbase threads tag <id> --add <tag-id>`,
   `mailbase threads stage <id> --stage meeting`,
   `mailbase threads follow-ups add <id> --title "..." --due-at 2026-10-12T09:00:00Z`.
3. Draft the reply text and show it. Send only after the user approves the exact
   text: `mailbase threads reply <id> --body-text @reply.txt --idempotency-key reply-<id> --yes`.
4. If someone asks to stop: `mailbase suppressions add person@example.com --reason "asked to stop"`.

## Recipe: workflows

1. `mailbase workflows list --json`; create as a draft:
   `mailbase workflows create --data @flow.json --json` (status stays draft).
2. Show the user the trigger, the steps and who it will email. After their yes:
   `mailbase workflows activate <id> --yes`, and for specific people
   `mailbase workflows enroll <id> --emails a@b.co --yes`.
3. `mailbase workflows pause <id>` stops it at any time.

## Limits

Prospecting covers France only (SIRENE). Buying domains, billing, plan changes
and mailbox OAuth are web-app only. Erasing a contact
(`mailbase contacts erase <id> --confirm-email <email> --yes`) is irreversible
and needs the contact's email retyped; do it only on the user's explicit request.
