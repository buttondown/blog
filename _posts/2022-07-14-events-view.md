---
layout: post
title: A better Events view
description: Debug your webhook integrations!
tags: fa-code
image: /img/uploads/events-share.png
---

(Nerd alert! This post is about a fairly nerdy part of Buttondown’s featureset — _webhooks_. If you’re a less technical user, this stuff probably doesn’t apply to you.)

I’ll be honest with you — it is hard to keep track of the myriad ways folks use Buttondown’s events & webhooks. There are the standard ones: listen to `email.created` events and then publish the email to the CMS, listen to `subscriber.created` and `subscriber.deleted` events to update tags in an internal database, and so on. But the long tail is where I find it hard to keep track: I know of one newsletter, for instance, that is listening to `draft.created` events to sync their Buttondown drafts with their local iPad storage in a way that I probably _can’t_ advise as good engineering but I am still quite fascinated by.

Regardless of _how_ you’re using webhooks [^1], there’s one piece of feedback that you’ve probably given me: that the dinky little “events” table in the Programming page is… let’s say lackluster.

![](/img/events1.jpeeg)

I have _heard_ that feedback. I have internalized it. And now I’ve built something better. Behold the new [Events list view](https://buttondown.email/events):

![](/img/events2.png)

Gone is the amateurish limit of 100 events: now you can paginate to your heart’s content! (And yes, that pagination widget is… a little cumbersome at this point, and I will be replacing it with something that requires a little less horizontal scrolling.)

You can filter on both _status_ and _event type_ to make it easier to pinpoint problematic events and replay them:

![](/img/events3.png)

In fact, I’m so in love with this filtering component that I plan on backporting it to the subscribers page too to allow more in-depth filtering & sifting there!

Anyway, apologies for how long-overdue this page is. I tend to only hear about the webhooks & events side of things once-per-customer (as they’re setting things up) and then it goes back to being quiet, so I forget how annoying it can be. If there’s any part of the _programmatic_ side of Buttondown that you wish was better, please [let me know](mailto:justin@buttondown.email).

[^1]: And to be clear, per the “nerd alert” preamble at the top of this post — if you don’t even know what a webhook _is_, that’s completely normal and fine and this post is probably not for you.
