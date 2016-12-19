---
title: Semi-Automated Bots, or Augmented Humans?
date: 2016-12-12
tags: meetings, pull requests, bug fixing, Slack
author: Sam Joseph
---

Friday included a lot of meetings, pull request review, and bug fixing.  I didn't get much fresh coding down, which was somewhat frustrating, but I did have a bit of a breakthrough on the greeter bot side, with this code:

```js
var Botkit = require('botkit');

var controller = Botkit.slackbot({
  debug: false
});

var bot = controller.spawn({
  token: process.env.ASYNC_VOTER_SLACK_BOT_TOKEN,
  retry: 20
}).startRTM()

var greet = "Welcome and great to have you with us! Please do introduce yourself in #new_members (if you haven't already), and if you have any general technical thoughts/issues/questions please ask in #techtalk - #random is for everything else :slightly_smiling_face:"

greet += "\n\n\nsee https://github.com/AgileVentures/AgileVentures/blob/master/JOINING_A_PROJECT.md for more on joining a project"

controller.on('team_join', function(bot, message){
  console.log(message)
  bot.say({channel: message.user.id, text: greet});
});
```

A simple script using botkit to detect anyone joining a Slack instance.  I had been burning time, and falling behind on, greeting people individually as they joined the AgileVentures Slack instance.  I think people really appreciate individual greetings when they join a community, and it's really helpful to point them to some documentation etc.  It can be a bit impersonal coming from a bot.  I've joined some other Slack communities where a bot greets me and immediately apologises about being a bot. 

The interesting thing we've found is that if you set the BOT_TOKEN to be the Slack general API token (rather than a custom bot token), then it appears to interact as the admin of the Slack instance.  At least that's my working hypothesis.  Now you could say I'm being a bit dishonest with people having a bot doing the greeting, but the message looks like it is directly from me.  However the beauty of the above script is that it opens a dialogue in a DM between myself and the new member on Slack.  So when the new member responds to me, thanking me for the introduction, I get the message and can respond in the DM to have a conversation.   I call this a semi-automated bot.  A bot is doing part of the work, but then I am assisting with the really hard AI-complete aspects.  I do the same with emails to the community, automating part of the work of assembling an email template in Thunderbird, but then visually reviewing the email before it goes out, adjusting it to use the correct first name or abbreviation, and add elements to the template that are specific to my interaction with that AgileVentures member.

I'm hoping that people will ultimately see this as ingenious rather than deceptive.   I want to augment myself so that I can maintain conversations with as many people as possible, not so that we can make a profit for some group of shareholders, but so that we can help more people with their professional development and more deserving causes around the world with their IT and non-profit process challenges.  Over the weekend the bot greeted three new folks while I was spending time with my family and I got the new members' follow up DMs on my phone and was able to respond to their questions about AgileVentures individually.  It feels like a good development.  The next thing will be to host this bot on an Amazon instance so it can be always up, and perhaps have it monitor for channel join events as well, so it (or rather I) can greet people coming into project channels with a welcome and a pointer to documentation for getting set up with the project.

I'll be tempted to see if I can gradually add more augmentation to myself on Slack so that that bot can start taking over parts of my conversation, but I'll do that very slowly and steadily.  In the first instance it seems to me that the bot is much, much better than me at detecting Slack events, and so that's where I should focus to begin with.  Particularly because Slack doesn't alert me to channel and team join events and I tend only to notice the little italics telling me about a change some time after it actually happened.

In summary I think this approach where I adjust the behaviour of my own Slack instance is different from the semi-automated approach I've espoused in the past.  Semi-automation is a better term for when something that is clearly a bot, is prompted to act by a human, such as we have for starting and finishing votes in the AsyncVoter bot.  There's nothing new under the sun, and I'm sure I'll find others who've done this bot augmentation before, but I start to see what I've got here in the greeter-bot as a form of conversation augmentation.  I'm pretty excited about augmenting myself further :-)

