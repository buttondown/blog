---
layout: post
title: Secure your account with multi-factor authentication
description: Buttondown offers support for Google Authenticator (et al)
tags: fa-lock
image: /img/uploads/mfa-share.png
canonical: https://buttondown.email/blog/2022-07-08-multi-factor-authentication
---

I suspect that there are two groups of people who read the title of this blog post: those who are saying "oh _finally_!" and those who are unfamiliar with the term 'multi-factor authentication'.

As you are likely aware, a [strong password](https://xkcd.com/936/) is necessary but inefficient in today's world to protect your account from malicious attackers. The most common family of approaches to defend your account is referred to as "multi-factor authentication", which is exactly what it sounds like: relying on additional pieces of evidence _beyond_ just your username and password in order to authenticate that you are who you say you are. (Have you ever had to type in a six-digit code that was texted to you, or click a link sent to your email? That's multi-factor authentication, too!)

Buttondown now lets you protect your account with the help of a third-party authenticator app like [Google Authenticator](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2&hl=en_US&gl=US) or [Authy](https://authy.com/). These apps exist as a remote fingerprint with a rotating code; when you log into Buttondown, you'll be prompted for the code from one of these apps.

If pictures are worth a thousand words, screenshares are worth at least fifteen hundred. Here's a two-minute walkthrough of what registering a QR code looks like and how to log in after doing so:

<div style="position: relative; padding-bottom: 88.45208845208845%; height: 0;"><iframe src="https://www.loom.com/embed/ca6e624ecde645db8be73fd02a7c885c" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"></iframe></div>

The support I've added for authenticator apps is one of many reasonable approaches that folks like to take when it comes to multi-factor authentication. I've already received requests for other avenues, such as [Yubikey support](https://www.yubico.com/) and SMS support. I plan on expanding coverage in the future; if you have a particular use case you'd like to have me know about, please [email me](mailto:justin@buttondown.email)!
