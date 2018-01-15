---
title: AV EcoSystem Review Making Events Welcoming
tags: 
author: Sam Joseph
---

![welcoming](../images/welcoming.png)

So I have the new code on staging to avoid reposting old YouTube URLs on Slack. So let's start by pushing that out to production:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (staging)]$ 
→ git pull origin staging
warning: redirecting to https://github.com/AgileVentures/WebsiteOne/
From http://github.com/AgileVentures/WebsiteOne
 * branch              staging    -> FETCH_HEAD
Already up-to-date.
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (staging)]$ 
→ git checkout master
Switched to branch 'master'
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (master)]$ 
→ git rebase staging
First, rewinding head to replay your work on top of it...
Fast-forwarded master to staging.
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (master)]$ 
→ git push origin master
warning: redirecting to https://github.com/AgileVentures/WebsiteOne/
Total 0 (delta 0), reused 0 (delta 0)
To http://github.com/AgileVentures/WebsiteOne
   a3521705..259467f6  master -> master
```

Hopefully that will be one less thing to worry about this week.  Now the other issue was the one pointed out by Will, that having the YouTube message being almost identical to the hangout link, which can be confusing.  We have a ticket for that:

[https://github.com/AgileVentures/WebsiteOne/issues/1921](https://github.com/AgileVentures/WebsiteOne/issues/1921)

I kicked off an asynchronous vote in the websiteone channel.  I'm conflicted about whether this is the thing to work on.  I really want our events to be as welcoming as possible, but there's so many potentially valuable things in the waffle board.  Let's at least start this one; although then again, maybe that's the wrong approach.  Perhaps I should be clearing out existing and stale tickets.  The challenge with this ticket is that I've put the welcoming message in the title of the main daily scrums:

> "Martin Fowler" Scrum and Pair Hookup - All Welcome :-) Discuss Any Project, Ask Any Question, Or Just Listen In :-)

and the YouTube slack ping is using the following ruby to generate the hyperlink:

```rb
message = "<#{video}|Video/Livestream for #{hangout.title}>"
```

so the entire title appears in the hyperlink.  Perhaps the solution would be:

```rb
message = "<#{video}|Click here to listen in to the Video/Livestream> for #{hangout.title}"
```

I'm caught between not wanting to have the welcoming message appear on everbody's scrum events, and wanting to be able to refer to the title separately.  

The message for the hangout link is as follows:

```rb
message = "<#{hangout.hangout_url}|#{hangout.title}>"
```

and maybe it should be 

```rb
message = "<#{hangout.hangout_url}|Click here to participate in> #{hangout.title}"
```

although I guess we'd acutally like the folks who want to listen in to actually join the hangout (as Will did) so that we can get them talking ... hmm :-)  Maybe a reasonable compromise would be:

```rb
message = "<#{video}|Video/Livestream> for #{hangout.title}"
```

Part of the issue is that it's difficult to judge it until one sees how it actually appears in Slack.  Which makes me wonder if I can run it locally for one of our Slack test instances.  In the background Heroku is having issues and I can't deploy yesterday's fix to production :-(

Thinking back about the AV ecosystem review philosophy I recall that my last idea starting from the ads we serve on Google is that we could be serving ads about the free access to mobs and f2f sessions.  We could just do that on Google, but we're talking about a signup form that allows us to track how many are signing up from particular campaigns.  Which is then another thing that sits on the TODO list and blocks us from using the Google ad revenue from working on something that might actually lead to new sign ups for paid services, which would be obvious whether we are tracking how many people signed up for them or not ... could we be tracking the social media likes and follows?

Other things are:

* performance issues on the site
* tawk.to site integrated chat system
* replacing the mercury editor so we can move to Rails 5
* supporting private events
* looking at long running cukes
* hosting our own jitsi
* making the main page counter dynamic
* notifying the project creator when people join a project
* having getting started page tick off progress
* better modelling of premium users
* allowing premium users to change payment methods
* start hangout button not working ...

I don't think I've experienced any 500 timeouts on the events pages since we rolled out the the icecube upgrade.  I'm conflicted about tawk.to after seeing OdinProjects lovely gitter integration.  Replacing the mercury editor feels like a big job.  Corey seems to be stalled working on private events.  The cukes are an ongoing bleed.  I should look at jitsi with Sigu ... making the main page counter dynamic would probably help us look more professional.  Notifying project creators when people click to join their projects could help connect people, as could adding some progress tracking to the "getting started" pages.  Better modelling of all the Premium users would probably make my life easier and support better predictions, while allowing Premiums to change payment plans would help money flow.  The Start Hangout button not working is a nasty fail point for newcomers trying to pair - particularly those starting the MOOC ...

Gosh, I'm really not sure what to work on and the time change means the scrum is coming up soon.  I think I'll have to leave it there today.  I really do want to make events more welcoming, but I think I need to spend some more time reflecting on how to divide up my labour ...
