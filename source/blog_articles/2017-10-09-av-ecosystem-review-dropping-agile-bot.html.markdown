---
title: AV EcoSystem Dropping Agile Bot
tags: 
author: Sam Joseph
---

![Clouds](../images/clouds.jpg)

Right, so, on Friday I tested the new rubyfied Agile bot on staging, and it appeared to work fine, posting messages to both Slack and Gitter.  I thought I had made a list somewhere of the other things I really wanted to sort for this code.  Can't find them in blogs, issue or pull request.  I did have some TODOs in the codebase.  I did get pretty solid unit test coverage (I think) and I set up something to switch in and out from the testing slack channels to the live slack channels (with real people in).  I've got the LIVE_ENV, Gitter and Slack env vars set on production, so in principle this is all good to go.  I kick the site around on staging a bit and notice the past scrum embedded videos aren't playing - same on production.  I get distracted going to youtube to ensure that our videos are "embed-enabled", and that fixes that for the more recent videos.  Okay, so we need to make the default settings there "embed-enabled":

[https://support.google.com/youtube/answer/2660027?hl=en-GB](https://support.google.com/youtube/answer/2660027?hl=en-GB)

but basically this new bot needs testing in production and I need to see if it pings correctly for this morning's Martin Fowler scrum, although my internet connection has just gone down ... urgh ... I'm a bit late to the desk today, but I'm excusing myself that I paired a Premium F2F trial yesterday (Sunday) and occasionally I've got to give myself a bit of a break, but what I really want to do today is at least start the text change for agile bot that will provide more welcoming messages in Slack.  My internet comes back on and I'm distracted by not being able to sort this embedding issue:

[https://github.com/AgileVentures/WebsiteOne/issues/1879](https://github.com/AgileVentures/WebsiteOne/issues/1879)

but let's do this deploy!

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (1852_support_sponsored_users_adding_new_card_for_premium)]$ 
→ git checkout staging
Switched to branch 'staging'
Your branch is up-to-date with 'origin/staging'.
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (staging)]$ 
→ git pull origin staging
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
Total 0 (delta 0), reused 0 (delta 0)
To http://github.com/AgileVentures/WebsiteOne
   724012d8..84eab076  master -> master
```

right, that's on the way to master (assuming the CI passes).  I created a ticket for the change to the messaging I want to make:

> The google hangout URLs are really ugly - hoping we can improve the post to slack to something like:

```
Martin Fowler Scrum - all welcome - discuss any project! :-) click to join
```

> where "click to join" is hyperlinked to the hangout (or jitsy) or whatever

[https://github.com/AgileVentures/WebsiteOne/issues/1880](https://github.com/AgileVentures/WebsiteOne/issues/1880)

I'll start a vote on that in Slack, and turn off the PR notifications (since dependabot is so active) ... and I wonder if we should get every dependabot thing merged first before moving on to this Slack messaging improvement?  Well, it all hinges on how this production deploy goes and whether we have to fix a lot of things up.  Let's see if this is stable and then we'll decide.  I do want to jump several levels back up to review the ecosystem from the top down before the end of this week ...
