---
layout: post
title: Adding a Buttondown subscription form to Webflow
tags: fa-graduation-cap
canonical: https://buttondown.email/blog/2019-10-11-adding-a-buttondown-subscription-form-to-webflow
---

You can create a subscription form in Webflow and connect it to your Buttondown newsletter to gather email addresses and segment them in two easy steps:

1. **Grabbing your subscription URL** from Buttondown
2. **Configuring your Webflow form** to forward emails to Buttondown

### Requirements

1. If you haven't already, [create a Buttondown account](https://buttondown.email/register). It's easy and only takes two minutes.
2. Ensure you have a [form](https://university.webflow.com/article/intro-to-forms) on your Webflow page.
3. Ensure there's at least one **input field** on your form (set to required) with the name set to `email` and the `text type` set to email, like the below:

![](/img/59.jpg)

### Getting your subscription URL

1. Log in to your Buttondown account and go to the [Share page](https://buttondown.email/settings#).
2. Click on "as a form".
3. Copy the URL on the second line, as highlighted below:

![](/img/9.png)

### Configure the form

1. Return to your project in the **Webflow Designer**.
2. Select the form on the canvas.
3. Go to **the Form Settings** section under the **Settings** tab.
4. Paste the action URL you just copied into the **Action** field.
5. Set the **Method** to `POST`.
6. Publish your site!

![](/img/10.png)

### Any trouble?

If you have any questions, please feel free to [email us](mailto:support@buttondown.email) — we'll be happy to help.
