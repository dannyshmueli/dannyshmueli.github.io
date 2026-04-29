---
title: How I’m using Matt Pocock’s skills inside Hermes to run sub-agents on multi-surface features
date: 2026-04-29 20:20:00
tags:
  - ai-agents
  - hermes
  - developer-tools
  - workflows
excerpt: "My current Hermes workflow: use Matt Pocock’s skills repo inside Hermes to turn a feature into a plan, PRD, and issue files, then run the implementation with Hermes sub-agents."
index_img: /img/matt-pocock-hermes-workflow/hero.png
banner_img: /img/matt-pocock-hermes-workflow/hero.png
categories:
  - ai
  - developer-tools
---

Matt Pocock’s skills repo has been getting a lot of attention lately. If you have not checked it out yet, you should. It is a workflow layer for making you and your agents think through the work before they touch code. Think in alignment first.

My current flow is: run Matt’s skills inside Hermes, then use [Hermes sub-agents](https://hermes-agent.nousresearch.com/docs/user-guide/features/delegation) to execute the output: plan -> PRD -> issue files -> Hermes implement.

Matt’s repo is here: [github.com/mattpocock/skills](https://github.com/mattpocock/skills)

He also posted his AI coding workflow in the workshop here: [x.com/aiDotEngineer/status/2047704667967381811](https://x.com/aiDotEngineer/status/2047704667967381811)
or on YouTube: [youtube.com/watch?v=-QFHIoCo-Ko](https://youtu.be/-QFHIoCo-Ko?si=HiX-OgaitZrNv9ok)

In the workshop, Matt is running the implementation with Sandcastle, his tool for orchestrating sandboxed coding agents:

[github.com/mattpocock/sandcastle](https://github.com/mattpocock/sandcastle)

But since Hermes [supports sub-agents](https://hermes-agent.nousresearch.com/docs/user-guide/features/delegation) out-of-the-box, it can be perfect for the implementation.

<!-- more -->

![Pixel-art workflow diagram showing PLAN, GRILL, PRD, ISSUES, Hermes, and parallel sub-agents.](/img/matt-pocock-hermes-workflow/workflow-diagram.png)

**When to use it:** You already have a project, and you want to add one feature or refactor one area that touches multiple surfaces. Backend, frontend, docs, landing page, tests, maybe migrations too.

This is not a “build the whole project” workflow. I also do not think it replaces product or engineering judgment. It is a way to make a bounded feature change smoother.

## TL;DR

Use Matt’s skills inside Hermes to turn a feature into executable steps, then use Hermes where Matt uses Sandcastle: run the implementation with sub-agents.

1. Hermes creates a local plan.
2. Matt `grill-me` / `grill-with-docs` pressure-tests the plan.
3. Matt `to-prd` turns the plan into a PRD.
4. Matt `to-issues` turns the PRD into local issue files.
5. You check that the issues are vertical slices.
6. Hermes implements those issues with sub-agents.

## 1. Ask Hermes for the first plan

Start in Hermes and ask it to create a markdown plan file.

Example:

```text
I want to add feature X to project A.
Create a local markdown plan at ~/projectA/big-feature-x.md.
Cover scope, surfaces touched, risks, rough implementation outline, and open questions.
Do not code yet.
```

The goal is not a perfect plan. It is just the first concrete artifact.

## 2. Use Matt’s “grill me” skill on the plan

Then run Matt’s [`grill-me`](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md) or [`grill-with-docs`](https://github.com/mattpocock/skills/blob/main/skills/engineering/grill-with-docs/SKILL.md) skill on that plan.

This is where the plan gets much better.

Ask it to interrogate the plan. Make it ask hard questions. Answer at least 8. If the questions start drifting, tell it to refocus and ask harder questions about the feature, architecture, edge cases, product intent, and what could go wrong.

The important part: it should update the same markdown plan as you answer, or at the end.

This step is annoying in a good way. It catches fuzzy thinking before the agents touch code. I got to question 50 once, then told it to wrap it up or speed it up. That also works.

## 3. Use Matt’s `to-prd` skill

Once the plan feels grounded, use Matt’s [`to-prd`](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-prd/SKILL.md) skill.

Example:

```text
/to-prd on ~/projectA/big-feature-x.md, create local md PRD not in github
```

Now you have a Matt-style PRD generated from the plan.

## 4. Use Matt’s `to-issues` skill

Then use Matt’s [`to-issues`](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-issues/SKILL.md) skill on the PRD.

Example:

```text
/to-issues on ~/projectA/big-feature-x-prd.md, create local md issues not in github
```

This is the point where you should check the shape of the work. The issues need to be vertical enough.

Bad issue split:

```text
backend issue
frontend issue
docs issue
tests issue
```

Better issue split:

```text
Issue 1: add the smallest backend path and connect one frontend entry point
Issue 2: wire the user-facing flow and error state
Issue 3: update docs and landing copy for the new behavior
Issue 4: add verification, tests, and cleanup
```

If the split is too horizontal, ask Hermes:

```text
Use the PRD and rewrite the issues as vertical slices as much as possible.
```

## 5. Run the issues through Hermes sub-agents

Now give Hermes the issue files and ask it to run them through sub-agents.

Each sub-agent should get one issue, clear allowed files, acceptance criteria, and a stop condition.

Tell Hermes:

```text
Implement the plan from these issue files using sub-agents.
Keep each sub-agent scoped to one issue.
```

## One config change that helped

I also hit this at first:

```text
[subagent-0] Iteration budget exhausted (50/50)
Reached maximum iterations (50)
```

This helped:

```bash
hermes config set delegation.max_iterations 100
hermes config set delegation.child_timeout_seconds 1200
```

Then restart Hermes chat.

The extra budget gave the sub-agents enough room to finish all the issues cleanly.

This still needs human judgment. You have to read the plan at least at a high level, go through the grilling session, review or reject bad slices, and catch AI slop. Running the grilling twice can help. It does find more important issues. It will not produce perfect design or text copy, but it is great at the technical parts.

Plan first. Get grilled. Turn the plan into a PRD. Turn the PRD into issues. Make the issues vertical. Then delegate.

All of that happens inside Hermes.

Hermes becomes the orchestrator. Matt’s skills create the planning pressure. The sub-agents do bounded work.

I am still early with this, but this is the first setup where multi-surface autonomous agent work did not immediately turn into a mess.
