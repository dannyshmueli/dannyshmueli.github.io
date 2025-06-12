---
layout: post
title: "I Wrote My First Chrome Extension Without a Single Line of Code"
date: 2023-04-03 00:00:00 +0300
categories: ChatGPT
tags:
  - chrome extension
  - ChatGPT
  - no-code
  - polygon-checker
---

A colleague recently asked me if I knew of a QA tool that could check whether a coordinate was within a polygon. I replied that I didn't, but suggested we try to build a tool like that with ChatGPT.

In this blog post, I will detail how I built a Chrome extension using ChatGPT **without writing a single line of code**, only following instructions and copy-pasting, despite having no prior experience in creating a Chrome app.

<!-- more -->

👉 **Read the full ChatGPT transcript** [here](https://sharegpt.com/c/KTIUmFy)  
👉 **Install from the Chrome Web Store:** [Polygon Checker](https://chrome.google.com/webstore/detail/polygon-checker/ddapaifbpicpmfldajmahofeeppkbdkh)  
👉 **Source on GitHub:** [github.com/dannyshmueli/polygon-checker](https://github.com/dannyshmueli/polygon-checker)

![Polygon Checker screenshot](/img/polygon-checker/polygon_checker_screenshot.png)

## The Idea

The goal was to build a Chrome extension that could take **a list of coordinates describing a polygon** and **a single map coordinate** as input, then output whether the single coordinate lies *inside* the polygon.

## The Process

I began by explaining my idea to ChatGPT 3.5. The model understood the requirements and produced everything I needed:

1. A `manifest.json` file.
2. A `popup.html` UI.
3. A JavaScript file with the point-in-polygon logic.
4. Step-by-step instructions for loading and testing the extension in Chrome.

ChatGPT even helped me debug along the way – for example, fixing an issue where the result disappeared too quickly after display.

![Improving the polygon input](/img/polygon-checker/improving_the_polygon_input.png)

The first version's UI wasn't ideal (the textbox was *tiny*), so I asked ChatGPT to enlarge it and polish the styling. After a few iterations the popup looked and felt great.

I also noticed the generated `manifest.json` listed unnecessary permissions. After asking "Why do we need these?" ChatGPT agreed they could be removed and produced a leaner version:

![Removing unneeded permission](/img/polygon-checker/removing_permission.png)

Finally, ChatGPT wrote a perfectly-serviceable `README.md`, which I committed as-is.

## Reflections on AI Collaboration

Working closely with ChatGPT offered a unique perspective on what AI collaboration can look like in product management and software development.

### Accelerating development

The pace was remarkable. What would normally require hours of searching and trial-and-error took minutes with concise prompts and copy-paste.

### Bridging the knowledge gap

I had *never* built a Chrome extension before, yet ChatGPT walked me through every step, from project structure to Chrome's developer mode.

### Enhancing creativity

Because ChatGPT handled the boilerplate, I could focus on the *creative* parts – UI tweaks, use-case brainstorming, and of course writing this post.

### Potential challenges

Not everything was perfect – for example, the suggested SVG icon needed a redo. Human oversight still matters, and the model will only get better.

## Conclusion

This experience felt like pairing with a tireless coworker, iterating together until we were both satisfied. It showed me how AI tools can empower anyone – regardless of background – to create and ship useful software.

As language models evolve, I can only imagine how they'll continue to revolutionise software engineering and product development.
