---
layout: post
title: Webhooks and the Programming page
tags: fa-code
canonical: https://buttondown.email/blog/2021-01-06-webhooks-and-the-programming-page
---

If you check your nav bar, you’ll notice a [new little section entitled **Programming**](https://buttondown.email/settings/programming). This is, as you might expect, a tab for programming your newsletter! Buttondown is getting more and more advanced at creating technical and advanced use cases for our authors, and I wanted a place to put all of these features without crowding out other parts of the dashboard.

So, if you’re not interested in supercharging your newsletter (and I don’t mean that in a bad way at all: none of _my_ newsletters are using webhooks!) feel free to ignore this. But if you’re interested in seeing how to better integrate Buttondown into your stack, let’s dive in!

![](/img/30.png)

## API Key

![](/img/31.png)

The Programming page is where you can find your API key. This was previously buried at the bottom of the main settings page for no other reason than “I didn’t know of a better place to put it.” Here is that better place!

## Webhooks

Now we’re getting to the exciting stuff. After almost an entire year in private beta, webhooks are now _live_.

I will spare you the how and the why and the what of webhooks: more talented writers, particularly the fine folks over at [Stripe](https://stripe.com/docs/webhooks) and [WorkOS](https://workos.com/blog/building-webhooks-into-your-application-guidelines-and-best-practices), have done a better job than I ever will.

I think the most interesting bit to dive into is specifically what event types are possible with webhooks:

1. Email creations
2. Subscriber creations
3. Subscriber churns
4. Unsubscribes
5. Subscriber trial beginnings and endings, for paid newsletters
6. Subscriber updates to metadata or notes

If you think I’m missing anything, [let me know](mailto:justin@buttondown.email) — there’s always going to be an updated list on [the API schema](https://api.buttondown.email/v1/schema#section/Events).

To create a webhook, all you need to do is set the URL, a description, and what event type you want that webhook to receive:

![](/img/33.png)

## Events

![](/img/32.png)

Of course, webhooks are nice, but it’s important to understand what is actually getting fed _into_ those webhooks, and your Events table gives you an overview of all events flowing through your account. Each event is listed here, along with the metadata and the payload that would be sent.

Notably, if you’re looking to test a part of your integration you can actually _replay events_ from this view by clicking on the relevant event and hitting the “Replay event” button. This makes it easy for you to stage your webhooks without having to rely on manual traffic.

![](/img/34.png)

## API requests

![](/img/35.png)

Lastly, if you’ve wired together an API integration with Buttondown, you get a nice administrative view of all of your previous requests along with latency, success, and response information. You can use this to debug any problems you have with your integration.
