---
layout: post
title: Email tracking is now off by default
tags: fa-user-secret
image: /img/optin-share.png
canonical: https://buttondown.email/blog/2021-03-01-opt-in-analytics
---

Last week I blogged about [investments in something that existing users will probably not appreciate](/2021/02/21/importer-v2)
and it appears that this week I am continuing the trend by announcing a change that only affects new
accounts going forward:

**Buttondown is turning off analytics by default.** (This only applies to _new_ accounts: I haven't changed any settings on existing accounts.)

This is something that a few folks have mentioned over the years:
allowing newsletters to opt out of link and open tracking is nice but allowing an escape hatch
is a little less opinionated than defaulting to do the right thing.

I was fortunate enough to get [Fireballed](https://daringfireball.net/linked/2021/02/26/buttondown)
this past week, and one of John's comments rekindled this notion:

> Buttondown’s privacy “win” is that it at least allows you to turn tracking off with a simple checkbox. Most services don’t. I can’t find any hosted service that doesn’t offer tracking period, or even defaults to no tracking.

If you'll let me wax poetic about running a service for a second: it is tiring to make decisions, and it is easy to think from the outside that certain artifacts are deliberate as opposed to made from a lack of decision.

Analytics being opt-out wasn’t deliberate so much as it was an artifact of that being originally implemented as an opt-out and then me not changing anything or revisiting it in the past three years.

But that's how building things on a temporal budget goes, and "lack of decision" is not meaningfully better than a bad decision: it is useful to get the codebase
viewed from a completely contextless lens, and to strip away a bit of the archaelogical detritus that comes from remembering the Buttondown of 2021 and the Buttondown of 2019 and the Buttondown of 2017.

Anyhoo. Now it's opt-in, because opinionated defaults matter. And all it was was a single line of code:

![](/img/optin.png)

As a reminder, if you want to opt out (or now _in_) to analytics, all you have to do is go to your [Account settings page](https://buttondown.email/account).

And stay tuned for more news on the privacy & sensible data front! My goal is to push out some changes that let you
get information about your newsletter without having to go _whole hog_ on the tracking pixel front, like conditionally enabling clicks but not opens (or vice versa)
and adding support for anonymized tracking (so you know _what_ is opened or clicked on without knowing _who_.)
