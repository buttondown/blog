---
title: Scheduling emails through the API
slug: scheduling-emails-through-the-api
date_published: 2019-05-23T03:48:44.000Z
date_updated:   2019-06-22T02:24:26.000Z
tags: fa-calendar-o
layout: post
---

<p>Scheduled emails has always been an important part of Buttondown’s platform.  I use it every time I send out a newsletter update, whether it’s a <a href="https://newsletter.buttondown.email">new feature</a> or a <a href="https://weeknotes.buttondown.email">weekly update</a>.</p><p>And — without beating around the bush <em>too</em> much — you can now schedule emails through the API!  With a simple API request, you can now schedule an email to get sent out next Tuesday!</figure><!--kg-card-end: image--><p>Or with just a handful lines of, say, Python code:</p><!--kg-card-begin: html-->

{% highlight python %}
import os
import requests
API_KEY = os.environ["BUTTONDOWN_API_KEY"]
API_URL = "https://api.buttondown.email"
ENDPOINT = "/v1/scheduled-emails"
data = {
  "subject": "Check out this email from the API",
  "body": "Hopefully the content is dope!",
  "publish_date": "2020-05-08T12:00:00"
}
requests.post(f"{API_URL}{ENDPOINT}", data ,headers={
  "Authorization": f"Token {API_KEY}"
})
{% endhighlight %}

<!--kg-card-end: code--><p>And of course, this works out of the box with any other way you might want to use Buttondown’s API — like, say, <a href="https://support.apple.com/en-us/HT209055">Siri Shortcuts</a> or <a href="https://zapier.com/developer/public-invite/10328/3f461ccf51be6e2e3f356c7365434907/">Zapier</a>.  Because APIs are neat.</p><p>(If you’re not familiar with Buttondown’s API, check out our <a href="http://api.buttondown.email">schema</a> and <a href="https://buttondown.email/settings">head over to your Settings page</a> to grab your API key!)</p>
