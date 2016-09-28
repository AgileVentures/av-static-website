---
title: Fallow Day
date: 2016-09-28
tags: tickets sinon mocha node express tutorials slack voting planning poker
author: Sam Joseph
---


Today was a fallow day.  I'd been knocked back with a fever, sore throat and tummy trouble from the latest virus doing the rounds at my kids "Virus Exchange Camps", ... sorry, I mean schools.  I've not idea if anyone else talks about fallow days, but there's this expression about leaving a field to go fallow.  In crop rotation you plant different crops in each field each year and then don't plant anything every so often to avoid exhausting the soil.  I came up with the expression during my Ph.D. to mark those days when I didn't seem to get anything productive done - usually because I was sick, or more commonly, slightly hung-over.

So I had spent most of the morning asleep, and didn't have the energy to pair, did a bit of admin work - catching up on a few long outstanding things - and later in the day I managed to spend a little time coding in node/express/mongo to see if I could get a little something set up that might form the basis of the async voting bot, or planning poker agent that we had been thinking about.  Let me explain, planning poker is this process of simultaneously voting on the complexity/difficulty of a task in a meeting.  So the task might be, add a new data report to the statistic page, and you might have 5 people and they would do like rock, paper, scissors with their fingers, except with numbers, e.g. 1, 2 or 3 and then the five people would all show their fingers at the same time and you'd get votes for say 1, 2, 2, 2, 3, and then you might ask the people voting against the majority why they thought that things were more or less complicated and then after some limited discussion you might revote or the outliers will agree to match the majority.

The value generated from that voting can then be attached to a ticket associated with an issue, and then that gives someone who's going to start on that task an idea of what they are taking on.  It also allows tools like Pivotal Tracker to help with estimating when a variety of tasks will be completed.  Another central part of the voting is that it exposes differences in assumptions between team members, and provides a structured way to get to the bottom of them relatively quickly.  We've been doing online synchronous voting in AgileVentures projects for a long time.  We tend to use a hangout to coordinate the meeting and then do the simultaneous voting in chat.  You can often see the traces of this in the different AgileVentures chat rooms on slack, particularly since we realised that the Google Hangout chat didn't persist.

Scroll forward, there are some projects where it's difficult to get people together at the same time, even in a hangout.  Also voting on a load of tasks one after the other can be somewhat tedious, so we're now experimenting with asynchronous remote voting, and for the last two weeks I've been playing the part of a hypothetical chatbot for the LocalSupport and WebSiteOne projects where I post something like this in the relevant Slack channel:

```
@channel new async vote on "make doit mentions in map key be hyperlinks" https://www.pivotaltracker.com/story/show/122461371, discuss here, or in ticket and then DM me your vote of 1 2 or 3
```

and then I'll get individual DMs from developers like so:

```
Vote for https://www.pivotaltracker.com/story/show/122461371:   1
```

or sometimes with a bit of explanation

```
https://www.pivotaltracker.com/n/projects/742821/stories/122461371 seems like a 2, because it requires a bit of front-end and a bit of back-end, which means testing on both those fronts too
i’m not sure who wants to do this story, but my coworker seems interested in pairing with me on it
```

or 

```
I guess the hyperlinks issue  for "do-it" is not your run of the mill "link_to" type. It will involve JS so if that s the case I say 3.
```

and there might be some discussion in the ticket itself, but as I get votes I'll post into the slack channel like so:

```
@here we have 2 votes for "make doit mentions in map key be hyperlinks" https://www.pivotaltracker.com/n/projects/742821/stories/122461371
```

I might also prompt the channel if we don't get any updates for a while:

```
@channel we have two votes on https://www.pivotaltracker.com/story/show/122461371, we need one more vote to move forward - discuss here, or in ticket and then DM me your vote of 1 2 or 3
```

then once I have a sufficient number of votes (currently 3) I post the results to the channel:


```
@channel vote on https://www.pivotaltracker.com/n/projects/742821/stories/122461371 "make doit mentions in map key be hyperlinks" complete - @decareano 3, @johnnymo87 2, @marouen 1 - I'm just imagining that this is just a simple `link_to` or am I missing something - @johnnymo87 @decareano any thoughts on why this is more complex than a 1?
```

and then we have a discussion in the slack channel (or it could be in the ticket):

```
@tansaku, did you read my comment in my vote? I had the same thought as @marouen but in reverse. Anyway, if it's a link_to my vote is 1.
```
```
@decareano thanks for your vote - we now have two votes of 1 for https://www.pivotaltracker.com/story/show/122461371 "make doit mentions in map key be hyperlinks" and a 2 from @johnnymo87 - @johnnymo87 happy to revise to 1 or is there some aspect of this we've missed?
```

```
oh, i thought that part of the UI was done in js. But I looked it up, and it’s not. I’ll revise to a 1, @tansaku
```

Then I set the estimate on the ticket and we start again:

```
okay @here so that story is unanimous as a 1

@channel we have a new async vote on https://www.pivotaltracker.com/story/show/131062023 "Refactor Build marker service" - please DM me 1 (simple), 2 (medium) or 3 (hard) - discussion in ticket or here as you prefer :slightly_smiling_face:
```

