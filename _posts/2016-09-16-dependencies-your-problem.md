---
layout: post
title: "Your Dependencies Are Your Problem; Or, Why I Don't Use React Router"
date: 2016-09-16
permalink: /dependencies-your-problem
tags: react, javascript, software, risk
---

There's been a lot of [negativity](https://news.ycombinator.com/item?id=12511419) surrounding the latest release of [React Router, v4](https://github.com/ReactTraining/react-router/blob/v4/README.md).

Some of the criticisms contain a kernel of truth, but that kernel is wrapped in a bitter shell of entitlement and mean spiritedness.

### The Bottom Line

__YOU are responsible for your app's dependencies.__

"You" here includes you personally, your team, and your company. It does _not_ include OSS volunteers. It does not include other companies and their developers that contribute to OSS.

You made a choice to depend on a new, unproven library. When the API of that new, unproven library turns out to be unstable, and you have to undergo a rewrite, guess what? That's _your problem_.

Libraries can be liabilities as well as assets. This is a basic software fact. Part of your job as a developer is to assess and mitigate risk. If you don't like the churn rate of a library's API, _don't depend on it_. Yes, it's hard keeping up with best practices in a chaotic ecosystem, but that's why you get paid well.

If someone else on your team is making bad decisions about dependencies, speak up. Be a voice of reason.

### npm install hairball

Here are some questions to ask before adding a library to your dependencies:

* Is the library battle tested?
* Is its API stable?
* How does its code smell?
* Is it backed by a community of developers that help maintain it?
* Is it backed by stable organization? Is that organization known for responsible OSS stewardship?
* Is it small enough that none of the other questions matter?

There's plenty more you could ask. The point is: do your due diligence. If you decide to gamble, own it. Take responsibility.

### React Router and Me

I've been developing production React apps for two years. I still use a tiny, homegrown router, which works just fine. When React Router came out, I looked at it, weighed its value against its apparent complexity, and decided to hold off.

After a while, new versions came out with nifty features (async routes, transitions). But the API smelled funny to me, and had not settled, so I continued to wait. I implemented routing features ad hoc in my own code bases. Hacks were made, but they were isolated.

I am not Nostradamus. I just did my homework. I remain hopeful a routing solution will be found by the community. When it does, I will jump on board, and I will be grateful to the developers that volunteered their time. Until then, I'm avoiding the risk.

### That Said...

Before I joined the mosh pit of modern JavaScript, I spent a few years in the Clojure community. The Clojure community has a history of extreme conservatism when it comes to API changes. Sometimes it was frustrating when improvements to the language and core libraries came slowly, but having a stable base on which to build was invaluable.

The JavaScript community, in contrast, has a tendency to release half-baked libraries, promote them, then rewrite or drop them when their shortcomings become apparent. This is unfortunate. I wish more developers would battle test libraries before promoting them (this applies both to library creators, and early adopters).

One of the reasons I was willing to give React a try early on, besides the fact that I liked its functional approach, was that Facebook had a policy of using their open source libraries in production. They are wary of open sourcing anything that has not passed muster.

Individuals and small teams in the JavaScript community would do well to follow Facebook's example.

To be clear: developers that release libraries and then iterate the API in public do not deserve personal scorn for doing so. Let's be decent humans. Strong opinions can be held and expressed without hostility.
