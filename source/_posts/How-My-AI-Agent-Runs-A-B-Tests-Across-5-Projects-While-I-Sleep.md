---
title: How My AI Agent Runs A/B Tests Across 5 Projects While I Sleep
date: 2026-03-16 16:30:00
tags:
  - ai
  - analytics
  - experimentation
  - ab-testing
  - product-management
excerpt: I do not have the patience to open five dashboards every morning and decide what to test next. So I built a setup where my AI agent proposes experiments, wires variants into the page, checks conversion results, and tells me what won.
categories: [ai, product-management, growth]
---

I build and ship a lot.

Some of it is work stuff. Some of it is side projects. Some of it is tiny experiments that exist because I wanted to test an idea before lunch.

That sounds fun, and it is. But there is a predictable problem that shows up right after shipping:

**nobody wants to babysit analytics across five different projects.**

Not me. Definitely not every morning.

And once A/B tests enter the picture, it gets worse. Now I need to remember what I changed, whether the goal event is tracked correctly, which variant is live, and whether the test has enough data to mean anything.

So I stopped treating experiments like a dashboard task.

I made them part of my agent workflow.

<!-- more -->

## The old loop

The usual experiment workflow looks something like this:

1. Think of something to test
2. Open the app
3. Open analytics
4. Open the experiment tool
5. Create a test
6. Add tracking
7. QA the variants
8. Forget to check the results for three days
9. Open the dashboard again and try to remember what "homepage_test_4" means

This is exactly the kind of workflow AI agents should take over.

Agents are already good at repetitive setup, naming things consistently, editing markup, checking URLs, and reporting results back in plain English.

What they usually cannot do well is live inside a human dashboard.

That was the main design constraint behind [Agent Analytics](https://github.com/Agent-Analytics/agent-analytics): **make analytics readable and actionable for agents, not just humans.**

## What my agent actually does

The workflow is simple on purpose.

When I want to test something, I do not open an experimentation UI first. I tell the agent what I want to improve.

Something like:

> Test the signup CTA on the homepage. Keep the scope narrow. Use signup as the goal event.

From there, the agent handles the annoying parts:

- suggests a single element to test, like a CTA or headline
- checks that the goal event already exists and is named consistently
- creates an experiment name in snake_case
- wires the variant into the page markup
- gives me forced-variant URLs so I can QA both versions
- later checks the results and recommends whether to keep running, pause, or ship the winner

That is the important part: **the agent is not just reading charts. It is operating the full experiment loop.**

## Why this works better than a dashboard for me

Dashboards are fine when you have one product and a dedicated routine.

They break down when you have multiple projects, uneven traffic, and a brain that wants to move on to the next thing immediately after shipping.

An agent changes the interface.

Instead of this:

- log in
- find the right project
- remember what you were testing
- compare the numbers yourself
- decide what to do next

I get this:

> signup_cta is still early. control has 312 exposures, new_cta has 298, and signup is too low to call yet. check again tomorrow.

Or:

> pricing_headline has enough data. new_cta improved signup by 14 percent. complete the experiment with new_cta as the winner.

That is the interface I actually want.

## The implementation is intentionally boring

The key choice I made was to keep the common case declarative.

If the UI is simple, the agent does not need to write custom JavaScript to run an experiment. It can use HTML attributes.

A headline test can look like this:

```html
<h1
  data-aa-experiment="signup_cta"
  data-aa-variant-new_cta="Start free today"
>
  Start your free trial
</h1>
```

The original content is the control.

The variant content lives right in the markup.

That matters because agents are much more reliable when the experiment is visible in the HTML instead of hidden inside a pile of custom logic.

The rules are easy to explain:

- keep the experiment focused on one element
- use a real business event as the goal
- do not use page_view unless the page view is the actual outcome
- make sure the goal event is tracked before the experiment starts
- QA both forced variants before sending traffic through the test

These are basic experimentation rules, but agents benefit from explicit constraints even more than humans do.

## Forcing both variants is one of the best parts

One small detail I love: I can force a variant from the URL.

That means the agent can verify each version locally without waiting for assignment logic or guessing whether the experiment is working.

The URLs look like this:

- `?aa_variant_signup_cta=control`
- `?aa_variant_signup_cta=new_cta`

This turns QA into a real workflow instead of a vibe.

My agent can open both versions, confirm the copy changed, click through the flow, and verify the goal event still fires.

That alone removes a surprising amount of experiment anxiety.

## The real unlock is across multiple projects

If you only run one site, this might sound like overkill.

If you run several projects, this becomes incredibly practical.

I do not want five separate mental models for experimentation.

I want one pattern:

1. pick one page element
2. pick one goal event
3. let the agent wire it up
4. let the agent check the results
5. ship the winner and move on

Once that pattern is stable, the agent can reuse it everywhere.

The same workflow works for:

- homepage headlines
- pricing page copy
- signup button text
- onboarding prompts
- support CTA wording
- simple flow variations tied to real conversion events

And because the agent can read the stats through an API instead of a browser login, it can do the follow-up work without me remembering to ask.

## What I learned building this

A few things became obvious pretty quickly.

### 1. Narrow tests are much easier for agents to run well

If you ask an agent to test layout, offer, copy, and flow all at once, you get messy results.

If you ask it to test one CTA against one goal event, it does great.

### 2. Naming matters more than you think

`signup_cta` is good.

`homepage_test_4` is bad.

When the agent reports results back later, clear naming is the difference between useful memory and archaeology.

### 3. Goal events matter more than exposures

This sounds obvious, but it is easy to drift into vanity metrics.

A variant getting more views does not make it better.

The business question is whether it improved signup, checkout, activation, or whatever real outcome you chose.

### 4. The agent should help decide what to do next

The valuable output is not a chart.

The valuable output is a recommendation:

- keep running
- pause
- complete with a winner
- start the next focused test

That is the part that actually compounds.

## This is the bigger thing I want from AI tools

The exciting part is not that an agent can write copy variants.

The exciting part is that it can close loops.

A useful agent workflow is not:

> here is a dashboard, good luck

A useful agent workflow is:

> I added the experiment, verified both variants, checked the goal event, and I will tell you when there is enough data to make a decision.

That is a very different relationship with analytics.

Less reporting. More delegation.

## If you want to try this pattern

Start smaller than you think.

Pick one page.
Pick one element.
Pick one real conversion event.

Then ask your agent to:

1. confirm the event is already tracked
2. create a clearly named experiment
3. wire the control and one variant into the markup
4. QA both forced variants
5. check the results later and recommend an action

That is enough to get the loop working.

Once the loop works on one site, it is easy to repeat across five.

And that is the whole point.

I do not need more dashboards.

I need my agent to remember what I wanted to learn, run the experiment cleanly, and tell me what happened while I was off building the next thing.

---

If you are curious about the implementation details, I wrote up the agent-facing A/B workflow in the Agent Analytics docs here:

<https://docs.agentanalytics.sh/guides/ai-agent-experiment-tracking/>
