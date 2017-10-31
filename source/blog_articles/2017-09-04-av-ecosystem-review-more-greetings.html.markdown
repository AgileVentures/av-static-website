---
title: AV EcoSystem Review More Greetings
date: 2017-09-01
tags: 
author: Sam Joseph
---

Got my jogging and podcasting/tweeting done this AM, but kids still off school, which means I'm late to the keyboard.  Lots to do - low hanging fruit is probably other changes to the greeter bots.  The change I rolled out on Friday seems to be working; that's the quick fix to ensure that channel names are hyperlinked in messages:

![](images/Screenshot%202017-09-04%2010.16.25.png)

So I guess I should get that merged into master, and I can probably kick through the other minor changes there ... I've merged the license and code of conduct pull requests that I had outstanding.  So I punted on the code of conduct in the greeter bot, posting the following to our AV community channel:

> we've been discussing codes of conduct before and I know that various projects have them now, following GitHub's lead of making them really easy to add.  I haven't heard any objections yet, and I just added them as a recommendation in our "Getting Started" documentation.

> I'm planning to add mention of them to our Slack greeter bot https://github.com/AgileVentures/greeter_bot/issues/6 but I thought I'd just check in with the community again before moving on that.  Would be good to discuss in a community meeting - which perhaps we should move to Fridays ... anyhow more on meeting times soon, but very interested in any input anyone cares to share here, or feel free to DM me directly :slightly_smiling_face:

And I got hyperlinks to our Premium mob offer and the user name in the main greeting bot, and pushed those live from a branch. I'm really promising myself that the next step is testing!

But before that I can't help myself but try and get the names and channels linked up in the project greeter bot, so I went ahead and did that and ended up doing a quick manual test on the now un-used AV mentors slack instance, that allowed for a quick round of debugging.  Gosh I'm really not setting this up to allow for easy-onboarding of others - will need a big documentation effort to get folks into the new AV bot testing slack instance ... can't help myself but get these quick fixes out first - actually make it so that there will be the equivalent of a ping to the project maintainers on channel join - or at least see if hyperlinking their name in the bot post has that effect ...
