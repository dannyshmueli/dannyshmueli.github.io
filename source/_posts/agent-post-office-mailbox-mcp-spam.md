---
title: Giving my agents a mailbox with Agent Post Office.
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
excerpt: "I built Agent Post Office so my agents could use email on a domain I control. Through MCP they check an inbox, read messages, and manage mail, and I find it useful with Muse, Grok Bot, and Instinct."
index_img: /img/agent-post-office-mailbox-mcp-spam/hero.jpg
banner_img: /img/agent-post-office-mailbox-mcp-spam/hero.jpg
og_img: /img/agent-post-office-mailbox-mcp-spam/hero.jpg
---

I built [Agent Post Office](https://agentpostoffice.com/) so my agents could use email on a domain I control. Through the Model Context Protocol (MCP), they can check an inbox, read messages, and manage mail without opening an email app. I find it useful with Muse, Grok Bot, and Instinct.

APO runs on Cloudflare. Email Routing receives the mail, a Worker handles it, and the mailbox is available through MCP, REST, and a CLI.

<!-- more -->

## Email inside the agent’s workflow

![An agent uses Agent Post Office MCP to read mail, inspect labels, and mark spam.](/img/agent-post-office-mailbox-mcp-spam/mcp-workflow.jpg)

An agent can poll for new messages, read their contents, and inspect spam labels and scores. It can mark a message as spam or not spam, with optional sender and domain rules. Sending and replying are separate tools with their own scopes.

That gives me a mailbox I can work with from the agent I’m already using.

## Then spam started arriving

![Mail moves into the Worker, gets a score, and receives a spam tag. The message is kept.](/img/agent-post-office-mailbox-mcp-spam/spam-flow.jpg)

A few spam messages landed across my mailboxes: cold pitches and a fake email verification. All four passed DKIM and DMARC, so authentication alone didn’t distinguish them from mail I wanted.

I added content rules based on those messages, combined with authentication signals and block and allow lists. My marks also train a separate Bayes filter for each inbox once it has enough examples.

I run it in tag mode: suspected spam gets labeled, not deleted. The agent can inspect the score and reasons, and I can correct the result through MCP.

The [spam filter is open source](https://github.com/Agent-Post-Office/agent-post-office-spam-filter) under Apache-2.0.
