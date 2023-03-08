---
layout: post
title: Support for HTTPS tracking links
tags: fa-lock
canonical: https://buttondown.email/blog/2021-03-04-support-for-https-tracking-links
---

The single biggest pain point for me in 2020 was the fact that my email service provider did not support HTTPS tracking links. [Discussion of tracking links aside](https://blog.buttondown.email/2021/03/01/opt-in-analytics) and [importance of HSTS aside](https://blog.qualys.com/vulnerabilities-research/2016/03/28/the-importance-of-a-proper-http-strict-transport-security-implementation-on-your-web-server), this resulted in a truly awful onboarding experience in which people would onboard and send emails through their HTTPS-only domain, discovering in their first newsletter that all of their links were broken in some browsers. The worst thing! You can’t take back emails, and if folks didn’t catch it when drafting (which was sadly often) it meant their first-ever experience with Buttondown was marred.

### The solution

I’ve been a huge fan of the folks over at [Postmark](https://postmarkapp.com/) for a long time: Buttondown has been using them as a transactional email provider for years. When they launched support for non-transactional emails, I was itching for a reason to try them out — and lo and behold, here is the perfect one!

### How to use it (if you’re not sending from any custom domain)

All you need to do to start sending from your custom HSTS domain is to go through the regular “sending from a custom domain” flow on your [Newsletter settings page](https://buttondown.email/settings). Buttondown will automatically detect that you’re sending through an HTTPS-only domain and route your newsletter to use Postmark instead.

### How to enable HTTPS tracking (if you are already sending from a custom domain)

This is... a little jankier. Go ahead and _remove_ your custom domain, and then re-add it — you should now see a new set of custom DNS records to use. Once you’ve added them, go ahead and re-enable analytics tracking and you’re good to go!

### Any questions?

As always, [my inbox is open](mailto:justin@buttondown.email)!
