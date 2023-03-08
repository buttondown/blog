---
layout: post
title: Better logging in RQ
description: A quick hit nerd post for all you Pythonistas out there.
tags: fa-pencil-square
canonical: https://buttondown.email/blog/2022-09-05-rq-logging
---

(Nerd alert! This post is a small technical snippet for Python and Django programmers. If you don't know what those proper nouns mean, don't worry — feel free to skip this post.)

I have been a fan of the asynchronous programming library [RQ](https://python-rq.org/) for a long time. It's been a classic "sweet spot" library: more powerful and futureproof than rolling my own solution from scratch, but more aggressively minimalist and ergonomic than something like [Celery](https://docs.celeryq.dev/en/stable/getting-started/introduction.html) which has felt the accumulated weight of a decade of bloat and featureq requests.

And Buttondown uses RQ a _lot_. Every API request, Stripe event, email sent, or webhook event created (plus around seventeen dozen other things) goes through RQ in order to keep the interface nice and snappy and the concomitant backend requests short and stout.

One of the things that is not so great about RQ is the out-of-the-box observability; it comes with an admin dashboard that does little else than tell you the number of items in each queue, and you're left to build out your own solution from scratch.

Here is what I did. This ten line `Job` subclass is easy to plop in and replace with the default one that RQ provides. I'm using [structlog](https://www.structlog.org/) as the logger mechanism, but that's more of an implementation detail than anything (you can use whatever you want).

```
class JobWithBetterLogging(Job):
    def perform(self) -> Any:
        start = time.time()
        result = super().perform()
        end = time.time()
        logger.info(
            "job.finished",
            key=self.key,
            seconds=(end - start),
            method=self.func_name,
            data_size=len(self._data),
            queue=self.origin,
            enqueued_at=self.enqueued_at.timestamp() if self.enqueued_at else None,
            started_at=self.started_at.timestamp() if self.started_at else None,
        )
        return result
```

You get a _lot_ of information here: your logs now tell you exactly what keys are being logged, how long each function took, how long they've been sitting in the queue, the _size_ of the payload you placed within Redis, and what queue was being used. This, combined with offline log analysis (or a structured query engine like [Logtail](https://logtail.com/)) will let you construct dashboards that can trivially answer questions like:

1. What functions are taking the most aggregate time?
2. What functions are bloating up Redis?
3. What queues are accruing the most demand?

And so on.
