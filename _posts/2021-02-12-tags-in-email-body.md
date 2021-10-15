---
layout: post
title: Referencing subscriber tags in your newsletter
tags: fa-tag
image: /img/tags-share.png
---

You've been able to reference subscriber metadata in your emails for
a while now. This is a great way of creating a mail-merge-esque effect
and customizing your newsletter for your recipients:

```
{% raw %}
{% if subscriber.metadata.first_name %}
Hello {{ subscriber.metadata.first_name }}!
{% else %}
Hello friend!
{% endif %}
{% endraw %}
```

You can now take this a step further if you're using tags! Tags are useful
ways of bucketing or grouping your subscribers already, but sometimes you want
to send slight variations of the same newsletter to multiple tags. To do so is simple:

```
{% raw %}
{% if "seattle" in subscriber.tags %}
Hope you're enjoying the snow up in Seattle this weekend!
{% else if "san francisco" in subscriber.tags %}
Hope you're enjoying the fog down in San Fran this weekend!
{% else %}
Hope the weather is pleasant wherever you are this weekend!
{% endif %}
{% endraw %}
```

(Though I certainly and sincerely hope you write something less stilted than the above.)

Buttondown's templating engine is based on [Django](https://docs.djangoproject.com/en/3.1/ref/templates/language/)'s, meaning that you can do a lot of other things.

Such as sending a paragraph if someone _isn't_ tagged with a given tag:

```
{% raw %}
{% if "python" not in subscriber.tags %}
I know you're not interested in Python that much, but I think you'll still
enjoy learning about the new [walrus operator](https://docs.python.org/3/whatsnew/3.8.html)
{% endif %}
{% endraw %}
```

Or handling logic if someone doesn't have any tags at all:

```
{% raw %}
{% if not subscriber.tags %}
I'd like to know more about you! Fill out a form [here](https://typeform.com).
{% endif %}
{% endraw %}
```

That's it for now! If there are other programmatic/template-y use cases you'd like to see Buttondown tackle, [my inbox is open](mailto:justin@buttondown.email)
