---
title: AV Website Review Part 12
date: 2017-07-20
tags: 
author: Sam Joseph
---

So I'll be taking a blogging/reviewing break for my Japan trip - perhaps I can get up to date pushing out some old blogs.  Over the last couple of weeks of these "reviews" of the AV website (and associated ecosystem) I've managed to get some changes out to the main site.  I think it's early to be assessing the changes - that should probably be the first thing to do in September when I plan to restart this series under the more accurate title "ecosystem review".  The main changes I've enacted so far are getting a privacy statement linked up and reinstating email sign ups with a Google reCaptcha system to block the spambots signing up.

I also simplified the "Getting Started" page and rewrote parts of the Premium page.  I can see there's lots more work to do there.  The Premium plan overview page is still very text heavy, as are the individual Premium pages.  The events page is loading really slowly at the moment.  Six seconds is not good. New Relic shows that the slowness is a split between the index method in the controller and the index.html.erb template itself:

![](https://dl.dropbox.com/s/pue0dkn4373lcbb/Screenshot%202017-07-20%2009.37.31.png)

I'm conflicted about whether to investigate that further, or try to deploy the new greeting bot with the new functionality and updates for the other project maintainers ... and I've gone for deploying the new project greeting bot.  Here's a maintainer testing it live in his main project channel:

![](https://dl.dropbox.com/s/ov99d3ogm4jq9lw/Screenshot%202017-07-20%2009.59.55.png)

And so the thing I need, to profile the events controller, is to clone the production data locally again, which I also did after opening some more project greeter bot tickets:

https://github.com/AgileVentures/project_greeter_bot/issues/15

I can't immediately see a solution to the slow event page loading, but I've found our old open issue and added notes there:

https://github.com/AgileVentures/WebsiteOne/issues/1503

I've got other fish to fry today, e.g. getting the NHS HLP wiki ssl certs renewed.  Gosh I'd love to be paid at market rates for keeping the AgileVentures ecosystem running ...