So that was kind of a trivial example, but actually that vote did expose some misconceptions about the ticket in question.  I think these async votes are quite good for those projects where the team members can't make the synchronous hangouts.  There is a fair amount of logistical overhead for me running it (which could be automated into a bot) and also since I'm the "bot" it's a bit unfair for me to vote.  If we had this all automated I could vote too, and things could proceed a little faster.  Although of course we have to consider the time it takes to automate, and whether the automating would throw the baby out with the bathwater.

Others have already expressed concern that the asynchronous nature of the vote would remove the critical discussion portion that happens in the synchronous meetings.  It may be we are losing something, but by running this demo with me as bot, we certainly continue some level of discussion.  Another concern is that switching to a bot people would not be as expressive to the bot as they are to me as bot.  A sort of human-y thing.  One solution to that might be to dress the bot up as me, which is what we do for pinging hangout notifications where the bot takes on the logo of whoever starts the hangout.

Unfortunately I think slack still does not support bot DM-ing.  At least you can DM, but then all the responses go to the slack bot channel - you can't quite continue a one on one conversation with a bot.  Anyway, the process has also allowed me to tweak the "interface" in that chats with developers have raised a few points:

1. Always include a link to the ticket in any post
2. Always include the title of the ticket in any post
3. It's helpful to include instructions for newcomers like: "please DM me 1 (simple), 2 (medium) or 3 (hard) - discussion in ticket or here as you prefer"

and I've validated that the functionality is getting some use.   So with all that in mind I started looking at test driving a Node/Express app.  I'm pretty sure I could write something faster in Ruby/Sinatra, but there's also this idea that we need to diversify the apps we're using in AgileVentures to appeal to a wider range of developers.  Also after our AgileBot experiences, I'm keen to see whether stripping away Hubot and CoffeeScript, whether a plain Node/Express app will be more manageable.

Now I've TDD'd Node/Express before but it's been a year or so, and I want to be up with the best practices so I was having a look around for tutorials and things.  I found:

* https://devcenter.heroku.com/articles/mean-apps-restful-api
* https://semaphoreci.com/community/tutorials/a-tdd-approach-to-building-a-todo-api-using-node-js-and-mongodb

Now interestingly the heroku one was appealing because getting something up onto a stable endpoint is critical, but it wasn't TDDd, so I went for the latter, which I ultimately got working, but was a bit disappointing, because it wasn't actually TDD.  It had tests, yes, but the functionality was not test driven.  I notice now that SemaphoreCI is paying $200 a pop for people to write tutorials ... In the author's favour they did link to the complete code at https://github.com/rajzshkr/todoapi, which is what allowed me to get things working.  The tutorial itself was flawed on the following points:

1. different code from the working example
2. tests introduced after the code they were testing, i.e. not TDD
3. bits of code in tutorial not associated with particular files

As part of working through the tutorial I did get mongodb installed and running, discovered some [express debugging tricks](https://expressjs.com/en/guide/debugging.html), and spent most of my time working out that the Postman Chrome plugin that I was using to test the POST endpoint needed to provide a raw post body to be handled by this app; form-data and x-www-form-urlencoded options just showing up as a blank body.  Frustratingly that burned the time I might have used deploying to Heroku.  So by the end I was looking at a more delineated app structure than I had previously seen with express, involving models from Mongoose, and controllers and a router, all in separate files.  Ironically I don't know how widespread that particular layout is, although if I know the node ecosystem, there may not yet be any accepted standard.

The tutorial itself didn't actually provide any end to end tests, only unit tests.  The repo showed a controller test and an end to end test (they called it integration) using the supertest module.  I found some other blogposts on that and on an alternate called hippie:

* https://www.joyent.com/blog/risingstack-writing-testable-apis-the-basics
* http://developers.redhat.com/blog/2016/03/15/test-driven-development-for-building-apis-in-node-js-and-express/

I was reflecting that perhaps that's where I should have started with a slack bot, and I found a tutorial on that:

* https://scotch.io/tutorials/building-a-slack-bot-with-node-js-and-chuck-norris-super-powers

but we're over our integration limits for our AV slack, and I started to think that rather than worrying about the technical details I should get the engine for the bot working independent of any of the interfaces (InsideOut again), and so I finished up with writing down some stories about the system, which you already understand because the first part of this blog lays them out, but just in case you're interested here was my summary notes from the days hacking:

> planning poker bot

> /bot vote <ticket url> # optional ticket url

> --> announces - we have async vote on ticket url (first from repo if not specified) "title", please DM me 1 (simple), 2 (medium) or 3 (hard) - discussion in ticket or here as you prefer :slightly_smiling_face:

> then needs to be able to receive inputs from people - slack DMs? or expose web interface (could do react there?) and then post back to slack ...

> whole thing could be non-slack to start with to reduce complexity?

> people vote on tickets

> so we have an AsyncVote, which consists of a ticket with a title and url, and then there are a number of votes, which have a value, and come from an individual and might have an explanation, and when the number of votes reaches a set value (e.g. 3) then the full results are revealed, and the ticket value is set if there is agreement, or we prompt more discussion ....

So I've come round to thinking should I just TDD something in node that will operate on the command line, and then build some different interfaces on top of it?  I'd love to have time to do both a node/express and a ruby/sinatra version to show the complete parallels and/or lack of them between the two ... let's see when I have another fallow day :-)

Related Code:

* [https://github.com/tansaku/planning_poker_bot](https://github.com/tansaku/planning_poker_bot)



