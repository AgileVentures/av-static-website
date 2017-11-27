---
title: Distracted Operations
date: 2017-05-02
tags: podcasts, blocking, overloading, debate, Slack, Google Hangouts, NHS, wiki, moderation, node, Parsoid, MediaWiki
author: Sam Joseph
---

![distractions](/images/Distractions.jpg)

So I'm getting to the blog phase late today.  I had a backlog of a couple of podcasts to tweet, but one of them seemed particularly relevant to the discussion going on in #av_community_talk.  I'd been keeping myself away from the discussion in order to focus on preparation for the NHS Wiki project training and updating the online courses.  I thought I could get away with just posting the podcast into the AV community talk, but I ended up reading, and then replying to lots of little elements.  I was definitely being reactive, and reading on, it was clear that others were doing a good job of making many of the points I was making, so I probably didn't achieve much more than blocking and overloading folks, which I'm supposed to be getting better at not doing, oh well :-)

As a promiment member of AgileVentures the danger is that my input ends up quashing debate (and problem-solving) rather than encouraging it.  We'll see.  There's at least one member whose assertions always seem to get my goat, and I just can't help getting into a debate.  On reflection I think I would have been better off reading the full discussion tomorrow and perhaps writing a blog post about it. At least with the new threaded reply system, maybe my input will not be as intrusive as it otherwise would. Either way, I'm now behind on my day's schedule and have yet to write up what I did yesterday.

Yesterday was the May day bank holiday in the UK and I stayed off Slack and Hangouts and spent the full day getting all the moderation and visual editor components set up on the NHS wiki, ready for a training session with NHS staff later today.  It took a good two hours to get the visual editor system working, which involved setting up a node service called "Parsoid" and opening up an additional port in the Azure system we're hosting on.  Ultimately the [Parsoid troubleshooting](https://www.mediawiki.org/wiki/Parsoid/Troubleshooting) was really helpful, and I'm getting a stronger sense of the MediaWiki extension ecosystem, with some projects on GitHub and some not (MediaWiki has their own code review thing called Gerrit) and the wiki talk pages on individual extension pages seems to be the place to find out tips & tricks, and also the place to report bugs and receive help.

By the end of the day I had a 30 page keynote with step by step screen shots of the following for the wiki:

* Basics
  - Where to go to access the wiki
  - How to request an account
  - How to search to find a page
  - How to create a page
  - How to edit a page
  - How to add a link to another wiki page
  - How to upload an image
  
* Moderation
  - Approving account requests
  - Receiving change notifications
  - Approving changes
  - Rejecting changes
  - Moderation List
    - Banning repeat offenders
    
And I'd managed to tweak the wiki to change the name of the homepage, get references working and add a fair bit of additional content.  Basically ready for today's hour long training session with a few core NHS HLP staff.  There are a set of other things I'd like to do, such as getting the whole thing on SSL (will that play well with VisualEditor/Parsoid?), installing Wikipedia moderation templates and replicating more of the HLP website look and feel.  The MyWiki folks have set up a visual editor enabled instance for us and said they'd look at getting the Moderation and ConfirmAccount extensions set up.  Maybe they're ultimately going to be faster and easier, but if I hadn't set up our own system I wounldn't have been able to work through and generate training documentation on the moderation process, so I think that's time well spent :-)

Then I spent an hour updating the pair programming functionality in the AV102 course and the first part of the MOOC to switch back to our older optional style and start to work through some of the accessibility issues.  There's still more to do, but that's progress there.  The critical thing today is to record some new videos about the way that hangouts and events are working on AV.  Need to get that done before I head off for this training event.  More on that in tomorrow's blog :-)  Unless I get distracted by Slack ...
