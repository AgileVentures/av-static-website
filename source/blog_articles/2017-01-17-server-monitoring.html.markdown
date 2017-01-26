---
title: Server Monitoring
date: 2017-01-16
tags: GitHub, DevOps, monitoring, Heroku, NineFold, migration
author: Sam Joseph
---


So I'm still forcing myself to think about DevOps in preparation for my talk on Thursday.  Other things being equal I'd be blogging about our new marketing activities and how Google will potentially give us $10,000 a month of AdWords, and how there might be similar, if less generous, offers from other social media sites.  However, it felt great yesterday when I managed to complete a first draft of the slides for the talk.  There's still a little something missing though; the performance requirements for the different systems.  

What I do know from the metrics we get from Heroku on WebSiteOne is that we regularly exceed our 500MB memory allowance, although the site performance doesn't seem too horrible.  We've gone in and fixed particularly slow loads on the users, projects and event pages, as well as had a stab at removing gems that seem to be eating up a lot of memory.  We've improved page load times, but not managed to get the memory usage down.  We could pay to upgrade to the next Heroku tier to get 1GB of memory, but the site performance seems superficially okay - and no one is explicitly complaining about it.  We know that pages need to load as fast as possible in order for people to spend any time on the site. Greater than 4 seconds is likely a big turnoff these days.  The front page appears in about 1.2 seconds when I refresh in Chrome.  Of course it would be slower if not cached.  In incognito mode the entire front page loads in about 2.6 seconds.  That seems acceptable, but I do have a reasonably fast mac.  It might be the case that other folks around the world are experiencing poor performance and we're not aware of it ...

It seems like NewRelic wasn't actually enabled on WebSiteOne production.  The code was all set up, but the NEW RELIC key wasn't in the production settings.  I've added that now so perhaps we'll see some interesting data.  NewRelic is all set up for LocalSupport, where we can see an overview of the web transaction times:

![NewRelic data from HarrowCN production](https://www.dropbox.com/s/myb72nmofhmm7lg/Screenshot%202017-01-17%2010.22.04.png?dl=1)

Ping requests haven't been working for the last 12 days, and aren't working from the command line either, but the site is up and it looks like pages are being served okay.  I can see a couple of page loads that failed in the last seven days, which is probably acceptable for this low traffic site.  Even if it wasn't, I'm not sure how to go from that failure to a fix.  Superficially it would just mean provisioning more dyno or more memory.  At the moment LocalSupport is on a legacy dyno from Heroku, meaning we aren't getting metrics on memory usage as we are for WebSiteOne.

While I've been writing this, the NewRelic data on WebSiteOne has started coming in:

![NewRelic data from WSO production](https://www.dropbox.com/s/s5x799dlbdx4l7z/Screenshot%202017-01-17%2010.28.42.png?dl=1)

This makes it look like there was at least one frustrated request in the last half an hour at least.  That makes me think fixing our memory issues on WSO might be higher priority.  Looks like some kind of active record lookup was getting stuck ... Going back to HarrowCN, it seems like I can embed a [feed of the throughput](https://rpm.newrelic.com/public/charts/7odyrRrzsqR) in an iframe:

![HarrowCN throughput](https://www.dropbox.com/s/zzyev88mr4orwbn/Screenshot%202017-01-17%2010.33.07.png?dl=1)

The baseline requests look like some sort of healthcheck is going on, rather than it being representative of human users ... more investigation required.  I wonder if that's a side effect of NewRelic or some other pinging service we've forgotten about?  Anyhow, I want to get deeper into this for both WSO and HarrowCN (and AutoGraders/MetPlus) to flesh out what I can say about the usage of our systems in my talk on Thursday.  Let's see how far I get ...
