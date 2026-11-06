---
title: Agents read a Cloudflare mailbox over MCP
date: 2026-11-06 18:30:00 +0200
tags:
  - ai-agents
  - cloudflare
  - email
  - mcp
  - open-source
  - developer-tools
categories:
  - ai
  - developer-tools
excerpt: "Agent Post Office gives my agents their own inbox on a domain I control. They poll, read, and mark mail through MCP, and spam stays labeled in the mailbox."
index_img: /img/agent-post-office-mailbox-mcp-spam/hero.jpg
banner_img: /img/agent-post-office-mailbox-mcp-spam/hero.jpg
og_img: /img/agent-post-office-mailbox-mcp-spam/hero.jpg
---

[Agent Post Office](https://agentpostoffice.com/) gives my agents a mailbox on a domain I control. Inbound mail hits Cloudflare Email Routing. A Worker stores and parses it with D1, R2, and Queues. Outbound mail leaves through Email Sending. The same Worker serves REST, a CLI, and `POST /mcp`.

Agents poll that inbox, open a message, and mark it with tools on the Worker.

<!-- more -->

## Read and mark over MCP

![An agent uses Agent Post Office MCP to read mail, inspect labels, and mark spam.](/img/agent-post-office-mailbox-mcp-spam/mcp-workflow.jpg)

1. `poll_messages` reads the feed. Pass `folder` as `inbox`, `spam`, or `all`.
2. `get_message` returns the folder, spam verdict, score, and reasons. The default body is untrusted plain text. Ask for HTML if you need it.
3. `mark_spam` and `mark_not_spam` record the decision. Optional flags can block or allow a sender or domain.

Sending and replying are separate tools with their own scopes.

I use this MCP surface from Grok Bot on live Agent Post Office inboxes. It is also useful for Muse and Instinct.

## Spam landed in the mailboxes

Four real spam emails arrived: an SEO audit offer, a downtime-outreach pitch, and a fake email verification. All four passed DKIM and DMARC. Email Routing reports SPF, DKIM, and DMARC results. The Worker scores the mail.

## The mail stays visible

![Mail moves into the Worker, gets a score, and receives a spam tag. The message is kept.](/img/agent-post-office-mailbox-mcp-spam/spam-flow.jpg)

The live path runs in tag mode. It labels spam and keeps the message. When I mark a message as spam, it moves to the spam folder, including when automatic mode is only tagging.

Marks also feed block and allow lists and a per-inbox Bayes layer. Bayes turns on after 20 spam marks and 20 not-spam marks, needs 90% confidence, and is capped at 52 points. Fresh inboxes have no trained Bayes score yet.

Scoring is additive and capped at 100. The default threshold is 60, set with `SPAM_THRESHOLD` or per inbox. Per-inbox modes are `tag`, `quarantine`, or `off`. DMARC failure adds 40, SPF failure adds 20, and DKIM failure adds 20. Each content rule is capped at 19 points. I wrote those rules from spam the mailbox received.

On three instances, one smoke test scored a spam message at 93 and a normal message at 0.

The scorer is [Agent-Post-Office/agent-post-office-spam-filter](https://github.com/Agent-Post-Office/agent-post-office-spam-filter), Apache-2.0, from v0.1.0.
