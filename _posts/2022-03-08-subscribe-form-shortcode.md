---
layout: post
title: Embed your subscribe form in the middle of your writing
description: Use {{ subscribe_form }} to let folks subscribe faster!
tags: fa-code
canonical: https://buttondown.email/blog/2022-03-08-subscribe-form-shortcode
---

A long-standing request that I’ve never quite got to is the ability to throw in subscription forms in the middle of your emails, so that folks who are scrolling through your web archives are prompted to subscribe before the end of the email itself.

This approach is something that I’m sure has vigorous and impressive scientific impacts on your conversion funnel, but moreover it for a long time was my favorite type of feature request, the one that hits two constraints:

1. Is eminently reasonable
2. Clearly slots into Buttondown’s architecture & surface

On that second bit, I haven’t taken full advantage of it yet, but Buttondown now renders every email twice — once for email clients and once for the web archive. This makes it much easier and cleaner to build features like this in the future!

Anyhoo, the magic incantation you’re looking for is:

{% raw %}

```
{{ subscribe_form }}
```

{% endraw %}

As in, if you want a cute little interstitial in the middle of your email you can do something like:

{% raw %}

```
Here’s a cute picture of a dog!

{{ subscribe_form }}

And then here’s an even cuter picture of a dog!
```

{% endraw %}

You get the idea.

You can also check out the full list of magic codes (or to use the technical term, “template variables”) in Buttondown’s documentation [here](https://docs.buttondown.email/advanced-features/template-variables). If you’re looking for anything else along these lines, let me know — I’m all ears!
