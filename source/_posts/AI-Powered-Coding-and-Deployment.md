---
title: AI Powered Coding and Deployment
date: 2025-07-13 15:39:35
tags:
excerpt: GitHub Issues as Your New IDE. Last week I fixed a bug waiting for my coffee. Not by SSH-ing into a server or opening my laptop. Just by typing @claude fix this on a GitHub issue from my phone.
categories: [cloud, DevOps, Cloudflare, GitHub Actions, Product Management]
index_img: img/ai-powered-coding-deployment/ai-coding-github-actions.jpeg
banner_img: img/ai-powered-coding-deployment/ai-coding-github-actions-wide.jpeg

---

# Building the Ultimate AI-Powered Development Workflow: From Issue to Production in Minutes

*How I built a fully automated development & deployment pipeline where Claude AI can fix bugs and Github actions create preview environments. All from a GitHub issue that I can create and manage from my phone.*

## The Problem: When Product Manager Meets Developer

As a product manager who writes code, I live in two worlds. In one, I'm all over user feedback and prioritizing features. In the other, I'm knee-deep in code. The constant context switching is exhausting and as I want more partners to work on the project I need better tools.

While working on [Canvas Genie](https://canvasgenie.app) (my AI-powered whiteboard tool), I found myself spending hours on repetitive tasks:
- Manually creating branches for bug fixes
- Setting up test environments (locally testing the change)
- Coordinating deployments

That's when it hit me: **What if AI could handle the entire flow from bug report to deployed fix?**

## The Vision: AI as a True Team Member

Imagine this scenario:
![Software Development Lifecylce](img/ai-powered-coding-deployment/scenario.png)

1. Report a bug in a GitHub issue
 ![](img/ai-powered-coding-deployment/report-a-bug-in-github-issues.png)
 **figure:** bug in light mode
1. Tag `@claude` in the issue (*"@claude please help with this"*)
1. Claude investigates, writes the fix on a branch and links to create PR
 ![](img/ai-powered-coding-deployment/claude-listen-fix.png)
 **figure:** Claude listens and responds with progress updates.
1. PR deploys it to a preview isolated environment (server + client)
 ![](img/ai-powered-coding-deployment/pr-deploy.png)
 **figure:** no AI for this step, just Github actions.
1. Verify the fix and merge the PR
1. Merge to main branch deploys to production automatically using Github actions.

## Cost Overview: What You'll Need

Before diving into the technical details, here's the monthly cost breakdown:

**Required Services:**
- **Cloudflare Workers**: Free tier (100,000 requests/day)
- **Cloudflare Pages**: Free tier (500 builds/month)
- **GitHub Actions**: Free tier (2,000 minutes/month)
- **Claude Pro/Max**: $20-200/month

**Total Monthly Cost**: $20-200 (just for Claude subscription)

The free tiers are generous enough for most small to medium projects. You'll only pay for Cloudflare if you exceed the limits.

## The Product Manager's Perspective

### Why This Investment Made Sense

What's the ROI? At 2-3 days of setup time, this investment pays for itself quickly:

1. **Velocity**: Ship fixes in minutes, not hours
2. **Quality**: Every change is tested in isolation
3. **Confidence**: Preview before production in a production-like environment
4. **Scale**: Handle multiple PRs simultaneously without resource conflicts
5. **Cost**: Cloudflare Workers are practically free at my scale

### The Real Impact

Since implementing this system:
- **Bug fix time**: From 2-3 hours to 15 minutes
- **Deployment errors**: Reduced by 90%
- **Context switching**: Eliminated for simple fixes
- **Developer happiness**: Through the roof

### When Automation Pays Off

The break-even point was surprisingly quick:
- Setup time: ~12 hours
- Time saved per deployment: ~1 hour
- Deployments needed to break even: 12
- Actual deployment frequency: 12 per week

We hit ROI in about 1 week.

## The Complete Flow: From Issue to Production

### Prerequisites: Technical Knowledge Required

Before starting, you should be comfortable with:
- GitHub Actions and YAML syntax
- Basic command line operations
- Environment variables and API keys
- Git branching and pull requests

### Prerequisites: The Foundation

**Why These Tools?**
- **Cloudflare Workers**: Serverless backend that scales globally without server management
- **Cloudflare Pages**: Static hosting with automatic preview deployments for every PR
- **GitHub Actions**: Automation workflows that respond to GitHub events
- **Claude Code Action**: AI agent that can read, write, and push code directly to GitHub

**Tech Stack:**
- **Backend**: Cloudflare Workers
- **Frontend**: Cloudflare Pages (static hosting with preview deployments)
- **CI/CD**: GitHub Actions (automation workflows)
- **Package Manager**: pnpm (monorepo support)

**Required Accounts:**
- GitHub account with Actions enabled
- Cloudflare account (free tier works)
- Claude Code OAuth token (Pro/Max Subscription) or Anthropic API key

#### Setting Up Cloudflare API Permissions

You'll need a Cloudflare API token with specific permissions to deploy workers and pages automatically.

![Cloudflare API Token Settings](img/ai-powered-coding-deployment/clouldflare-api-token-setttings.png)

**Required Permissions:**
- **Cloudflare Pages**: Edit permissions (to deploy frontend previews)
- **Workers Scripts**: Edit permissions (to deploy backend services)
- **User Details**: Read permissions (for authentication)

### Setup Claude Code in your Github Actions: 
Follow the instructions from https://github.com/anthropics/claude-code-action 

#### GitHub Actions Permissions

In your repository settings → Actions → General:
- **Actions permissions**: Allow all actions
- **Workflow permissions**: Read and write permissions
- **Allow GitHub Actions to create and approve pull requests**: ✓ Enabled

Required repository secrets:
- `CLAUDE_CODE_OAUTH_TOKEN`: Your Claude Code OAuth token
- `CLOUDFLARE_API_TOKEN`: API token with Workers and Pages permissions
- `YOUR_APP_API_KEY_PRODUCTION`: Your production API keys
- `YOUR_APP_API_KEY_TEST`: Test API keys for preview environments

## Step-by-Step Workflow

### Step 1: User Reports an Issue

A user creates a GitHub issue: "Live Suggestions Button in Light Mode looks bad" + adds screenshots
They simply add: *"@claude can you fix this?"*

### Step 2: Claude Springs into Action

The GitHub Actions workflow detects the @claude mention and triggers the AI agent. This is where the magic begins - Claude analyzes the issue, examines the codebase, and creates a solution.

`.github/workflows/claude.yml`:
```yaml
name: Claude PR Assistant
on:
  issues:
    types: [opened, assigned]
jobs:
  claude-code-action:
    if: contains(github.event.issue.body, '@claude')
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@beta
        with:
          model: "claude-opus-4"
```
 
**⚠️ Critical Configuration**: The `custom_instructions` field is essential! Without explicitly telling Claude to push to a branch, it might only commit changes within the GitHub Actions environment where they're never accessible.

```yaml
branch_prefix: "claude-agent-"
custom_instructions: |
  Use branch prefix: claude-agent-
  Push your changes to the branch and verify the branch is found before done
  I need you to check if the branch is live - no need for PR, just make sure the branch you made and committed all the changes to is there
```

Learn more about setting up Claude Code in GitHub Actions: [Official Documentation](https://docs.anthropic.com/en/docs/claude-code/github-actions) 

### Step 3: Claude Fixes and Creates Branch 

Claude analyzes the codebase, identifies the problem, and creates a fix. Then it pushes the fix to a new branch (e.g., `claude-agent-issue-26`) and comments on the issue with:
* branch link
* create PR link

This gives you full control - you can review the changes before creating the PR.

**Example Workflow:**
1. Claude fixes issue #26, creates branch `claude-agent-issue-26`
2. You create PR from that branch
3. You test and find issues
4. Comment on PR: "@claude the button needs to be centered on mobile"
5. Claude pushes new commits to the same branch
6. PR automatically rebuilds

**Why on PR:** When Claude is triggered on a PR (not an issue), it always works on the existing PR branch. This means:
- First time: @claude on issue → creates new branch
- Subsequent times: @claude on PR → updates same branch

### Step 4: Isolated Preview Environment

Here's where the real power comes in. When you create a PR from Claude's branch, the automated deployment system creates a completely isolated environment for testing.

Each PR gets its own complete environment through the `pr-deploy.yml` workflow:

1. **Deploys a branch-specific backend**: `https://your-app-name-claude-agent-issue-26.cloudflareuser.workers.dev`
2. **Builds the frontend with the branch API**: The client knows to talk to the branch backend
3. **Deploys to Cloudflare Pages**: `https://claude-agent-issue-26.your-app-name.pages.dev`
4. **Comments on the PR** with all the preview URLs

```yaml
- name: Deploy branch-specific Cloudflare Worker
  env:
    CLOUDFLARE_API_TOKEN: ${{ secrets.CLOUDFLARE_API_TOKEN }}
    # Use test API keys for preview environments
    YOUR_APP_API_KEY: ${{ secrets.YOUR_APP_API_KEY_TEST }}
  run: |
    ./deploy-cloudflare-branch.sh "${{ steps.branch.outputs.name }}"

- name: Build client with branch API
  env:
    # Point frontend to the branch-specific backend
    VITE_API_BASE_URL: https://api-your-app-${{ steps.branch.outputs.sanitized }}.your-domain.workers.dev
  run: |
    pnpm build:cloudflare
```

**Key Points:**
- Use separate API keys for test (`YOUR_APP_API_KEY_TEST`) vs production (`YOUR_APP_API_KEY_PRODUCTION`)
- The frontend build gets the branch-specific API URL via environment variable
- This pattern works for any API service (OpenAI, Stripe, OpenRouter, etc.)

**Result:** 
- Isolated backend with test API keys
- Frontend configured to use the branch backend
- No interference with production or other PRs
- Full end-to-end testing capability

### Step 5: Review and Test

With the preview environment ready, you can now test the fix in a production-like environment. The PR comment includes direct links to both the frontend and backend, making it easy to verify the changes work as expected.

### Step 6: Automatic Cleanup

When the PR is merged or closed, `pr-cleanup.yml` automatically:
- Deletes the branch-specific worker
- Updates deployment status
- Posts a cleanup confirmation

No more forgotten test environments eating up resources!

```yaml
# pr-cleanup.yml
- name: Delete Cloudflare Worker
  env:
    CLOUDFLARE_API_TOKEN: ${{ secrets.CLOUDFLARE_API_TOKEN }}
  run: |
    echo "🧹 Cleaning up Cloudflare Worker: ${{ steps.branch.outputs.worker_name }}"
    npx wrangler delete --name "${{ steps.branch.outputs.worker_name }}" --force
    echo "✅ Worker cleanup complete"

- name: Add cleanup comment
  uses: actions/github-script@v7
  with:
    script: |
      const commentBody = [
        '### 🧹 Preview Environment Cleaned Up',
        '',
        `The preview environment for branch \`${branch}\` has been removed.`,
        `- **Worker:** \`${workerName}\` (deleted)`,
        `- **Status:** ${isMerged ? 'Merged' : 'Closed without merging'}`,
        '',
        'Thank you for your contribution! 🎉'
      ].join('\n');
```

![Cleanup Confirmation Comment](/img/ai-powered-coding-deployment/cleanup-done.png)

### Step 7: Production Deployment

Merging to the `main` branch triggers the production deployment:
- Backend deploys to `myproject.myusername.workers.dev`
- Frontend deploys to `www.myapp.com`
- All with zero manual intervention

## Implementation Details

### Branch Name Sanitization

Cloudflare has strict naming requirements, so I sanitize branch names:

```bash
SANITIZED_BRANCH=$(echo "$BRANCH_NAME" | \
  sed 's/[^a-zA-Z0-9-]/-/g' | \
  sed 's/--*/-/g' | \
  sed 's/^-//;s/-$//' | \
  tr '[:upper:]' '[:lower:]' | \
  cut -c1-50)
```

This turns `feature/ADD-NEW-API!!!` into `feature-add-new-api`.

### Environment Isolation

Each environment uses different API keys:
- **Production**: `OPENROUTER_API_KEY_PRODUCTION`
- **PR Previews**: `OPENROUTER_API_KEY_TEST`
- **Local Dev**: `OPENROUTER_API_KEY`

This prevents test environments from affecting production quotas or data.

### Challenges and Solutions

1. **CORS configuration**: Branch preview URLs need special handling
2. **Secret management**: Different keys for different environments
3. **Cleanup automation**: Ensuring resources are properly removed
4. **Iteration workflow**: Remember to use PR comments (not issue comments) for continued work on the same branch

---

*Check out [Canvas Genie](https://app.canvasgenie.app). Have questions? Find me on [X](https://x.com/dannyshmueli)*

## Technical Resources

- [Cloudflare Workers Documentation](https://developers.cloudflare.com/workers/)
- [Claude Code Action](https://github.com/anthropics/claude-code-action)
