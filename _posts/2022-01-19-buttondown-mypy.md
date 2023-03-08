---
layout: post
title: Migrating Buttondown to mypy
description: The journey of 37,000 lines of static types begins with a single annotation.
tags: fa-code
image: /img/uploads/mypy-share.png
canonical: https://buttondown.email/blog/2022-01-19-buttondown-mypy
---

## What is Buttondown?

[Buttondown](https://buttondown.email) is an application for writing, sending, and growing newsletters — though that part isn't quite applicable to this essay!
The more important part: Buttondown has a Python & Django backend of around 38,000 lines and it's been around for around four years, so it has a good-but-not-excessive amount of cruft and abstractions that I wish I could take back.

## What is mypy?

The short answer: `python : mypy :: JavaScript : TypeScript`. [^1]
The longer answer, as provided by the [official mypy docs](http://mypy-lang.org/):

> Mypy is an optional static type checker for Python that aims to combine the benefits of dynamic (or "duck") typing and static typing. Mypy combines the expressive power and convenience of Python with a powerful type system and compile-time type checking. Mypy type checks standard Python programs; run them using any Python VM with basically no runtime overhead.

## Why did I do this?

I've actually been using something that's, like, _mypy-adjacent_ for a while now. [PyCharm](https://www.jetbrains.com/pycharm/) was for a long time my Python IDE of choice [^2] and it had really strong support for type hints, meaning that a declaration like:

    def count_words(input: str) -> int:
      return len(input.split())

would be enough to provide PyCharm with information for _callers_ of `count_words`, such that I would get a very angry red squiggle if I tried to write something like:

    arbitrary_string = "hello world"
    if "hello" in count_words(arbitrary_string):
      print("Greetings found!")

This was net useful in of itself, and _even if you have no plans to integrate with mypy_ I would recommend getting in the habit of using type hints! (Much electronic ink has been spilled about the niceties of writing type signatures as an exercise in thinking more deeply about your interfaces & contracts. I won't rehash those arguments, but rest assured I agree with them.)
However, that was the depth of my investment. Back when I did this in ~2019 or so, I looked into actually providing a typecheck _step_ for the Python codebase and was stymied by lack of third-party "stub" support. [^3]
That was around eighteen months or so ago, and things have improved significantly since then! On a lark, I decided to pick back up the branch (aptly named `mypy-world-domination`) and saw that both [django-stubs](https://github.com/typeddjango/django-stubs) and [boto3-stubs](https://pypi.org/project/boto3-stubs/) have progressed significantly, to the point where the majority of issues flagged by mypy were not "hey, I have no idea what `django-rest-framework` is" but "hey, you're not handling this `Optional` correctly."

After some configuration futzing, I was greeted with a veritable _wall_ of `mypy` errors:

    $ poetry run invoke typecheck
    utils/functional.py:58: error: Incompatible return value type (got "Dict[Any, Any]", expected "Stream")
    markdown_rendering/extensions/newtab.py:28: error: "handleMatch" undefined in superclass
    emails/views/utils.py:99: error: "BulkActionView" has no attribute "model"
    # ...truncated for conscision...
    api/tests/test_emails.py:31: error: "Client" has no attribute "credentials"
    api/tests/test_emails.py:34: error: "HttpResponse" has no attribute "data"
    Found 822 errors in 281 files (checked 887 source files)

**822** instances of `mypy` telling me something was amiss. It was time to get to work!

## Where did I stumble?

**Optional unwrapping**

The first stumbling block is perhaps the most stereotypical: dealing with optionals. My code now has a lot of the following pattern:

    def cancel_premium_subscription(subscriber: Subscriber) -> None:
      stripe_subscription: Optional[StripeSubscription] = subscriber.stripe_subscription
      if not stripe_subscription:
        return
      stripe_subscription.cancel()

It's fair to say, too, that this is perhaps _working as intended_. Nulls are [the worst thing or whatever that quote is](https://www.lucidchart.com/techblog/2015/08/31/the-worst-mistake-of-computer-science/), and I think my codebase suffers from "None-as-control-flow" syndrome a good deal. Part of this has been ameliorated by borrowing some concepts like [Result types](https://en.wikipedia.org/wiki/Result_type) from other, more mature static environments, but I would love to see some a bit more semantic sugar not unlike the constructs offered by [TypeScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining) or [Swift](https://docs.swift.org/swift-book/LanguageGuide/OptionalChaining.html):

    const person = {
      name: "Jane",
      pets: [{
        type: "dog",
        name: "Spike"
      }, {
        type: "cat",
        name: "Jet",
        breed: {
          type: "calico",
          confidence: 0.8
        },
      }]
    };

    // Returns [undefined, "calico"]
    console.log(person.pets.map(pet => pet.breed?.type)

**Mutable global payloads**

In what is probably one of many regrettable architectural decisions, I rely on Django [middlewares](https://docs.djangoproject.com/en/4.0/topics/http/middleware/) to handle a lot of things that happen within the lifespan of an API request. This might looks like this:

    class CustomDomainRoutingMiddleware(MiddlewareMixin):
      def process_request(self, request: HttpRequest) -> None:
        custom_domain: Optional[str] = extract_custom_domain(request)
        if custom_domain:
          newsletter = Newsletter.objects.get(domain=custom_domain)
          request.newsletter_for_subdomain = newsletter

This approach is valid Python, and [mostly recommended within Django documentation](https://docs.djangoproject.com/en/4.0/topics/http/middleware/#writing-your-own-middleware), but `mypy` is not a fan for two reasons:

1. We're setting global state on an `HttpRequest` which has no concept of a `newsletter_for_subdomain` attribute.
2. We then need a way of _retrieving_ that attribute from an `HttpRequest` object; any subsequent access of `request.newsletter_for_subdomain` will also raise warning signs.

My suspicion is that the right approach here is to declare an omnibus `HttpRequest` subclass with all potential global payloads:

    class ButtondownRequest(HttpRequest):
      newsletter_for_subdomain: Optional[Newsletter]
      # ... and so on

But when I go through such a process, I run into lots of violations of the [Liskov substitution principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle).
Of the three stumbling blocks listed, this is the one that I would bet has the most obvious (or at least “obvious in retrospect”) solution. One of the trickinesses of migrating to mypy in 2022 is that, while it's easier and more worthwhile than it was in 2020, documentation & war stories are still somewhat scarce.

**Type refinement**

Django (and thus Buttondown) express foreign key relationships as optionals. For example, I have a `Subscriber` model that represents a single email address subscribed to a newsletter. A simplified version of this model looks something like the following:

    class Subscriber(models.Model):
      email_address = EmailField()
      creation_date = DateTimeField()
      import_date = DateTimeField(null=True) # blank if was not imported

      # Every subscriber corresponds to a single newsletter
      newsletter = ForeignKey(Newsletter)

      # Premium subscribers also exist in Stripe
      stripe_subscription = ForeignKey(StripeSubscription, null=True)

This is all `django-stubs` and `mypy` need to get a pretty useful understanding of what a `Subscriber` entails; it's got a handful of non-optional fields (such as `email_address` and `creation_date` and `newsletter`) and some optional fields (`import_date` and `stripe_subscription`).
The tricky part here is when you want to express an invariant upon all Subscribers. Let's say I have a cron that filters through all premium subscribers and checks to make sure the backing Stripe subscription isn't cancelled:

    def check_premium_subscriptions() -> Iterable[Subscriber]:
      premium_subscribers = Subscriber.objects.exclude(stripe_subscription=None)
      for subscriber in premium_subscribers:
        if subscriber.stripe_subscription.status == 'cancelled':
          yield subscriber

Sadly, `mypy` is not a huge fan of this — `subscriber.stripe_subscription` is an `Optional[StripeSubscription]` and calling `.status` on it is therefore dangerous.
You could, I think, persuasively argue that this is solved with something like a `Result` type (there's a very interesting Pythonic one [here](https://github.com/dry-python/returns)). A more elegant solution, though, and one that closely maps onto [TypeScript's approach to nuanced type refinement](https://www.typescriptlang.org/docs/handbook/2/narrowing.html), would be being able to declare a version of Subscriber that _has_ a StripeSubscription.
This issue in of itself is still interesting, though, because it suggests a better way to structure this cron and avoid the refinement entirely — iterating on the _subscription_ rather than the _subscriber_:

    def check_premium_subscriptions() -> Iterable[Subscriber]:
      subscriptions = StripeSubscription.objects.filter(status='cancelled')
      for subscription in subscriptions:
        if subscription.subscriber:
          yield subscription.subscriber

This kind of forced re-examination of cross-object relationships was a very useful byproduct of driving down my mypy errors, in much the same way that the act of expressing type signatures forces you to think a little more deeply about the contracts & interfaces you're reifying.

## How long did the whole thing take?

Buttondown's Python codebase currently sits at 38,103 lines. Upon the initial run of `poetry run typecheck` [^4], mypy reported **822** errors. Ouch.
Resolving those errors took me approximately **eleven hours** to resolve in full. I pulled out the metaphorical banhammer, annotating a file with `# mypy: ignore-errors`, only thrice:

1. Django's abstraction for creating RSS feeds has some very specific interfaces that I resolve in very gross ways, and rather than try to shoehorn it into something `mypy` would endorse I ignored the entire thing.
2. That "some models have stripe models associated with them" wasn't a toy example; it applied directly to how I handle paid accounts, and since I'm [knee-deep in a refactor](https://github.com/buttondown-email/roadmap/issues/62) of that codebase I decided to punt on the module in question.
3. Python's quasi-official [markdown rendering package](https://python-markdown.github.io/) has some significant duck-typing implications that make it very hard for me to rigorously type-check my extensions on top of it.

That was around two solid engineer-days spread across two weeks (I was doing this whilst traveling, so around an hour or two every day). The work was an even 80/20 split:

- 80% of the work was rote and pleasantly formulaic ("oh, that's actually an `Optional[float]` and not a `float`!"; "oh, I need to express this as an `Iterator` and not an `Iterable`!");
- 20% of the work was unbounded and painful, a bit of an "unknown unknowns" situation ("how do I express a strongly typed spread operator?"; "how do I express a partial mock?")

## What advice do I wish I had?

- This might be cheating, but if you know you want to _eventually_ move to `mypy` start as early as possible. Even if you need to litter your codebase with `Any` and `# type: ignore` annotations, the sooner you start the better.
- Getting the ground running with functional bits of your codebase as quickly as possible facilitates the entire process! Rather than trying to boil the ocean in your first go, start off with small little ponds of well-typed functionality before moving onto the hairier parts of your codebase. Django's app-based architecture lends itself very well to this, since ideally you're breaking out logically disjoint parts of your application early and often.
- Aggressively separate "type reification" (keeping the logic of the codeabse intact, but annotating as necessary) with "type fixes" (changing the logic of the codebase in order to clean up your types). My first few efforts commingled the two, which led to issues where I was seeing divergences in the behavior of unit tests and it wasn't immediately obvious what changes had caused them.

## Was it worth it?

_Yes!_ As mentioned above, I don't think I'd advise folks in trying to do a "big-bang"-style migration in the manner I did unless your codebase is sufficiently small; because I was working on this branch alongside other feature branches, churn was non-trivial and it would have made more sense to go package-by-package, starting with smaller and more reified interfaces and moving onward.
One of the more common cliches about shifting towards type safety, as alluded to earlier, is the concept of "forcing you to think in types". An example of this is something like the below method that I had kicking around:

    def send_draft(email, recipient)

"Recipient" is not a proper noun in Buttondown's codebase, and once I started adding types it became obvious that it was a bit of a chimera:

    def send_draft(email: Email, recipient: Union[Subscriber, Account, SyntheticSubscriber]) -> None

This need to declare an interface for something that "looks like a person with an email address" led to a number of arcane issues and duct-tape over the years — keeping audit logs of emails Buttondown’s sent to subscribers versus accounts is different, for instance. Just the act of writing out the contract made it much more obvious what the right behavior should be: rather than having an omnibus "send draft" method that tries to handle things differently, I refactored the logic to decompose the 'recipient' into a single email address, giving me something much more simple to reason about:

    def send_draft(email: Email, email_address: str) -> None

That being said, "thinking about types" and reifying your interfaces are caviar problems. I like those things, but I (and likely you) am in a position where elegant abstractions are a luxury compared to the value proposition of _writing safer code_. To that end, I thought I'd end by talking about some specific, real-world (albeit silly!) bugs that mypy revealed for me:

1. I have a simple dataclass — `AdminNewScheduledEmailNotifier` — that pings me in Slack whenever a new email is scheduled. I pass in a `ScheduledEmail` but mistakenly declared the type as an `Email`, which is an object with slightly different properties. Notably, `ScheduledEmail` has `schedule_date` whereas `Email` has `publish_date`. `mypy` detected this — and found a code branch where I was not getting notified about newly scheduled emails for certain newsletters.
2. I display cohort information for subscribers — essentially how many subscribers a given newsletter pulls up in a given bucket of time. I declared the dataclass for this structure to be of a `List[List[float]]` whereas in fact it was a `List[List[Optional[float]]`; this meant that while the Python side of things was fine (dataclasses do not throw if you pass in malformed data) my frontend assumptions of the returned data were not, and as a result mypy actually helped me fix a _frontend_ bug that I had been nigh-unable to reproduce for months.
3. I had a whole lot of duplicative test code where I was just passing in completely extraneous kwargs! For example, `Subscriber.objects.create(user=user)` where `user` is not actually an attribute on Subscriber. While this isn't a _bug_, it's certainly confusing, and can lead to serious issues down the line when I [programmatically modify the codebase](https://github.com/facebookarchive/codemod).
4. Lots and lots of mishandled Optionals. Too many to count.

## The promised land

I'm writing this post a few weeks after I actually completed and shipped the migration, so as to provide space for a bit of a coda — now that I've actually done the dang thing, what does day-to-day development feel like?
The answer is — more of the same, but with an additional guard rail. I'm writing code with very, _very_ few optionals now unless a foreign key is involved, and `precommit` lets me know when I've missed an off-ramp somewhere.
Plus, I get to write functional pipelines like the following:

    # A function that pulls in archived emails from an external source such as WordPress, Hey World, or Tinyletter
    def execute_online_import(
        retrieve_urls: Callable[[ArchiveImport], Iterable[str]],
        convert_response: Callable[[requests.Response], Email],
        archive_import: ArchiveImport,
    ) -> List[Email]:
        urls = retrieve_urls(archive_import)
        extant_email_subjects = typing.cast(
            List[str],
            Email.objects.filter(
                newsletter=archive_import.newsletter,
            ).values_list("subject", flat=True),
        )
        pipeline = pipe(
            requests.get,
            convert_response,
            partial(filter_extant_emails, extant_email_subjects=extant_email_subjects),
            partial(maybe_finalize_email, archive_import=archive_import),
        )
        emails = [pipeline(url) for url in urls]
        return [email.unwrap() for email in emails if email != Nothing]

Whereas before, Python made it a dangerous proposition to deal with partials and composition in this manner — what if `convert_response` doesn't map cleanly onto the arguments of `filter_extant_emails`!? — it's now safe.

## Useful resources

- [Hypermodern Python](https://medium.com/@cjolowicz/hypermodern-python-d44485d9d769), a very opinionated series of essays about structuring a well-typed & well-executed Python codebase. While this wasn't the _specific_ impetus for me going whole-hog on `mypy`, it certainly was an accelerating factor.
- [typeshed](https://github.com/python/typeshed), the official repository of type stubs. Without this package's growth and prominence I would have been at an absolute loss.
- [dry-python's returns](https://github.com/dry-python/returns), a collection of utility functions to improve type safety in your codebase. I don't use a lot of this package — mostly I use the pipeline functions which allow me to compose functions in a typesafe manner — but it's an excellent resource to read through and shift over parts of your codebase to more of a mypy-friendly mode.
- [Zulip](https://blog.zulip.com/2016/10/13/static-types-in-python-oh-mypy/) and [Cal Paterson](https://calpaterson.com/mypy-hints.html) both had great writeups of their shifts to mypy.

[^1]: Perhaps a more accurate comparison here would be with [Sorbet](https://sorbet.org/), a Ruby type checker that sits on top of Ruby. But I am surmising that more people are familiar with TypeScript than with Sorbet, so there you go.
[^2]: I've since replaced PyCharm with VSCode. This is for two reasons, neither of which are PyCharm’s fault! VSCode's Vue ecosystem is really robust compared to JetBrains', and I use VSCode at my day job (in the rare occurrences when I code these days), so context-switching is minimal. Still, I heartily recommend PyCharm if you're interested in very, _very_ strong integration with the Python ecosystem.
[^3]: "stubs" are a silly name for a useful concept that ideally should not exist. They refer to separately-published sets of type signatures for packages that themselves do not have type signatures. For instance, `Django` has made a [conscious choice to not yet include type information in their package](https://code.djangoproject.com/ticket/29299), so a stubs package — aptly titled [django-stubs](https://github.com/typeddjango/django-stubs) — consists solely of type signatures for Django itself.
[^4]: This is an incantation that may not look familiar. I use [poetry](https://python-poetry.org/) for Python dependency management and [Invoke](https://www.pyinvoke.org/) for task execution.

## Coda

Thank you to [Sumana Harihareswara](https://www.harihareswara.net) for proofreading this essay!
