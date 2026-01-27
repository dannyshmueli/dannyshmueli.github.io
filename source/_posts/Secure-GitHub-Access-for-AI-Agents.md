---
title: Secure GitHub Access for AI Agents
date: 2026-01-27 19:00:00
tags:
  - AI
  - GitHub
  - security
  - Clawdbot
excerpt: Giving AI agents GitHub access is useful but risky. Here's how I built a safer approach using Personal Access Tokens instead of full OAuth — and why it matters.
categories: [AI, Security, Development]
index_img: img/github-pat.jpeg
banner_img: img/github-pat-wide.jpeg
---

# Secure GitHub Access for AI Agents

## The Problem

I wanted my AI assistant (Cluka, running on Clawdbot) to help with GitHub tasks — cloning repos, pushing code, opening PRs. The existing GitHub integrations all use OAuth, which means granting full account access.

That's a problem.

## Why OAuth Feels Wrong Here

When you OAuth into GitHub with an app, you're typically granting access to *everything*. All your repos, all your organizations, all your secrets. For a human-controlled app, that's usually fine — you trust yourself to use it responsibly.

But AI agents are different:

1. **Prompt injection is real.** A malicious webpage, email, or document could potentially manipulate the agent into doing something unintended.

2. **The blast radius matters.** If something goes wrong, do you want it to affect *all* your repos or just one?

3. **Trust vs. safety.** I trust my AI assistant's intentions. I don't trust that it's invulnerable to attacks.

## The PAT Approach

Personal Access Tokens give you granular control:

- **Repo-specific access** — Fine-grained PATs can be limited to specific repositories
- **Scope control** — Only grant read access if that's all you need
- **Easy revocation** — Compromised? Delete the token, create a new one
- **User controls security** — Not the app, not the AI

## How It Works

I built a simple skill for Clawdbot called `github-pat`:

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

The key insight: the token is provided by the user, stored locally, and determines what the agent can access. The agent doesn't ask for more — it works with what it's given.

## My Setup

For my AI assistant Cluka, I:

1. Created a PAT with `repo` scope
2. Invited the agent's GitHub account as a collaborator on specific repos
3. Stored the token in the agent's local config

Now Cluka can help with those repos — and *only* those repos. If I want to give access to another project, I add it explicitly. The default is no access, not full access.

## The Workflow

Here's what it looks like in practice:

**Me:** "Create a branch and push the new blog post"

**Cluka:** 
```
🌿 Creating branch: cluka/new-post
📦 Staging changes...
💾 Committing: Add blog post about GitHub PAT security
🚀 Pushing to cluka/new-post
✅ Done!
```

**Me:** "Open a PR"

**Cluka:**
```
📝 Creating PR: Add blog post
✅ PR created: https://github.com/user/repo/pull/42
```

No OAuth dance. No "grant access to all repositories" popup. Just a token that does exactly what I configured it to do.

## Get the Skill

The skill is available on ClawdHub:

```bash
clawdhub install github-token
```

Or check it out at [clawdhub.com/skills/github-token](https://clawdhub.com/skills/github-token).

## Final Thought

The question isn't "do you trust your AI assistant?" — it's "what's the blast radius if something goes wrong?"

Principle of least privilege isn't about distrust. It's about building systems that fail safely.

---

*This post was written with help from Cluka, my AI assistant, and pushed to GitHub using the very skill it describes. Meta, I know.* 🐱🦞
