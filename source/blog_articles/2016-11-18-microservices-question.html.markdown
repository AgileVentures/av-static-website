---
title: Microservices Question
date: 2016-11-18
tags: pairing spiking focus voting planning poker feedback sponsors sustainability AWS drie hosting 
author: Sam Joseph
---

Michael was off sick and so I didn't get back to WSO coding.  That's one of the things about having a pair partner, they keep you focused on things.  The previous day we'd investigated the poor performance on the projects page, which had turned out to be our non-cached checks of code climate stats for projects.  There was lots of other clear up indicated, but the simple drive-by was to remove that functionality for the moment.  Then we wrapped a payment bug that a Premium user had encountered, and got a fix in.  There were a whole series of other refactorings and changes that I wanted to get to, and thought I might hit today, but I was distracted by further developing the AsyncBot and working with our new sponsor drie.

I got the AsyncBot up on [GitHub](https://github.com/AgileVentures/async_slack_bot) and developed the spike a little further so it would announce updates in the channel the vote had been started in:

```js
var story = {
              name:'document the API',
              url: 'https://waffle.io/AgileVentures/AsyncVoter/cards/582afb10c2d2ce320075f487'
            };

controller.hears('start new vote',['direct_message','direct_mention'],function(bot,message) {
  start_channel = message.channel;
  bot.reply(message,'<!channel> NEW ASYNC VOTE on <' + story.url + '|' + story.name + '> ' + instructions);
});

controller.hears('vote',['direct_message'],function(bot,message) {
  console.log(message)
  vote = message.text.match(/\d+/)[0]
  votes.push({vote:vote, user:message.user});
  bot.reply(message,'I received your vote: ' + vote +  ' <@'+message.user+'>');
  bot.say({channel: start_channel, text: '<!here> ASYNC VOTE UPDATE '+ vote_text()+ ' on <' + story.url + '|' + story.name + '> ' + instructions});
});
```

I ran a couple of votes in the #websiteone channel and another in the #async channel.  I was getting much more of what I wanted as well as some useful feedback on the system:

![WSO channel vote](https://www.dropbox.com/s/5v6ii9lrpox5pi5/Screenshot%202016-11-18%2009.25.31.png?dl=1)

It was clear that when the votes came on thick and fast that the messages were a bit overwhelming.  certainly room for some tuning.  Also running two votes in a row, we had confusion about what was being voted on from one user who hadn't quite got caught up with their slack feed.  I have to say it is so nice to work with a small project where you've written all the code yourself, and you can tweak the interface as you please.  I can also see how without tests the whole thing could become a complete monster.  I also notice that unlike Ruby/RSpec I don't have throwing together a node test rig memorised; I need to do some reps on setting those ups so that I can't use that as an excuse for continuing the spike indefinitely.

In the background we got the latest version of the AsyncVoter RESTful API running on drie servers.  It seemed like the failing POST operation was just me not having pushed the right code out, and then getting confused about which endpoints were up and which weren't.  In response I started a vote on documenting the AsyncVoter API, and I think Arreche got started on that.  It occurred to me that we might have moved faster by focusing on the Slack interface initially, but it is good to have a RESTful API up to try and avoid the worst spiky impulses.  It does feel that we've spent a while building something that could be generated in minutes by a Rails scaffold, or possibly even and Sails or MEAN scaffolding operation.  That said this low level work is great for everyone to appreciate the core of a RESTful API.

The question is now about architecture going forward and where the slack bot gets hosted.  I paired with Ben from drie and looked at his analysis of our Harrow Community Network logs where he tried to work out how frequently the drie apps should be sleeping in order to give us the best performance.  We ended up identifying the different needs of the final deployed app versus the developer experience during creating it.  It also seemed like it might be problematic to host the slack bot on the "drie push" offering since it uses an always on web socket and it's not clear whether traffic from slack on the socket will be sufficient to wake up the instance.  I started to think about whether to just host the bot on AWS.  We host the auto graders on AWS and I'm pretty familiar with the interface ...

In principle the slack bot could talk directly to the MongoDB in order to persist the details of the vote.  This would mean that it wouldn't be easy to interrogate the bot about the vote other than outside the slack interface.  It would certainly nice to have a web dashboard of all the ongoing votes and get statistics and all.  I guess we have the question of whether the micro services architecture gives us a short term win here.  By making the RESTful API and starting work on two different clients we've gone for a more distributed architecture that might make the development of any individual client slower, but will give us more flexibility in the long run?

In the background I'm talking to possible new sponsors of AgileVentures and getting ready for our first mob programming sessions on Avdi Grimm's "Confident Ruby" for those who've upgraded their Premium memberships.   I think we need the asynchronous voting to keep the individual projects ticking over, but we need more money coming in from sponsors and premiums if we're going to be able to continue to offering all our services, both free and premium.  There's a new feeling of momentum to the community, but as I was saying the other day, it's not just a question of sitting around and waiting for the growth.  Each bit of growth still has to be fought for at this stage, although perhaps the battle is getting easier - until we start coasting after we achieve that fabled critical mass? :-)


###Related Video:

* [Pairing on WSO](https://www.youtube.com/watch?v=lCm8Ht7vvp8)
* [Previous days "Kent Beck" scrum](https://www.youtube.com/watch?v=_fogY2nGq2Q)
* [Pairing on WSO](https://www.youtube.com/watch?v=MW6JuYJSEQA)
* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=soSqEHPT3tU)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=SoILIjeb02E)






