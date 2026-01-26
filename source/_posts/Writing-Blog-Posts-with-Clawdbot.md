---
title: Publishing and Updating Your Blog Using Clawdbot
date: 2026-01-26 23:00:00
tags:
  - AI
  - Clawdbot
  - automation
  - blogging
excerpt: I asked my AI assistant to help me write this blog post. From my phone. While making coffee. Here's how Clawdbot turns Telegram messages into committed code.
categories: [AI, Productivity, Automation]
index_img: img/clawdbot-blogging/clawdbot-blog.jpeg
banner_img: img/clawdbot-blogging/clawdbot-blog-wide.jpeg
---

# Publishing and updating your blog using Clawdbot

## What is Clawdbot

[Clawdbot](https://github.com/clawdbot/clawdbot) connects Claude to messaging apps like Telegram, WhatsApp, and Discord. It runs on your own server with access to your filesystem, shell, and tools.

Instead of copying code snippets back and forth, I tell it to do things. It reads files, writes files, runs commands, and remembers context across sessions.

## Setting it up

Head to [clawd.bot](https://clawd.bot) and follow the setup guide. You can run it locally with Claude Code or deploy it to a hosted environment depending on your needs. The docs cover both options.

Once running, connect it to Telegram (or your preferred messaging app) and you're ready.

## How I update my blog now

My blog runs on [Hexo](https://hexo.io/) and deploys via GitHub Pages. The old workflow: SSH in, create a markdown file, write, commit, push. Enough friction that I'd put off writing unless I was at my desk.

Now I message Clawdbot on Telegram:

> "Let's write a blog post about using Clawdbot to update my blog"

It checks my existing posts to match the format, drafts something, and I iterate in chat. When it looks good: "commit and push it". Done from my phone.

## How the skills work together

I told Clawdbot I wanted help with my Hexo blog. It figured out what skills it needed and installed them. Skills extend what Clawdbot can do - you can browse available ones at [clawdhub.com](https://clawdhub.com).

For this workflow, it uses three:

**github** - handles git operations. Clone the repo, commit changes, push to GitHub. The blog deploys automatically via GitHub Actions.

**nano-banana-pro** - generates images using Gemini. I asked for a header image and Clawdbot looked at my existing blog images to match the style (pixel art, dark navy background, warm orange glow).

**humanizer** - catches AI-isms in writing. Removes things like "It's important to note that..." that make AI-assisted text obvious.

## Previewing before publishing

I asked to preview the post before publishing. Clawdbot spun up a local Hexo server and created a temporary public URL using Cloudflare Tunnel. I could see exactly how it would look before committing.

## Try it yourself

Clawdbot is open source: [github.com/clawdbot/clawdbot](https://github.com/clawdbot/clawdbot)

You'll need:
- A server (VPS, home machine, Raspberry Pi)
- Telegram bot token (free from @BotFather)
- Anthropic API key

---

*Written from my couch, talking to a bot on Telegram.*
