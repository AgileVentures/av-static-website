Set the alarm this morning but still a late start as all my boys wanted help with their codecombat programming challenges, and they're still off school for the summer.  Anyhow, there are several niggling things about the various greeter bots we have for AgileVentures and individual channels.  I've directed a few people to the #bots channel to get involved, but so far no one has really gotten involved.  Is there some critical mass of activity on a project, or some critical level of support that's required before some people will start submitting code.  One comment I had was that these bots are just tiny nodejs projects.  I guess for those wanting to cut their teeth on open source node projects a challengingly complex project is appealing in some sense.  Personally I have had just about enough of complexity and the simpler the better.  The key metric for me continues to be can we deliver something of value to the end user.  Is what the project delivers of value to someone somewhere.

What was really simple this week was setting up slack repeating reminders such that we get alerts about when the scrums are upcoming, some project meetings and some mobs.  The syntax is super simple, e.g. 

```
/remind #localsupport to prepare for weekly meeting @here in 30 minutes at 4:00pm every Thursday 
```

The exciting thing with the new fixed URL videoconferencing from Jitsy is that the meeting ping itself can be set as a recurriung reminder:

```
/remind #localsupport to join the weekly meeting @here in http://meet.jit.sy at 4:30pm every Thursday 
```

I'm thinking to make my participation in the project meetings a bi-weekly thing so that I can get private project work done on the "off-weeks" so to speak.  My only complaint is that the reminders aren't directly editable. However they are easily listable and deletable.  I also see that they can be controlled by API:

https://api.slack.com/methods/reminders.list

That makes me think we could possible take the agile-bot out of the equation by simply having our main site hit slack directly to set reminders based on events created or updated through the rails interface ...  By having set up the LocalSupport meeting to rely on a permanent jitsy url we take the need for a really experienced scrum master to start the videoconference out of the equation, and in principle anyone can stream the recording - although that is more fiddly in JitSy.  It does seem like JitSy performance might not be as good as hangouts, particularly since they're offering their new low latency setting, but I guess we should experiment with our own JitSy hosting on Azure.

So that's all very well for maintaining the run of existing projects, but that's not going to immediately help with getting more folks involved in the greeter bots (or other new projects), or make them greeter bots seem a little more friendly.  Maybe I would be better off labelling them specifically as bots.  For the main project greeter if I don't have the message come from me, then I don't get to see the response that people make to the bot.  In the group channels, it's kind of weird to have an automated message from me when I'm inviting someone in for a vote.  What I really want to move towards is some kind of drip campaign that follows up with people who've joined project channels (and our community) to ask them if they need help and support.  I notice that Slack follows up with multiple emails after the fact when you've been invited to a Slack community, presumably to maximize the chances of getting new users into Slack.

For the main greeter bot I've got four little changes to tick off:

1. hyperlinking to our free premium mob page
2. adding mention of our code of conduct
3. referring to the new person by name
4. hyperlinking to channel names

For the project greeter bot I've got lots of issues, but my top ones are:

1. names of folks in message not being linked
2. channel names not being linked
3. avoid reposting the intro if we've recently done so

This last would go a long way to making the bot seem more human, but it would also require the most complexity, i.e. some state would need to be held, or we'd need to pull messages out of the channel to check for recent posts.  That latter seems simpler, but also something that we should try after wrapping all this in tests.

I'm conflicted here - maybe the thing that would get more people involved would be making super-polished documentation on all this, or created new project channels - project placeholders in our main site and getting started pages; but the itch I want to scratch is a few formatting issues, and also ensuring we have a global code of conduct in place (which should go in the getting started as well ...).  I guess I'll let myself hit those issues first ...

I've just picked off hyperlinking channels in the main greeter bot, and pushed that branch to production - hmm, no staging set up there - not so easy to test even on the bot-test instance I created - unless creating new accounts - can do ... should get set up with that properly.  Linking to channels and users from bot posts does seem like a relatively straightforward syntax fix:

https://api.slack.com/docs/message-formatting#linking_to_channels_and_users

Gonna stop there today since I'm behind and hopefully this will give me impetus to get the few other rough edges sorted before the big push, which is setting up some proper testing infrastructure on these projects ...

