---
title: AV EcoSystem Review Headaches
tags: 
author: Sam Joseph
---

![headache](../images/headache.jpg)

I'm feeling a bit rough - maybe coming down with something.  Bit of a headache at the computer.  Have burnt early start on handing off the AgileVentures sticker send out.  Will it actually make sense to make a shop front where folks can buy stickers and other AgileVentures swag?  Would that ever he anything other than a trivial source of revenue?

Just had to stop to put on warm clothing as it cools down here in the UK.  Headaches literally and figuratively. Drink lots of water and ease myself into the day.  So, heroku had issues all day yesterday and I *think* we deployed the fixes for old youtube links being pinged, but we still had the same problem in the Kent Beck scrum yesterday afternoon.  I had deployed manually and got an odd message at the end of the deploy:

```
remote: -----> Launching...
remote:        Released v296
remote:        https://websiteone-production.herokuapp.com/ deployed to Heroku
remote: 
remote: Verifying deploy... done.
error: Could not read c93108679daff7e04ea8d6408b172c1aed987389
fatal: Failed to traverse parents of commit 600ecd2a36175294d8ba8a18e8b0f0c318402487
error: failed to run repack
To heroku.com:websiteone-production.git
   a3521705..259467f6  master -> master
```

I guess the deploy worked - would be nice if we had a page somewhere in the site that told us what commit it was on.  Actually Heroku shows us that and it does seem like it's deployed.  So is the fix I thought I had applied not working in production ...? I guess I see how it goes today and then circle back on it tomorrow and do a local test with production data if it's still an issue.

So the real headache is choosing where to focus next.  I generated a big list of things yesterday from reviewing the WebSiteOne waffle board.  There are good reasons for working on any number of them.  But maybe the backlog of PRs from dependabot and open stuff in the waffle board is the real challenge?  What I could do with is, say, five other volunteers working on this semi-regularly with me.

My inclination is to say that we need to push some of our AdWords budget at promoting our mob programming sessions.  And I don't want to wait to have created a sign up form.  We are doing sign up via people following us on twitter and liking our page on facebook.  The target can be having people click through to our social media links.  I'm also frustrated that I'm not feeling more comfortable with Google AdWords.  It feels like it should be a simple thing to use, but somehow my mental model is not quite there.  Part of me thinks I need to read up on Adwords, or take a course, but I have terrible trouble focusing on stuff I'm not interacting with, things that I can't interactively test.  It's like I'm deeply suspicious that anything written down will not be up to date.

Anyway, so I start by creating a Google Ad Campaign.  Immediately there are choices between "Search Network with Display Select" and "Search Network Only".  I read an article about the difference, but have no real way to judge the value of one over the other to us, so I go with "Search Network Only" which is what all our current campaigns are.

![](https://dl.dropbox.com/s/79slyq3k2slwmsl/Screenshot%202017-11-01%2010.15.04.png?dl=0)

I throw in our landing page and some random keywords:

![](https://dl.dropbox.com/s/y4ngbqedas629wf/Screenshot%202017-11-01%2010.18.22.png?dl=0)

And then I get the view I love, the one where we can see the form of the ad:

![](https://dl.dropbox.com/s/j399vz9voklwg3c/Screenshot%202017-11-01%2010.20.34.png?dl=0)

So now we have a campagin that is ready to go.  I'm not actually sure how to kick it off, maybe it's started already?  I'm not sure how we set the targets for the campaign.  I'd like success to be folks clicking through to http://facebook.com/agileventures or http://twitter.com/agileventures.  I find that there's a process of creating conversions, which seems to involve javascript.  Not sure that will play well with out mercury editable pages.  Okay, I'm going to leave it there for today and see if I can engage the rest of the marketing team on this.

what I would like to do is add a video of an example session to the premium mob page, which I do and then I go to make coffee.  Still on the decaf - could use a hit of real coffee about now, but not sure that will help in the long run ...



