---
title: Feels like Progress
date: 2016-09-15
tags: css mobile tickets cookies cucumber progress refactoring gems
author: Sam Joseph
---

Wednesday felt good this week.  My eldest son was cycling to high school by himself for a second day - an extra 40 minutes for me really coming in handy to catch up with administrative stuff.  The automation of the Thunderbird mailing allowing me to send 20 emails a day and have some nice chats with the AgileVentures members who respond.  Even more pleasing was the afternoon's programming.  Hadn't got to the static site in the morning; although I had got two reasonable clips of good pairing activities to share with TAs and instructors.  So I suggested to Michael we should try and make some progress on some small niggles on the static site.  Part of me was, oh, we should be pushing on with AgileBot, but I also wanted to put that down to settle and clear up some presentation stuff that was bugging me.

Blogging daily through the middleman-blog we have on [http://nonprofits.agileventures.org/blog/](http://nonprofits.agileventures.org/blog/) means I was seeing things like the font-size of html lists [feeling too small](https://github.com/AgileVentures/av-static-website/issues/48). Fixed that in a few minutes, and was straight on to dealing with a [presentation issue](https://github.com/AgileVentures/av-static-website/issues/46) identified by another AV member.  They'd also submitted a pull request which we reviewed.  It looked pretty good full size, but it seemed to break the mobile view.  Looking back perhaps we should have started debugging why it broke the mobile view, but it felt simpler and faster to just go with the initial suggestion of smaller font sizes from the original ticket, and kicking things around we ended up removing a couple of icons, and switching the text-align to centred.

Michael was saying that we'd spent time getting those icons in there, and that we should switch text-alignments from mobile to desktop views, and I think he was right, but I didn't want to spend a big chunk of time on that now.  I suggested we put a further refactoring ticket in and just deploy the fix we had, which was a basic improvement, not wonderfully elegant, but I wanted to move fast.  The narrative in my mind was let's move fast; avoid getting bogged down in too much discussion (usually my fault) and if something turns out to be trickier than we anticipate we should do the simple thing and open a new ticket for any more complex work.

Michael concurred and we switched drivers, and he fixed up the pagination for the blog posts.  The great thing about working with a static middleman site like this is that we can get fixes into production pretty much instantaneously.  In under an hour we'd fixed up three presentation issues with the static site, and I was already feeling more positive about pushing the site out on social media.  There was still a little swathe of content related work that I feel should be fixed before we really start pushing it to non-profits - linking it into the main developer site and so on.  I resolved to work on that during my admin period today.

Move fast, quick fix - push things off into other tickets.  What I really wanted to get to was this mobile presentation bug on the main developer site (AgileVentures WebsiteOne).  On the main site on desktop we have these floating call outs promoting different aspects of our mission:

![shout out on main site](https://www.dropbox.com/s/2e7z3nz6he927w9/Screenshot%202016-09-15%2008.56.23.png?dl=1)

Which look pretty good to me.  Unfortunately on mobile they end up looking like this:

![shout out on mobile](https://www.dropbox.com/s/z5te1eewurcalcq/Screenshot%202016-09-15%2008.57.46.png?dl=1)

Now I'm not sure if they always looked like that on mobile, or if that's how they ended up after some change in the CSS.  That design was all put in place before I took on the project manager role, and to be honest I don't spend that much time looking at web sites on mobile, but hey it's the future, right? :-) We've got to look good on mobile.  Freeranger had helped us fix up some navigation problems on the mobile view a few months back, which was a great improvement, and it was then that I'd noticed the layout issue.  Just me perhaps, but the lack of space around the shout out just feels crowded; looks wrong.  Maybe there are loads of other issues with our mobile view that I'm missing ... reminds me we have an issue that you can't click through on individual users on mobile, but anyway, what I wanted the shout outs to look like on mobile was this (more or less):

![shout on on mobile improved](https://www.dropbox.com/s/2jz1upu2fn34tpp/Screenshot%202016-09-15%2009.02.36.png?dl=1)

which turned out to be achievable with a pretty small change; specifically the addition on the `margin` elements in the following:


```css
div.section-well {
  padding: 1em;
  margin: 1em;
  border-radius: 10px;
  overflow: hidden;
}

@media (min-width: $screen-sm-min) {
  div.section-well {
    padding: 1em 2em;
    margin: 0em;
  }
}
```

So, job done, another micro pull-request pushed up, and I was feeling pretty pleased.  This presentation issue had been bugging me for months; a constant drag on my level of enthusiasm for promoting the site.  Ironically, I think casual viewers of the sites tend to be positive.  I guess it's easy to be over-critical of the things we work on; although there's the argument that the cumulative effect of many rough edges is its own drag on everything else you are trying to achieve.

So then we switched gear and Marcelo joined us from SF to go through a LocalSupport pull-request.  He had less than an hour, so we didn't really have time to get into pairing, but he and I had already async git ponged on a [related refactoring](https://github.com/AgileVentures/LocalSupport/pull/348), and then he had done the same with Marouen on an [alternative approach using a 3rd party gem](https://github.com/AgileVentures/LocalSupport/pull/359).  The full details are maybe worth another blog post on the evolution from hacky case statement fix, to our refactoring of that into something more maintainable, to the realisation that there was a gem that pretty much did what we needed.  In the session with Marcelo we reviewed all that, including the internals of the gem, and how the LocalSupport site used cookies and how the gem could be integrated with Cucumber stories.

It was great to hook up with Marcelo, but he had to dash off to work, and so Michael and I finished up with some clean up related to the Stripe credit card change feature, with Michael popping in a sad path to give the user better information when card update fails, and I dealt with some legacy data migration.  The day finished up for me with the Kent Beck scrum where Michael and I reviewed our possible next steps, which include further work on making premium membership more visible in WebSiteOne, improving the flow of the event pairing mechanism and increasing the resolution of the pairing tracking telemetry so that we can give our users more Karma for the scrums and pairing sessions they are involved in.  Like I say, it was a day where it felt like we were making some progress :-)


###Pairing Videos related to this blog:

* [Pairing on Static Site](https://www.youtube.com/watch?v=lbcH6Gqr-I0)
* [LocalSupport PR review](https://www.youtube.com/watch?v=e84n_wBP7_c)
* [Pairing on WebsiteOne](https://www.youtube.com/watch?v=nuyODPgitIk)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=60EREsMw00o)

