---
layout: post
title: Reminding folks to confirm their subscription
tags: fa-bell
canonical: https://buttondown.email/blog/2019-11-04-reminding-folks-to-confirm-their-subscription
---

Building an audience (or to use the slightly more concise, slightly more cynical term, _list building_) is mostly an art of navigating a spectrum between maximum conversions and maximum subscriber quality. [^1]

On one end of the spectrum, you can imagine a completely open sieve: a text input that sits on the internet, blessedly naive, ready for any enterprising individual to plop an email in and hit "submit". There are no barriers to entry: no validations, no typo checking, no spam detection, no CAPTCHA.

If you collect email addresses like this, well — you'll get a lot of email addresses! And a lot of garbage, too:

- people typing `gamil.com` instead of `gmail.com`
- spammers and malicious actors trying to use your mailing list for nefarious purposes
- undeliverable email addresses
- garbage strings

On the other end of the spectrum, you can imagine a careful and meticulous screening process: every potential email address is manually added, after careful and personal vetting, by you. You have utmost confidence that each email is kosher (whether it's by cross-referencing it with a person you know or some other database), but it's completely locked down to the outside world. Your list will be high-quality, but it won't grow very quickly (or without much manual labor.)

Buttondown's approach, naturally, is somewhere in between. We do a lot of automatic verifications [^2], like making sure that new subscribers' have syntactically correct email addresses with valid MX records and haven't been reported for spam.

We also _optionally_ allow what's called "double opt-in". This is the industry term for "make subscribers confirm their email addresses before I send stuff to them"; you've probably run into this before.

But the thing about double-opt in is that it's sort of a black box. You don't have a great sense of why folks aren't opting-in; some of them might be bots or spammers, sure, but a pretty common worry is just that hey, people have lots of emails, and the confirmation email may have been lost in the torrent.

That's why I'm excited to have launched **subscription reminders**, which is exactly what it sounds like: you can send emails to folks who haven't confirmed their address to remind them gently about your beautiful newsletter. [^3].

Simply go to your [Subscribers](https://buttondown.email/subscribers) page and choose the "Unconfirmed subscribers" filter, and you're good to go:

![](/img/17.png)

They'll get a nice lil email in their inbox reminding them of the exciting task ahead of them:

![](/img/18.png)

[^1]: This is, to be fair, true of _most_ conversion funnels; you're always going to be either sacrificing conversion rate for conversion quality or vice versa.
[^2]: Software! Truly, a miracle.
[^3]: And you can only do it a few times, to make sure things don't become overbearing.
