---
layout: post
title: "CIDetector with Live Video - UIFacialGestuures"
date: 2014-10-23 09:33:31 +0300
comments: true
categories: iOS
---

Making Facial Gestures or, Using CIDetector with Streaming Video

Since iOS7 Apple introduced new feature to the CIDetector, specifically smile and blinks detection.

I've wondered if i could "feed" it streaming video instead of static image and get make something like a live smile facial gesture be registered as an action on the device.

Here is a demo video of it:

{% youtube cdzPRymOC7o %}
<!-- more -->

I've extracted all the logic into an easy to use facade so it can easily be added in any project.
Have a look at the 
The main issues where:

1. CIDetector is a slow and takes up a lot of CPU

2. Smartley aggregating the feature detection results from the CIDetector until enough features are considered a registered gestured.

The code and example project: [https://github.com/dannyshmueli/DSFacialGestureDetector](https://github.com/dannyshmueli/DSFacialGestureDetector)




