---
title: AV EcoSystem Review Return
date: 2017-08-24
tags: 
author: Sam Joseph
---

![ecosystem](/images/ecosystem.jpg)

I'm back from my travels and it's time to get back to work, jet-lagged or not.  I was working on my travels but eschewed blogging and hangouts, as is my usual practice.  My plan now I'm back is to keep focusing on the AgileVentures "ecosystem", by which I mean the website, Slack, our Social Media and all parts of how AgileVentures interacts with members.

One thing I did manage to do while away was improve the performance on the events page, so it now loads in under two seconds rather than in more than five.  I think that's an important step forward.  I'm very pleased with the changes wrought from my reviews in July, reinstating the password based signup (using Google's ReCaptcha to fight spam bots), a "getting started" page that is shorter and emphasizes our key projects, and a more pointed Premium members page. 

However, it feels like there is so much more to do on the getting started page before it really operates as a  scaffolded guide for those getting started in AgileVentures.  My hypothesis is that the majority of people take a look and see too much text, and don't really have enough of a guide to help them really get started.  My hunch is something that could remember the user and scaffold them on their way towards submitting their first pull-request on a project could make a big difference.  A quick glance over the Premium membership page leaves me feeling it also has too much text.

Of course it's easy to come up with hypotheses, move heaven and earth to change things around, only to discover there's not actually much impact.  The other thing I've been committing myself to is to measuring changes and testing alternatives.  My memory is that I made a few changes in July that I should be able to get some data on now.  However I haven't even managed to push those "blogs" out, so I need to dig back through my pull requests to find out what I was doing ...

There I notice that all my dropbox images are not showing up ... it looks like Dropbox has adjusted their file download so that images are served so that they won't display in the browser.  Unfortunately this will mean that all my blogs on our nonprofit static site will be without embedded images ... or so I would have thought, but actually they show up there, meaning this may well be GitHub markdown specific. Gah, talk about yak-shaving.   To see my previous blogs with images I need to move all the image files around in git.  Well, until if/when GitHub fixes then it will affect folks ability to review my drafts so let's get it fixed ...

Okay, so that took about five minutes and now I can see my first website review blog with images here:

[https://github.com/AgileVentures/av-static-website/blob/av\_website\_review\_part\_one\_blog/source/blog\_articles/2017-07-05-av-website-review-part-one.html.markdown](https://github.com/AgileVentures/av-static-website/blob/av_website_review_part_one_blog/source/blog_articles/2017-07-05-av-website-review-part-one.html.markdown)

The first thing I should be checking up on is what effect we had from the changes I made to the "Getting Started" page.  The drop-off from that page for the period Jul 3rd - Jul 31st 2017 is (222 vs 595) 37.3% of total traffic; compared to Jun 4th-Jul 2nd 2017 is (344 vs 500) 40.8% of total traffic, which is an improvement, but not conclusive.  That change could be due to seasonal or random fluctuations. If I compare with the same period last year (72 vs 156) 31.6% we se a lower dropoff rate, for a much smaller overall number of site visitors ...

Frustratingly I can't easily do comparisons in on the behaviour flow page in Google Analytics.  Conversely in the chrome plugin I can see these side by side, although I seem to be seeing PageView rather than session data. (Sessions = Unique PageViews?) The plugin shows that overall traffic to the page was down, but the bounce rate dropped by over 28% which is a big change ...

Okay, I'll need to go through all this again tomorrow when I'm not jet-lagged ... apparently bounce rate is not the same as drop off:

[http://www.rankraiser.com/rankraiser/difference-between-google-bounce-rate-and-drop-offs/](http://www.rankraiser.com/rankraiser/difference-between-google-bounce-rate-and-drop-offs/)
