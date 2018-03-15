---
title: AV Website Review Part 9
date: 2017-07-14
tags: 
author: Sam Joseph
---

![robots](/images/robots.png)

So the discussion in marketing was about adding a time limit to the message about the Premium Mob special offer, and any changes can now be submitted as a pull request.  There also seemed to be general agreement that we should avoid bots doing the greeting of new members in the #new_members channel.  There was interest though in the idea of having bots greet new members when they joined project channels.  I also reached out to a few project managers and got positive responses to the idea.

The issue is that new members joining a channel doesn't generate any kind of notification on Slack (at least on default settings) - having explained it to a few people and everyone agreeing that it means that we miss when members join new channels I just sent Slack a question about it:

> I was wondering if there is any setting in Slack that will allow channel joins to show up as a stronger notification.

> At the moment in our AgileVentures Slack, we get a little notice in the channel, e.g. Member name joined #general

> but I notice that the member joining doesn't cause the channel to get the bold highlight as there is with a new message, and not the number that's associated with being mentioned.

> I know I can write a bot that will detect new members joining and a channel and responding to them, but is there a setting that allows us to adjust the notifications status of channel joins?

> We wouldn't necessarily want to set it so that all members were bothered by channel joins, but it would be useful for admins, and perhaps nominated members of a channel who were focused on greeting activities.  Does that make sense?

> If not something in main Slack is there any plugin that already addresses this issue?

I have to say I've been very tempted to rustle up a quick bot that will do some greetings for us.  I'm supposed to be reviewing the AV website, but this has turned into a review of the AV ecosystem and I think that makes sense.  I don't think I should tinker more with the main website until I've got the results of the few experiments I'm running at the moment and in the flow through the site, that point when members join Slack channels seems pretty critical.  It's also an opportunity to get set up with a deployment flow on the bot project.  Although, should it be part of the greeter bot, or a separate instance?  Either way I definitely want to test this on a separate Slack instance before dropping it on our main Slack.

The other thing I was thinking of last night was whether we could have a bot like AsyncVoter that was activated by a slash command; then project managers could add the greeter bot to their channel, and then configure it themselves via Slack?  I'm not sure - I'll have to enumerate all these different Slack integrations at some point.  Anyhow, I can't help myself from quickly deploying another bot against a test Slack instance to see if I can get the basic channel greeting behaviour I want ... and thirty minutes later I have one deployed on Azure/Dokku, after a quick round of tests on a separate Slack instance to find the exact notification I want to match, getting stuck on the syntax for environment variables on dokku, but I now have it live on the #localsupport channel.  The code is just this:

```
var Botkit = require('botkit');

var controller = Botkit.slackbot({
  debug: false
});

var bot = controller.spawn({
  token: process.env.PROJECT_GREETER_SLACK_BOT_TOKEN,
  retry: 20
}).startRTM()

var greet = "Welcome to LocalSupport! :slightly_smiling_face: To install the codebase see: https://github.com/AgileVentures/LocalSupport/blob/develop/docs/installation.md#installation and our #localsupport-install channel if you need help"

greet += "\n\n\n see https://github.com/AgileVentures/LocalSupport/blob/develop/CONTRIBUTING.md for how to contribute to the project, but feel free to ask questions here too :-)"

controller.on('member_joined_channel', function(bot, message){
  // console.log('member_joined_channel')
  // bot.say({channel: message.channel, text: greet});
  if(message.channel === process.env.PROJECT_SLACK_CHANNEL_ID){
    bot.reply(message, greet);
  }
});
```

and the deployment was just this:

```
ssh dokku@agileventures.eastus.cloudapp.azure.com apps:create projectgreeterbot-production
ssh dokku@agileventures.eastus.cloudapp.azure.com config:set projectgreeterbot-production PROJECT_GREETER_SLACK_BOT_TOKEN=XXXXX
ssh dokku@agileventures.eastus.cloudapp.azure.com config projectgreeterbot-production set PROJECT_SLACK_CHANNEL_ID=C0KK907B5
git push azure-production master
```

with all the code at: [https://github.com/tansaku/project_greeter_bot](https://github.com/tansaku/project_greeter_bot)

and we get this:

![](https://www.dropbox.com/s/tunznnlxylbjteo/Screenshot%202017-07-14%2010.32.58.png?dl=1)

Should we label it more clearly as a bot?  I have mixed feelings - I'd like people to feel they could reply directly to me using @tansaku, but perhaps it degrades my credibility as a real person on Slack ...?
