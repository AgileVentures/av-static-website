---
title: AV Website Review Part 11
date: 2017-07-19
tags: 
author: Sam Joseph
---

So I've got some suggested channel greeter bot text from at least one project maintainer.  I'm also running behind today, and I'm trying to get the websiteone sign up changes deployed ... I checked that the recaptcha sign up works on staging, which it does, and I filed a bug report about the double sign up message:

[https://github.com/AgileVentures/WebsiteOne/issues/1743](https://github.com/AgileVentures/WebsiteOne/issues/1743)

I stabbed around with the hangout and YouTube notifications and am still not getting consistency, but the video play is still working in all the right places.  I'm not seeing the Jitsi buttons on develop, which makes it seem like we had a deploy fail, but I can't see that in the semaphore history:

![](https://dl.dropbox.com/s/ssmcw84gzal6u43/Screenshot%202017-07-19%2010.02.41.png)
 
 ah, but they're visible on the events, not projects.  Okay, that looks good:

![](https://dl.dropbox.com/s/90dboy3bg5qmm82/Screenshot%202017-07-19%2010.01.29.png)

Well we might want to resize and/or provide some sort of flow here so that it's clear what people should do.  What we could have is detection of the button press, and then we could ping Slack that someone's joining the hangout.  Okay, I think the plan should be to get the recaptcha stuff out onto production and tomorrow maybe I can review the site flow experience.  

I'm also tempted (though I'm over time) to try and get the project greeter bot able to mention the new user name ... and I did - though not deployed - sent preview to project maintainer for approval:

![](https://dl.dropbox.com/s/41xijwjt9186nx0/Screenshot%202017-07-19%2010.28.18.png)

I'm just worrying if it's a bit long ...
