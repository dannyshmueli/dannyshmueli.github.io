---
title: Secure GitHub Access for Moltbot 🦞 (formerly Clawdbot)
date: 2026-01-27 19:00:00
tags:
  - AI
  - GitHub
  - security
  - Moltbot
  - Clawdbot
excerpt: Moltbot is trending and everyone's giving it full GitHub access. Here's a safer approach using Personal Access Tokens — and why it matters.
categories: [AI, Security, Development]
index_img: img/github-pat.jpeg
banner_img: img/github-pat-wide.jpeg
---

# Secure GitHub Access for Moltbot 🦞

## The Problem

[Moltbot](https://github.com/moltbot/moltbot) 🦞 (formerly Clawdbot) is trending. People are connecting it to everything — including GitHub. And the default approach uses OAuth via `gh auth login`, which means granting full account access.

If you're running Moltbot 🦞 with GitHub access, you've probably run through an OAuth flow that grants access to all repositories. That's a problem.

## The Existing GitHub Skill

The creator of Moltbot 🦞 published a great [GitHub skill](https://clawdhub.com/steipete/github) that uses the `gh` CLI. It's powerful — you get `gh issue`, `gh pr`, `gh run`, and `gh api` for everything GitHub offers.

But it requires `gh auth login`, which means:
- OAuth flow granting broad access
- All-or-nothing permissions
- Harder to scope down

For many use cases, that's fine. But I wanted something more locked down.

## Why I Built a PAT-Based Alternative

AI agents like Moltbot 🦞 are different from regular apps:

1. **Prompt injection is real.** A malicious webpage, email, or document could potentially manipulate the agent into doing something unintended.

2. **The blast radius matters.** If something goes wrong, do you want it to affect *all* your repos or just one?

3. **Trust vs. safety.** I trust my AI assistant's intentions. I don't trust that it's invulnerable to attacks.

## The PAT Approach

Personal Access Tokens give you granular control:

- **Repo-specific access** — Fine-grained PATs can be limited to specific repositories
- **Scope control** — Only grant read access if that's all you need
- **Easy revocation** — Compromised? Delete the token, create a new one
- **User controls security** — Not the app, not the AI

## A Moltbot 🦞 Skill for Safer GitHub Access

I built `github-token` for this:

```bash
# List repos you have access to
gh.py repos --token YOUR_PAT

# Clone a repo
gh.py clone owner/repo

# Create a branch, make changes, push
gh.py branch feature/my-change
gh.py push "commit message"

# Open a PR
gh.py pr "PR title" --body "description"
```

The key insight: the token is provided by you, stored locally, and determines what Moltbot 🦞 can access. The agent doesn't ask for more — it works with what it's given.

## How to Install

Ask your Moltbot 🦞:

> "install this skill: https://clawdhub.com/dannyshmueli/github-token"

It'll install the skill automatically. Then:

1. **Create a PAT** at [github.com/settings/tokens](https://github.com/settings/tokens)
2. **Select minimal scopes** — `repo` for full access, or use fine-grained tokens for specific repos
3. **Give Moltbot 🦞 the token** — it stores it locally in your config

Now your Moltbot 🦞 can help with GitHub — but only the repos you explicitly allow.

## Which Skill Should You Use?

**Use [steipete/github](https://clawdhub.com/steipete/github) if:**
- You want full `gh` CLI power
- You're comfortable with OAuth access
- You need advanced queries with `gh api`

**Use [github-token](https://clawdhub.com/dannyshmueli/github-token) if:**
- You want tighter access control
- You prefer PAT-based auth
- You want to limit blast radius

Both are valid — it depends on your threat model.

## Final Thought

If you're running Moltbot 🦞 with GitHub access, ask yourself: what's the blast radius if something goes wrong?

Principle of least privilege isn't about distrusting your AI. It's about building systems that fail safely.

---

*This post was written with help from my AI assistant and pushed to GitHub using the very skill it describes.* 🤖
