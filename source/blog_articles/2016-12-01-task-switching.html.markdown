---
title: Task Switching
date: 2016-12-01
tags: blogging, planning, time management, automating, unit testing, mocha, chai, nodejs, botkit, botmock
author: Sam Joseph
---

![task switching](/images/task_switching.png)

Tuesday was a day of much task switching.  It started with putting my office back together after new windows had been put in.  That took a while.  I managed to quickly stamp out a blog on the Agile Project Workshop before the "Martin Fowler" scrum.  My attempts at changing the flow for blog drafts were getting a little out of hand as the process of squashing and merging between the `new_blog_drafts` branch and master seemed to be leaving stray commits that were then making the subsequent new blog draft PRs rather messy.  In frustration I deleted the branch and re-created it.  I think we need to go with Arreche's suggestion of a webhook, and to do that I'll need to move the blog_draft notifications into the new AV Premium Slack instance; or at least some where with spare Slack integration capacity.

In the scrum I was advising one Premium member about the perils of task switching, or more accurately about the perils of spreading oneself too thin over many projects.  Talk about the pot calling the kettle black! :-)  After the scrum I joined Matt for an hour pairing on WebSiteOne and Redeemify, and I managed to restrain myself from immediately diving in to individual problems and talked to Matt about the bigger picture of trying to make progress with all of AgileVentures and WebsiteOne in particular.  We reviewed the open PRs and board and I unblocked Matt on two issues, and then helped him with another on Redeemify where the presence of a legacy Rails 3 `asset` group in the Gemfile was breaking the system on Heroku.

After lunch I scaffolded a mentoring session for a private company on a paid project.  I used Google hangouts to connect up an experienced developer from the US with a London based educational company, so they could get advice on their legacy Rails codebase.  That all seemed to go reasonably well, and more sessions like those could go a long way to making my own financial situation a lot more stable.  We'll see if we can't do a lot more of that.  I had been hoping to bring in some large paid projects for the benefit of a group of AV Premium members, but maybe slivers of small consulting work will ultimately serve the same purpose.

While I was monitoring the paid session I was managing Slack and getting my own company's corporation tax paid.  30 minutes on the phone to discover that Her Majesties Revenue & Customs (HMRC) aren't sending paper letters with reference ids any more.  Next I caught up with Thomas and Michael in the "Kent Beck" scrum, and we talked more about the task switching issue.  Michael called out Thomas and myself as being heavy task switchers, and we clarified that although there was lots of room for us to be more task focused, heavy task switching can be particularly challenging for junior developers.  While there's lots to learn from different projects, Thomas and I agreed that sticking with one codebase for a couple of weeks to really get to know it well is important for someone just learning the ropes.  Actually I'm starting to think that I'd really like to have a coding binge on a single project.  I've started looking at the calender to see when I will get to the one year mark for my blogs.  Blogging every day is great, but I'm thinking wistfully about other setups, for example where I do nothing but code for four days and then do blogging and admin on a Friday ...  I wonder if my commitments to help people get coordinated on Slack prevent me from getting any really focused coding done?

I believe it was June when I started blogging daily.  Will I keep myself to my committment of doing it every day for a year?  That's another six months ... I'm also wondering if I can do a blog that's almost purely code?  Personal time management strategy aside I jumped into a quick session with Michael.  I was totally torn between working on the Premium upgrade interface on WSO and the Slack bot.  It certainly feels like the async Slack bot could be a real engine of activity for many projects.  What I'm really hoping is that it can start to take over some of the project managment admin roles that I'm doing on various projects.  I'd also love it to take over some of the greeting and orienting admin.  Of course it might all backfire if all the projects get really active and my workload spikes! :-)

We're not measuring quantitatively, but from the Slack messages pinging about, I get the sense we have more active projects now in AgileVentures than ever before.  I resolved to try and get some testing code into the async Slack bot spike to give us a framework going forward.  This was the repping on node testing setup I'd been talking about needing to do for some time.  I followed the mocha docs to produce this test with Michael and Arreche's input:

```sh
var assert = require('assert');
var vote_text = require('../src/utils');

describe('Server', function(){
  describe('vote_text', function(){
    it('should format vote text correctly for no votes', function(){
      assert.equal(vote_text([]), '0 vote so far []')
    });
  });
});
```

here's the contents of the utils file under test

```sh
module.exports = function(votes){
  var text = votes.length +' vote';
  if(votes.length > 1){
    text += 's';
  }
  text += ' so far ['
  votes.forEach(function(vote) {
     text += '<@' + vote.user + '> ';
  });
  text += ']'
  return text;
};
```

We got a spike of a unit testing setup.  I had to run off to take my sons to football academy and so I didn't get to fix up the awful naming conventions in the above.  Do we really want a `utils` file, or should we be creating a `vote_text` function in the utils file, or should we be creating objects?  Michael pointed out that as the Slack bot, this code would have a different domain from the RESTful API we had built separately, revolving more around channels and users than votes and stories.  We left on the question of could we get a good end-to-end acceptance testing framework in there?  My experiences with testing Stripe have soured me a little to sandboxing systems like VCR, and our attempt to use the node equivalent of sepia on the AgileBot project had been met with failure.  I found a [BotMock](https://github.com/gratifychat/BotMock) framework that would in principle allow us to do to botkit what we had done with the Stripe Ruby mock on WebsiteOne.  BotKit really had been a gorgeous experience in terms of getting the spike of the bot up and running.  What I wanted now most of all was a good way to get the high level user stories of the slack bot defined and driving the code.  At the moment they live implicitly in the spiked code and some Slack interactions.

The needle we really want to thread is the one that allows us to expose clearly the features that the system supports, so that we don't get all snarled as we add new features and evolve the domain model; and that new developers coming onto the project can easily orient themselves.  What we don't want is to get bogged down in crazy bugs arising from mis-matches between our mental models of the software and testing components.  If we can thread that needle, perhaps we can support many active projects without my brain being levered out of my ears, and possibly enough space for a coding binge without too much task switching.  Now that would be a great Christmas present! :-)

###Related Videos:

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=3wPmyLfRwxY)
* [Pairing on WebsiteOne](https://www.youtube.com/watch?v=1rwVM5uuFR4)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=JrIeqF5y9pU)
* [Coding on AsyncSlackBot](https://www.youtube.com/watch?v=ll91NnqwRZs)
