---
layout: post
title:  "Learnings - June 2019"
date:   2019-06-30 11:13:00 -0700
category: ["learning"]
---

I started working at Google (again), recently, which has been incredibly exciting. I also have a couple of side projects ongoing, which keep me fairly occupied (and satisfied). I've also started to come across some brilliant articles that have (and I'm trying not to be dramatic) pretty much changed my life and approach to learning. I've decided to maintain a public log of all the small little snippets of knowledge I gain through these various sources. Here goes the June 2019 edition:

## Java

 * Use [parameterized tests](https://github.com/junit-team/junit4/wiki/Parameterized-tests) for tests that are data-driven. This sounds particularly useful in the context of competitive programming repos, where there's typically a single function tested with different data.
 * Use `@Rule` to insert boilerplate test code that runs before and after each test case. Note that this provides a richer mechanism than `@Before` and `@After` since you can write multiple `@Rule`s, and they will be "nested" in the order that you declare them. [This](https://stackoverflow.com/a/13489506/4014685) answer on StackOVerflow explains it fairly well.
 * Validate assumptions. Spent more than a day discussing how to wrap an API to make it idempotent, only to realize that it already was.
 * Either explicitly design classes for extension, or make them final. If making them final, make them implement an interface so they are mockable.

## Learning

 * Use [Anki](https://apps.ankiweb.net/) (or a similar spaced-repetition app) for learning new things. This is an incredibly powerful way of retaining information over an arbitrarily long period of time, and I wish I had discovered it sooner. (I implore you to at least try it out despite the fact that they use the term _flash cards_; it really doesn't do the concept justice)
 * Sleep for 8+ hours each night. It is a wonder how much the brain does while you sleep, including persisting the day's experiences into long-term memory, integrating those experiences with your long-term internal knowledge graph during REM, and reducing emotional fatigue.
