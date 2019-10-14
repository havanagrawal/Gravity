---
layout: post
title:  "Learnings - September 2019"
date:   2019-09-30 11:43:00 -0700
category: ["learning", "pinned"]
---

September was mostly uneventful. I received some praise and some critical feedback at work, which I am trying to incorporate into my daily work.

## Articles

 * [From FOMO to JOMO - The Joy of Missing Out](https://nesslabs.com/jomo)

## Miscellaneous

 * An eye-opening hypothetical scenario that exposes a common fallacy in trusting p-values:
   > Imagine a society where scientists are really, really bad at hypothesis generation. In fact, they're so bad that they only test null hypothesis that are true. So in this hypothetical society, the null hypothesis in any scientific experiment ever done is true. But statistically using a p value of 0.05, we'll still reject the null in 5% of experiments. And those experiments will then end up being published in scientific literature. But then this society's scientific literature now only contains false results - literally all published scientific results are false.

 * [The Clockwise/Spiral Rule for C Declarations](http://c-faq.com/decl/spiral.anderson.html): An unbelievably simple yet effective evaluation rule that I wish I had known when I was learning function pointers.
 * Remember to **Be Proactive** (Habit no. 1!). Bring up your ideas, concerns, problems and their solutions before you are asked for them.
 * Take out time to read. White papers, novels, articles, anything that provokes thought and keeps you mentally engaged.

## Guice

 * Use `@AssistedInject` along with Guice Factories for mixing Guice injection with user-provided values.
 * Use `@Inject(optional=true)` or `OptionalBinder` for providing optional dependencies into classes.
