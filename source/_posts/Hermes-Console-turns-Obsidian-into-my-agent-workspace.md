---
title: How my LLM Wiki became my second brain agent workspace
date: 2026-05-18 19:22:46
tags:
  - ai-agents
  - hermes
  - obsidian
  - workflows
  - developer-tools
excerpt: "My LLM Wiki works because Obsidian holds the structure, Hermes acts on it, and Hermes Console makes the loop native inside the note I am reading."
index_img: /img/llm-wiki-agent-workspace/hero.png
banner_img: /img/llm-wiki-agent-workspace/hero.png
categories:
  - ai
  - developer-tools
---
I keep an LLM Wiki in Obsidian because it gives my work compounding memory.

Brain dumps, project notes, research, backlogs, Kanban boards, decisions, and drafts all live as markdown. Hermes can read that structure, act on it, and write the result back. Obsidian keeps the graph visible.

The useful pattern is simple: I keep the judgment layer human-owned, and let the agent maintain the coverage layer around it.

| Layer | What lives there | Who should touch it |
| --- | --- | --- |
| Human-owned thinking | Brain dumps, taste, decisions, strategy, voice, product judgment | Me first. Hermes can reference it, but should not casually rewrite it. |
| Agent-maintained coverage | Source notes, summaries, entity pages, links, citations, drafts, backlog hygiene | Hermes can ingest, organize, update, and connect it. |
| Shipped artifacts | Blog posts, plans, issues, code changes, cleaned-up notes | We create them together, then feed the context back into the wiki. |

That is the second-brain loop I want: rough input becomes structured context, structured context becomes work, and finished work leaves better context behind.

Hermes Console came from that loop. I wanted the place where I think to also be the place where I ask Hermes to act.

![Hermes Console running inside Obsidian](/img/llm-wiki-agent-workspace/hermes-console-github-screenshot-2026-05-23.png)

<!-- more -->

## The idea clicked in Obsidian

I first saw the Hermes + Obsidian shape in [Greg Isenberg's conversation with Imran Muthuvappa](https://www.youtube.com/watch?v=Qn2c_U-cWQs&t=1457s), where Imran talked about Obsidian working well with Hermes because it is markdown and the agent can organize it.

The LLM Wiki structure comes from [Andrej Karpathy's LLM Wiki pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f): build interlinked markdown so future work starts from accumulated context instead of a blank chat.

Kevin Simback's post on a [modified Karpathy second brain method](https://x.com/KSimback/status/2043745657748361476) gave me the sharper framing: the wiki needs two authors. Human thinking should stay protected. Agent-maintained knowledge should stay fresh.

That distinction is what keeps the system useful. If the agent writes everything, the wiki drifts toward generic prose. If I maintain everything manually, it decays. The durable version is a shared workspace with clear ownership.

![The second brain gets two authors](/img/llm-wiki-agent-workspace/comic-two-authors.png)

## My working loop

A normal idea starts messy.

It might be a paragraph in a brain dump, a saved link, a note under `backlogs/personal-blog.md`, or a card in a Kanban board. The important part is that it is already inside the wiki, close to the surrounding context.

Then Hermes can do the next step:

1. Read the selected note or current backlog item.
2. Inspect the linked context around it.
3. Turn it into a draft, issue, plan, cleanup, or research task.
4. Update the wiki so the next session starts smarter.

![Brain dump to agent memory](/img/llm-wiki-agent-workspace/brain-dump-to-agent-memory.png)

This is why markdown matters. It is boring in the best way. Notes, links, frontmatter, backlogs, logs, and drafts are all plain files. Hermes can reason over them, patch them, move them, and verify them.

Obsidian gives me the human surface. Hermes gives me the action layer.

## What Hermes Console adds

Before Hermes Console, the workflow already worked from a terminal. I could open Hermes in the vault, point it at a file, and ask it to update the wiki.

The missing piece was immediacy.

When I am reading a note and a paragraph becomes the next action, I want to select it, type a prompt, and continue from there without leaving Obsidian.

Hermes Console makes that native:

- terminal tabs inside Obsidian
- Hermes startup from the vault context
- opt-in selected-note or cursor context per tab
- Shift+Enter for multi-line prompts
- busy/idle state per Hermes tab
- unread/background completion indicators
- settings for theme, font, shell, scrollback, and panel location

The most important detail is selected-note context. I can highlight text in Obsidian, turn on `Send context to Hermes`, and submit a prompt. The selected text becomes context for Hermes without being pasted into the terminal input.

![Send context to Hermes from the Obsidian sidebar](/img/llm-wiki-agent-workspace/hermes-console-github-screenshot-2.png)

That changes the feel of the wiki. A note is no longer only something I read. It is something I can act on.

## The bridge is intentionally small

Hermes Console has three pieces:

| Piece | Job |
| --- | --- |
| Obsidian plugin: Hermes Console | Owns the terminal UI, tabs, PTY sessions, note-context capture, settings, and visible status. |
| Bridge file: `.obsidian/hermes/context.json` | Local JSON handoff written inside the active vault. |
| Hermes plugin: `obsidian-context-bridge` | Reads the bridge, injects note context into the Hermes turn, exposes `obsidian_context()`, and writes busy/idle status. |

I like this because each side owns what it understands. Obsidian owns editor state. Hermes owns the agent turn. A local file connects them.

![Hermes Console plugin flow](/img/llm-wiki-agent-workspace/hermes-console-plugin-flow.png)

To use selected-note context, install the Hermes-side bridge once:

```bash
hermes plugins install dannyshmueli/obsidian-hermes-console --enable
```

Hermes Console is now published in Obsidian Community Plugins: [community.obsidian.md/plugins/hermes-console](https://community.obsidian.md/plugins/hermes-console). Install it there, enable it, download the native binaries from plugin settings, and open the console.

## The product lesson

The pattern I would copy into other agent products is this:

1. Keep the user's pointing action native.
2. Pass the current object as context at submit time.
3. Show enough state that the handoff feels trustworthy.

In Obsidian, the pointing action is selected text or cursor location. In a code editor, it might be the current file, diff, symbol, or failing test. In an analytics product, it might be the current chart, cohort, or funnel step.

The interface already knows what the user is pointing at. The agent should receive that context cleanly.

## The takeaway

Hermes Console matters because it makes my LLM Wiki easier to grow.

The wiki gives Hermes durable context. Hermes turns that context into work. Obsidian keeps the structure visible. Hermes Console puts the agent inside the same workspace where the thinking happens.

That is the system I want: a personal knowledge base that improves through use, with an agent sitting inside it.

Install page: [Hermes Console on Obsidian Community Plugins](https://community.obsidian.md/plugins/hermes-console)

Repo: [github.com/dannyshmueli/obsidian-hermes-console](https://github.com/dannyshmueli/obsidian-hermes-console)
