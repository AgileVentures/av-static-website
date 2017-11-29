---
title: One Step Forward
date: 2017-05-03
tags: podcast, Slack, wiki, NHS, MediaWiki, Visual Editor, design sprint
author: Sam Joseph
---

![one step](/images/one_step.jpg)

So I got distracted by Slack and email again this morning.  Wanting to post my last podcast listen into #av\_community\_talk I ended up replying to freerangers last post.  At least today I think I was calmer, and there wasn't so much to catch up with in all the incoming communication channels.  I'm definitely struggling with anxiety about getting everything done.  I'm less anxious this morning after completing the first round of wiki training with some of the HLP core team yesterday.  The big thing was that the MediaWiki visual editor ultimately loaded on three different NHS laptops and networks, and all the trainees succesfully created edited pages as well as got set up with moderation permissions.

We never managed to really complete important parts of the design sprint we started a month ago.  The project time and budget wasn't ever going to allow for custom development of a bespoke solution, so we were reduced to a process of reflecting over existing off the shelf solutions.  We'd "sneaked" in the customer testing phase as part of training everyone to use the selected system.  We've also failed to "prototype" in the pure design sprint fashion by using a real software package (MediaWiki) and having got that set up and working.  I think the design sprint concept assumes that there's budget and time, following the sprint, to properly deliver the solution to the end users.  A critical part of the overall question for us was whether we could get *any* reasonable solution working on the NHS laptops and networks, and the result from yesterday appears to be yes.

I ran an hour long training session and had the trainees try things out for themselves (as we might do in a usability test) before reviewing how things should be done (if they got stuck).  I learned a lot, including:

* initial sign up confirmation emails were going into some junk mail folders
* the sign up process with the ConfirmAccount module was rather burdensome
* the visual editor was working in our custom instance on NHS laptops/network

That last element might seem trivial.  One might be tempted to say, well Wikipedia's visual editor worked fine on NHS laptops/network, so of course the same visual editor on our own server will work fine, but if working with computers has taught me anything it's that you really musn't assume that things work as you expect.  Particularly since we moved to MediaWiki after a selection of other high profile enterprise and open source wikis completely failed to load critical icons on the NHS laptops/network, which rendered them completely unusable.

So I've got a series of additional training events to run next week, and in the meantime we've got a soft launch scheduled for tomorrow.  To support that we need to get a good subset of the following sorted:

* backups on our MediaWiki instance
* ensure we have a fixed IP -> okay because we are CNAMing to the bitnami name ...
* get SSL set up on our MediaWiki instance
* consider whether we remove the ConfirmAccount plugin (is the Moderation plugin sufficient by itself?)

Other things we will need to do before next week include

* getting the watch list email notifications working
* create a training video
* consider whether to add Wikipedia style moderation templates

I could easily spend the whole rest of the week completely focused on this, but I've also got to re-record AV102 videos to reflect the changes to the Google Hangout API, and ensure we've made all the final accessibility changes on the MOOC which goes live next week.  There's also reviewing all the pull requests for the different AgileVentures projects, and the process of following up with Premiums. I have finally started creating individual private Premium channels to try and distribute the load of supporting the increasing numbers of Premium members.  We can do it, yes we can!

### Related Videos

* ["Martin Fowler" scrum](https://www.youtube.com/edit?o=U&video_id=4oBrjn-9S3o)
