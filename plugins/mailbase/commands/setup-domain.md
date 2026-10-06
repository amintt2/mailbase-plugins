---
description: "Connect a sending domain to Mailbase, show the DNS records to publish, verify it and add a sender address"
argument-hint: "<domain, e.g. mail.example.com>"
---

Follow the `setup-sending` skill for the domain: $ARGUMENTS

If no domain was given, ask which one. Say before you call anything that
reaches DNS (`check_domain_dns`, `connect_domain`, `verify_domain`). Show the
DNS records exactly as returned, wait for me to say they are published, then
verify. You cannot buy a domain or connect a mailbox from here: those happen in
the Mailbase web app, and I never type passwords into the chat.
