---
layout: post
title: Better upselling for premium subscriptions
tags: fa-money
---

To be blunt, the upselling experience for paid subscribers has been…bad. This is entirely my fault!

When designing the paid subscriptions experience, I had a few blunders:

1. I assumed the majority of paid subscribers would be new subscribers. The flow is optimized for individuals who start out completely unsubscribed, and so the desire path is for folks to immediately be upsold after subscribing.
2. Buttondown is structured intentionally without much concept of a “subscriber-user”, meaning that subscribers interact with Buttondown entirely with their email address. This is good for portability reasons, but bad for state — meaning that the version of a newsletter that a subscriber views online is nigh-identical that which someone who isn’t subscribed views.

Both of these choices manifest in a number of less-than-ideal ways, but one particularly egregious one is the flow by which subscribers upgrade to premium subscriptions. Up until this week, they would have to go to the click a link in the footer of their email, re-input their email address, and then they were prompted to sign up. It was a confusing thing that spawned a lot of emails, and I get it. It was bad! No way around it.

Now, though, things are better. Rather than bore you with some of the technical details of how things are better, I’d like to just show you! This is the page that a subscriber is brought to when they’re prompted to upgrade their subscription in an email:

![The snazzy new Checkout page](https://i.ibb.co/wrhnnYP/image.png)

No fiddling with inputs or remembering what email address they used. Just a simple Stripe-powered checkout page. This has rolled out automatically, and you should see a healthy uplift in conversions!

{% raw %}
And if you're interested in dynamically inserting this link at an opportune moment, you can! The magic template variable is `{{ upgrade_url }}`, so you'd want to write something like:

<code>[Click here]({{ upgrade_url }}) to become a paid subscriber today.</code>
{% endraw %}
