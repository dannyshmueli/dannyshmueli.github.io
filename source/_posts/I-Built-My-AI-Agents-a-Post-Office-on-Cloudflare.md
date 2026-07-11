---
title: I built my AI agents a post office on Cloudflare
date: 2026-07-11 18:48:06
tags:
  - ai-agents
  - cloudflare
  - email
  - open-source
  - developer-tools
excerpt: "Agent Post Office gives AI agents durable email addresses, inboxes, sending, and replies inside infrastructure I already use: Cloudflare."
index_img: /img/agent-post-office/how-it-works.webp
banner_img: /img/agent-post-office/how-it-works.webp
categories:
  - ai
  - developer-tools
---

AI agents can browse websites, run code, call APIs, and work for hours. Then a normal workflow asks them to receive an email and everything gets awkward.

Verification links, support requests, reports, customer replies, and asynchronous handoffs still arrive through email. Borrowing my inbox means sharing a human identity, exposing unrelated mail, and building brittle forwarding rules. Using another hosted service adds another account, credential, bill, and control plane.

That is the problem [Agent Post Office](https://agentpostoffice.com/) solves: it gives agents their own durable mailboxes on a domain I control, with an API, CLI, and MCP interface they can actually operate.

<!-- more -->

## Why I built it on Cloudflare

I already use Cloudflare for infrastructure. Workers, D1, R2, Queues, DNS, and browser automation are familiar parts of how I build and run products.

Email Routing and Email Sending made the next step obvious: use the same account as an agent-native post office.

The result is self-hosted inside the operator's Cloudflare account. There is no Agent Post Office account, hosted database, or message relay outside the deployment. The operator owns the domain, Worker, database, objects, queues, tokens, and message data.

| What the agent needs | Agent Post Office provides |
| --- | --- |
| A stable email identity | Multiple logical mailboxes on your own domain |
| A way to notice new mail | A pull-based feed the agent can poll on a schedule |
| Safe, explicit processing | Messages stay unprocessed until the agent acknowledges them |
| Sending and replies | Transactional send and threaded reply operations |
| Native agent access | REST API, local CLI, and bounded MCP tools |
| Operator control | Deployment and data stay in your Cloudflare account |

## How the post office works

Inbound mail enters through Cloudflare Email Routing. A Worker validates the envelope recipient against active mailboxes, stores the exact raw message in private R2, records state in D1, and puts an ID-only parsing task onto a Queue.

The Queue consumer parses the MIME message, stores full bodies and attachments privately, and writes a bounded plain-text excerpt into D1. An agent can then poll across all its mailboxes, inspect a message, handle it, and explicitly mark it processed.

Outbound mail and replies leave through Cloudflare Email Sending.

One Worker exposes all three agent surfaces over the same contract:

- REST for any runtime
- a credential-aware local CLI
- MCP tools for agents that speak MCP

This is deliberately pull-based. Scheduled and interactive agents can fetch work when they are ready. There are no webhooks or public callback endpoints to keep alive.

## Email is hostile input

An email inbox is also a prompt-injection inbox.

Subjects, bodies, headers, links, and attachments are all treated as untrusted content. The MCP server labels outputs as untrusted, omits HTML by default, returns attachment metadata without opening files, and requires an explicit save operation for raw messages or attachment bytes.

The service also keeps destructive actions narrow. Deletion requires explicit message IDs. Sending and replying require idempotency keys. Tokens are scoped, D1 stores only their SHA-256 digests, and the raw credential stays on the client side.

These boundaries do not make email safe. They stop the integration from pretending email is safe.

## The agent installs it

The normal setup path is agent-first. Sign in to Wrangler, then give a coding agent this prompt:

```text
Install Agent Post Office from https://github.com/Agent-Post-Office/agentpostoffice-cloudflare for <your-domain>. Create mailboxes <your-mailboxes>. Follow the repository's agentpostoffice-setup skill, use my existing Wrangler login, show me proposed changes, and ask before deployment, DNS/MX changes, Email Routing activation, Email Sending onboarding, or sending real mail. Do not ask me to paste API tokens into chat.
```

The approval boundaries matter. Changing MX records can replace an existing mail provider. Outbound onboarding can affect DKIM and DMARC. The setup skill is designed to inspect first, show the proposed changes, and stop before live infrastructure changes.

A dedicated mail subdomain is the safer default when the apex domain already receives human email. Installation requires a domain on Cloudflare DNS and Workers Paid, currently starting at $5 per month, because arbitrary-recipient sending uses Cloudflare Email Sending.

## What works today

The developer preview supports:

- creating and disabling mailboxes
- receiving, parsing, polling, and acknowledging messages
- downloading raw mail and attachments through authenticated endpoints
- sending new transactional messages
- replying with preserved threading headers
- CLI, REST, and MCP access

The implementation is covered by Node tests and Cloudflare workerd integration tests using local D1 and R2 bindings. The live receive, send, reply, authentication, and safe-download happy paths have also been exercised.

It is still a developer preview. The full failure-injection and deliverability matrix is not complete. Cloudflare accepting an outbound message is not proof that it reached the destination. I would not use it as the only copy of business-critical mail yet.

## The bigger idea

Agents need more than tools that work while a terminal session is open. They need durable identities and asynchronous places where work can wait for them.

Email already connects support systems, vendors, users, reports, alerts, and verification flows. Agent Post Office turns that existing protocol into an agent-native queue while keeping the human in control of the infrastructure.

I did not want another inbox product. I wanted my agents to have an address, a safe polling loop, and a post office that already lives where the rest of my infrastructure lives.

See [Agent Post Office](https://agentpostoffice.com/) for the product overview, or [inspect the open-source repository](https://github.com/Agent-Post-Office/agentpostoffice-cloudflare).
