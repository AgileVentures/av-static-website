---
title: Semi Automatic Asynchronous Planning Poker Bot
date: 2016-11-17
tags: planning team slack node botkit custom integration testing persistence spiking tracer-bullet prototype
author: Sam Joseph
---

The AsyncVoter project has been humming recently.  A lot of people interested in coding in Node, putting a React front end together and so on.  They say that JavaScript is hot.  We've also had new folks come in and start to understand some of the Agile development approach, self-organise to get a series of basic RESTful endpoints in place.  All great, but I was starting to worry that our little RESTful MVP was not really making enough contact with end users.  I was getting antsy that we needed to start automating some of the tedious and error-prone parts of running asynchronous votes manually in project slack channels.

I felt that Slack integration was key.  We had another vote on refactoring test support code so couldn't start another vote on Slack integration if I was to follow my own guidelines of no voting on multiple stories in parallel.  I settled for moving the Slack integration ticket to the top of the backlog and searching for integration mechanisms.  Several of our systems ping messages to Slack, but I wanted more.  Custom slash commands for Slack looked like they might work, but they had custom payloads that our RESTful endpoint wasn't set up to process.  I didn't like the idea of customising endpoint specifically for Slack, since it would be nice to support other clients as well.  I also wanted to be able to have a personal bot that our community members could interact with.  I went ahead and made a [custom bot integration](https://api.slack.com/bot-users):

![AsyncVotingBot integration](https://www.dropbox.com/s/wopizsow3ortdbu/Screenshot%202016-11-17%2009.17.40.png?dl=1)

It's easy to get put off making custom commands and integration in Slack when they paste the "Your team has reached the integration limit" all over the place.  However this doesn't mean you can't add more configurations on the integrations you already have, including slash commands and bots.  One thing that accelerated my progress here was [botkit](https://howdy.ai/botkit/), which allowed me to avoid diving into Slack's real time messaging API and just get up and started super fast.  Ironically, as I put the bot together Arreche found a [Planning Poker Plugin](https://github.com/Sergej-Popov/hubot-planning-poker) for hubot and was getting that set up in another channel.  There are no new things under the sun!  

Just writing this blog I did a search for "slack planning poker" and found a number of things that would have been good to review and check earlier:

* https://tangocode.com/2015/12/scrum-planning-poker-for-slack-part-1/
* https://github.com/apheleia/plan-b-ot
* https://github.com/nateyolles/slack-pokerbot
* https://github.com/paulodiovani/planning-poker-slack
* https://www.scrumbot.co/

Although I think most of these are designed for synchronous operation, i.e. assuming that the team will all be assembled in the channel at the same time.  What I'm really after is something that can maintain open votes on a number of channels and provide a mechanism for people to check what they need to vote on, and have the bot proactively prompt the channel to ensure that votes don't get left hanging.

I went ahead and had fun creating a bot and running a semi-automated asynchronous vote in the #async_voter channel:

![async bot announces](https://www.dropbox.com/s/j2vu3nwh53yt1hp/Screenshot%202016-11-17%2009.28.26.png?dl=1)

Each team member messaged the bot independently like so:

![me messaging the bot](https://www.dropbox.com/s/obn0y3xphjn85pg/Screenshot%202016-11-17%2009.29.38.png?dl=1)

and then I prompted the bot to display the result:

![async bot results](https://www.dropbox.com/s/4s2pjly2u2emsaf/Screenshot%202016-11-17%2009.28.58.png?dl=1)

Here's the complete code for the bot:

```js

var Botkit = require('botkit');

var controller = Botkit.slackbot({
  debug: false
});

controller.spawn({
  token: "<SLACK_BOT_TOKEN>",
}).startRTM()

var votes = [];

controller.hears('start vote',['direct_message','direct_mention'],function(bot,message) {
  bot.reply(message,'<!channel> NEW ASYNC VOTE on "Slack Integration" https://github.com/AgileVentures/AsyncVoter/issues/12  Please DM me with: `vote 1` (Simple), `vote 2` (Medium) or `vote 3` (Hard) - Discussion in ticket or here as you prefer. :slightly_smiling_face:');
});

controller.hears('vote',['direct_message'],function(bot,message) {
  console.log(message)
  vote = message.text.match(/\d+/)[0]
  votes.push({vote:vote, user:message.user});
  bot.reply(message,'I received your vote: ' + vote +  ' <@'+message.user+'>');
});

controller.hears('result',['direct_message','direct_mention'],function(bot,message) {
  response = 'votes so far: \n\n'
  votes.forEach(function(vote) {
     response += '<@' + vote.user + '> voted: '+ vote.vote + '\n';
  });
  bot.reply(message,response);
});
```

Note that I hard coded the ticket I wanted the vote on and I ran locally without any persistence, so if my code or computer crashed then state would be lost, AND the bot needed to be prompted to both start & finish the vote.  We could wail and gnash our teeth and say that we need all those features sorted before we could push the bot live, but by eschewing them I was able to run a proof of concept of a successful vote in a channel, and I actually saved myself having to manage all the DMs I get during a manual vote.  You can say, oh, it's obvious that it would all work, but I don't trust computers to do what I think they'll do.  My mental model of them is so often faulty.

I also spiked this without tests.  I did manually test the code in a private channel before unleashing it on #async_voter, but I think this is a situation to spike, to get a tracer bullet in to see the round trip.   Of course the danger here is to get drunk with power and keep building on top of this untested code until it really is a total mess and impossible to change without breaking something.  We've got a nice tested RESTful core ready to start storing the data persistently.  I can run a few more test votes on other channels, and gradually refine the look and feel of the bot, ensuring along the way that it is having the positive effect on the projects and community that I am hoping for.

I think this is the core of the Agile process.  Apart from remembering to reflect on your process frequently, it's getting some sort of working system in front of the end users as often as possible.  I'm also a big fan of this semi-automated approach.  I still had to intervene to stop and start the vote, but it saved me some time compared to a full manual run.  Having confirmed the partial utility of the system I can cautiously automate more and more.  What I really need to add next is to have the bot update the channel where the vote started when someone makes a vote.  Let's see if I can add a little of that today ...









