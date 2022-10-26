---
layout: post
title: Generating a Typescript router in Django
description: Pre-announcing an alternative to django-js-reverse
tags: fa-code
---

(Nerd alert! This post is a small technical snippet for Python and Django programmers. If you don't know what those proper nouns mean, don't worry — feel free to skip this post.)

Buttondown has used [django-js-reverse](https://github.com/ierror/django-js-reverse) since its inception to route backend API urls from the Django backend to the Vue frontend.

This has been (not to damn with faint praise!) better than nothing: some kind of syncing mechanism certainly beats having to manually correlate backend routes with frontend definitions. But there are two nits I've had with the package:

1. The package isn't TypeScript-friendly; errors in routing or invocation (such as calling `Urls.email_detail()` instead of `Urls.email_detail("foo")`) are not caught until runtime.
2. The package does some light metaprogramming to allow you to refer to a route not just as its name (`email-detail`) but via camel or snake case (`Urls.emailDetail()` or `Urls.email_detail()`). This attempt at ergonomics makes it trickier to actually find all invocations of a route at a glance.

Naturally, I thought to myself: why not try my hand at building a better version? I'm happy to share that it was fairly painless: while I'm still a little bit away from _formally_ open-sourcing this as a package (there are a number of administrative tasks like registering with PEP and settling on a name that I need to take care of), here is the [total implementation](https://gist.github.com/jmduke/434739ebc4e2112397becfd403f2ecfd) — 34 lines of Python and 4 lines of templated TypeScript.

I am confident this implementation is missing many, many, many edge cases. It does not handle URL namespacing nor non-string parameters, nor I'm sure many other things. But it is a step change improvement to Buttondown's codebase, and I'm excited to have it. Please let me know if you find it useful, or if you have any feature requests/suggestions — I promise to update this blog post with a link to the repository once it's published to PEP.
