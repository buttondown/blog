---
layout: post
title: Attach files to your emails
description: Drag and drop has never been easier. (Just like Gmail.)
tags: fa-paperclip
image: /img/uploads/attach-share.png
canonical: https://buttondown.email/blog/2022-07-17-attachments
---

One of the delights in watching Buttondown take off over the past few years has been seeing the proliferation of use cases that, frankly, I never really imagined when I started building it. Teachers & faculty are using Buttondown to send lesson plans to their students; a peer group of poets uses Buttondown ("round robin style", as I've been informed) to send over drafts to edit & critique; a food truck sends out weekly locations & specials.

One common thread that I sort of intentionally _didn't_ handle for a while was _sending emails with files attached._ This is not a particularly hard technical problem, nor is it a tricky UX problem — the core flow of dragging and dropping a file into an editing interface is all but solved!

The thing that gave me pause for so long was less the surface-level implementation and more the so-called "long tail" of choices and impacts that adding attachments would entail. Should Buttondown add tracking on downloads (so an author can see who's downloaded what?)? How many attachments are allowed for a given email? What happens to attachments after they've been removed from an email? And so on. At risk of turning this anodyne blog post into a bit of a therapy session — this is what building software is like. Even the "easy" things come with hundreds of little microdecisions along the way, and I want to make sure that I get as many of those microdecisions _right_.

Anyhoo, after hemming and hawing for a fairly long time — **attachments have arrived.** They are exactly what I think you might expect they are: drag and drop, no fuss or muss. There's no tracking or anything too-clever-by-half happening. Check it out:

<br />

<div style="position: relative; padding-bottom: 81.63265306122447%; height: 0;"><iframe src="https://www.loom.com/embed/95ef3cb98fee43c980443f6ada70dc4e" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"></iframe></div>

<br />

And when you attach a file to your email, it's downloadable on the web archive version too.

So — send your emails, with great care and joy and now also with as many (well, up to three) attachments as you'd like.
