---
title: AV Website Review Part 7
date: 2017-07-13
tags: 
author: Sam Joseph
---

So overnight some new folks signed up for the AgileVentures slack.  It seems like the greeter bot on Azure/Dokku has succesfully greeted a handful of new members, so I've turned off the greeter bot on EC2 and shut down the instance, which should save us $5 a month, or so.  Look after the pennies and the pounds will look after themselves they say.  Lots of pennies still to save methinks.  Anyhow, my calculated gamble seems to have worked.  We got one user having a double greeting in the changeover, but they've actually responded positively and, otherwise, normal service has been restored.  So now in principle we can have simpler management of the greeter bot message.  Federico and others can submit PRs to update the message, and even deploy it themselves.  I guess I should just test that with a new message ... so I put in a pull request:

[https://github.com/AgileVentures/greeter_bot/pull/3](https://github.com/AgileVentures/greeter_bot/pull/3)

merged that and pushed that up to dokku from the command line:

```
→ git remote -v
azure-production	dokku@agileventures.eastus.cloudapp.azure.com:greeterbot-production (fetch)
azure-production	dokku@agileventures.eastus.cloudapp.azure.com:greeterbot-production (push)
origin	git@github.com:tansaku/greeter_bot.git (fetch)
origin	git@github.com:tansaku/greeter_bot.git (push)
→ git push azure-production master
Counting objects: 4, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (4/4), done.
....
-----> Shutting down old containers in 60 seconds
=====> fc005e593ed37166e80f04c0b0093f367771bff7aa055e4bc2900fa8a9a2850b
=====> Application deployed:
       http://greeterbot-production.agileventures.eastus.cloudapp.azure.com

To agileventures.eastus.cloudapp.azure.com:greeterbot-production
   f7db8d3..53ef0fc  master -> master
```

so of course ideally we'd have a staging instance which was pointing at a different Slack instance to check that was all working and tests wrapping different parts of the system, but I'm doing my usual "get the round trip working first".  I took the liberty of pushing out the Premium Mob special offer on both twitter and facebook, and I'll now happily wait to see if the next person who joins Slack gets the updated message.  The code change is so small that it feels pretty safe.  There may be an issue with how it formats in Slack, but I quickly tested that by pasting the message in, and this slackbot seems to get it's hyperlinks auto linked in the way expected.

So I guess that's a short one today.  In parallel we're starting to have some tiny traction from having added Chatlio to the site.  I'm just talking in #new_projects to a new member who first contacted us on Chatlio ... they were reaching out to me in DM and I got them to ask their questions in #new_projects:

> I am just curious to understand about Agile Ventures as an organisation like:

> Who decides on whether a project would be accepted by Agile Ventures or not?

> Along with a charity aspect, can the project also have a commercial angle to it?

> I had always felt a need for better products in education sector and obviously, technology is the enabler for it. I am just trying to think Loud and just wondering if I come up with some products which at present is just at an idea stage, will I be able to take that idea to product stage using a platform like yours?

> Once the idea reaches proof of concept, may be then we could use platforms like Kickstarter etc to raise funds?

> Anyway thanking all of you in advance for taking time to read  this------

I responded:

> Ultimately the decision about whether a project is accepted into AgileVentures is up to the charity trustees and charity members

> we have a charity constitution with objectives of 1) open education accessible to all AND 2) helping charities, non-profits and other socially beneficial causes

> the process usually starts with someone proposing a project here and us discussing whether it's appropriate - if you scroll up you can see some examples of that

> projects do occasionally have commercial angles to them - I believe #take_me_away was for a normal business where the owner had agreed to make the project completely open source for learning purposes

> where we'd have difficulty is supporting a project that was for a for-profit business where the code was close-sourced.  It might still have educational benefit to the volunteer coders, but really developers should receive payment for that sort of work, and while we're happy to help our members make connections with private businesses, we can't really support those projects directly

> you're most welcome to brainstorm product and project ideas.  Our platform could certainly help you take that to the product stage given commitment to open source and open development so that all our members could benefit from the experience, and especially if you're committed to non-profit and charitable objectives

I do wonder if we need a more formal route for project proposals.  That's another part of the website ecosystem - in the first instance I'm going to keep focusing on the onboarding route for new members.
