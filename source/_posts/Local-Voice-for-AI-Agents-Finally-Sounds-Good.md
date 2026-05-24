---
title: Local voice for AI agents finally sounds good
date: 2026-05-24 22:15:46
tags:
  - ai-agents
  - tts
  - hermes
  - local-ai
excerpt: "Supertonic 3 made local agent voice good enough that I switched Hermes to it. Here is the prompt and why you should try it."
index_img: /img/local-voice-for-ai-agents/hero.png
banner_img: /img/local-voice-for-ai-agents/hero.png
categories:
  - ai
  - developer-tools
---

I finally found a local voice for my agent that I actually like: [Supertonic 3](https://github.com/supertone-inc/supertonic) by [Supertone](https://www.supertone.ai/).

I tried Edge TTS. I tried Chatterbox. I tried a few local options. Most were either too robotic, too slow, or too much setup for something I want to use every day.

Supertonic 3 is the first one that felt good enough to make Hermes speak in my Discord voice channel.

## Hear it

This is the first sentence of this post, generated locally with Supertonic 3 at 95% speed:

<audio controls preload="metadata" src="/audio/local-voice-for-ai-agents-supertonic-3-sample.mp3">
  Your browser does not support the audio element.
</audio>

<!-- more -->

## Why use it

For agents, voice quality changes the product feel.

Bad voice makes the agent feel like a toy. Good local voice makes it feel like part of the workflow.

The important part is simple:
- it runs locally
- it sounds natural enough
- it is fast enough for agent replies
- it does not need a paid TTS API for every response

That is the whole point. If your agent can talk back, the voice should not be the worst part of the experience.

## Prompt to apply it to your agent

Paste this into your coding agent:

```text
Add Supertonic 3 as the local text-to-speech provider for this agent.
```

For Hermes, I wired it as a command TTS provider and set the default voice to `M3` at speed `1.05`.

## When I would use it

Use this when your agent already works, but the voice makes it feel cheap.

Do not start with voice. Start with a useful agent. Then add a voice that makes people want to keep using it.

Supertonic 3 cleared that bar for me.
