# Mailbase for Claude Code

Official [Mailbase](https://mailbase.studio) plugin marketplace for Claude Code. It adds the Mailbase
tools (via the Mailbase MCP server) and a set of workflow skills and slash commands, so an agent can
find clients, run campaigns, triage replies, set up sending domains and automate follow-ups in your
Mailbase workspace. **Nothing is sent without your explicit approval.**

## Install

In Claude Code:

```
/plugin marketplace add amintt2/mailbase-plugins
/plugin install mailbase@mailbase-plugins
```

Or from your shell:

```sh
claude plugin marketplace add amintt2/mailbase-plugins
claude plugin install mailbase@mailbase-plugins
```

Then connect your workspace: run `/mcp`, pick the **mailbase** server,
and sign in. A browser window opens; choose the one workspace you want Claude to work in and approve.
To use another workspace later, authenticate again and pick it. You need a Mailbase account
(https://mailbase.studio).

Update with `/plugin marketplace update mailbase-plugins`.

## Slash commands

| Command | What it does |
| --- | --- |
| `/mailbase:campaign <goal, e.g. 'announce our new reporting feature to active customers'>` | Plan, draft, test and (after your yes) send an email campaign in Mailbase |
| `/mailbase:prospect <who you sell to, e.g. 'accounting firms in Lyon with 10-50 staff'>` | Find new customers (French companies) for what you sell and review the outreach Mailbase drafts for them |
| `/mailbase:review-drafts [target or company name]` | Review the outreach emails Mailbase drafted for your prospects, one by one, and approve, edit or reject each |
| `/mailbase:setup-domain <domain, e.g. mail.example.com>` | Connect a sending domain to Mailbase, show the DNS records to publish, verify it and add a sender address |
| `/mailbase:status` | Mailbase status: workspace, replies needing attention, campaigns in flight, prospecting counts and deliverability warnings |
| `/mailbase:triage [hot | warm | objection | ooo | contact name]` | Work through the Mailbase reply inbox: what needs attention, notes and next steps, drafted replies for approval |

## Skills

Claude picks these up automatically from what you ask; `get-started` is the overview.

| Skill | Use |
| --- | --- |
| `audiences-and-contacts` | Use when someone works on people data in Mailbase: build segments or static audiences, import or update contacts, handle unsubscribes and suppressions, set up signup forms and topics, or process a GDPR erasure request. Includes the rules for consent and deletion. |
| `automate-workflows` | Use when someone wants an automated email sequence or journey in Mailbase (welcome series, nurture, follow-up after a tag or signup): design the workflow as a draft, review it, then activate or enroll contacts only after explicit approval. |
| `find-clients` | Use when someone wants to find new customers or prospects in Mailbase: turn an ideal-customer brief into French company targets (SIRENE register, NAF codes), manage the prospect pool, set the business profile and sequence, review the outreach emails drafted for each company, and only then switch sending on. France only. |
| `get-started` | Use for anything in Mailbase, the email workflow app (campaigns, reply inbox, contacts and audiences, templates, workflows, sending domains, prospecting). Start here to learn what the Mailbase tools can do, which ones email real people or delete data, and which workflow skill to follow next. |
| `launch-campaign` | Use when someone wants to write, test and send an email campaign in Mailbase, run an A/B test, or read campaign results. Covers audience, template, draft with variants, preflight, a test to the user, launch or schedule after an explicit yes, send progress and the A/B winner rollout. |
| `mailbase-cli` | Use when the user wants to work in Mailbase (the email workflow app: campaigns, reply inbox, contacts, templates, workflows, sending domains, French prospecting) from the shell, scripts or CI, or when the mailbase command is installed, or when no Mailbase MCP connection is available. Drives the mailbase CLI: install, login check, command discovery, JSON output, confirmation rules and recipes. If the Mailbase MCP tools are connected, prefer them over this skill. |
| `setup-sending` | Use when someone needs to get Mailbase ready to send or is hitting deliverability problems: connect a sending domain, publish and verify DNS records, add sender addresses, connect mailboxes, set sending limits and read the deliverability report. |
| `triage-replies` | Use when someone wants to work through their Mailbase reply inbox: see which replies need attention by intent, read threads, add notes, tags, a pipeline stage or follow-ups, and draft a reply for approval. Never sends a reply without explicit approval. |

## Safety

Anything that emails a real person (test sends, campaigns, replies, approved prospecting emails,
workflows) or deletes data needs your explicit yes in the conversation, for that exact action.
Mailbase also applies its own checks: suppressed addresses are skipped, daily caps and quotas apply
and scam screening can block a message. Passwords and OAuth consent for mailboxes stay in the
Mailbase web app, never in the chat. Prospecting covers French companies (SIRENE register) only.

## Prefer a terminal?

The `mailbase` CLI covers the REST API with `--json` output and `--yes` / `--no-input` for scripts
and agents: `npm i -g mailbase` (see https://mailbase.studio).

Support: support@mailbase.studio - License: MIT
