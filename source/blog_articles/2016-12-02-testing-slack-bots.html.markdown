---
title: Testing Slack Bots and Project Metrics
date: 2016-12-02
tags: botkit botmock yada cucumber chai mocha svg projectscope 
author: Sam Joseph
---

Wednesday saw me firing off a few emails to potential sponsors, which felt like a good start, since landing another sponsor would make all the difference to us over the coming months.  We had our second mob programming session, and I got a preview copy of the new RSpec book, so things were humming along.  What wasn't humming along was invites to Slack.  We still seemed to be having an email every day or two saying that the automated Slack invite was not showing up after a sign up on the main AV site.  Michael and I had put in an email system to supposedly alert us when the Slack invites failed, as they sometimes did when the API keuy expired.  Those email alerts had only ever triggered for failures on the develop system.  And looking back through the logs it seemed that invites really weren't going out for the last month or so and our fail-safe wasn't even triggering; so frustrating!  The challenge with this kind of issue is that every time someone says they didn't receive the invite, it could be because of a spam filter issue.  

I do get cc'd all the welcome emails when people join AV so I can see the flow of people coming in.  I just wish I could get cc'd on the Slack invite emails.  I'd refreshed the API keys recently.  The Slack invite thing would clearly require another detailed look. Doubly frustrating was the hacked up env settings for test that meant the full Slack invite pathway was not properly tested.  I didn't want to dive into debugging the Slack issue as I was feeling the AsyncVoter project was blocked due to a lack of test harness on the AsyncSlack bot; so I took a temporary measure and grabbed the emails for the most recently signed up 150 AV members and did a bulk invite in Slack.  It told me that at least 80 people had never been invited.  It was gratifying to see the 15 or so new members enter slack over the next 12 hours with several of them mentioning the Ruby Rogues podcast.

Having put in the short term fix I could focus a couple of hours coding on getting a test harness into the AsyncSlackBot.  I refactored the simple unit test from the day before so the naming of the files and methods was more consistent, and started on checking out the BotMock project.  Installing it involved building it locally and copying over a couple of mock files.  Not ideal, and I start to think we could just as quickly build our own mocking system, as we did in the agile-bot project.  The BotMock project is new and not even on npm, and I wonder if we're really gaining much from it.  I stamped out a few controller tets using their approach, which involved coding in ES6 and I had to adjust the callback operations to get working tests like these:

```js
  it('should acknowledge vote if users direct messages `vote #`', ()=>{
    var self = this;
    var text = 'vote 1'
    var response = "I received your vote: 1 <@test>"
    return self.controller.usersInput(self.input(text))
               .then((text)=>{
                 expect(text).to.equal(response)
               })
  });
```

Working with this sort of test had involved pulling the body of the bot out of the server.js file and dropping it into a controller file from which it would be exported.  I was getting critical practice with breaking up my node apps.  Not that it's so hard, but when something is slighltly less familiar it's easy to put off doing it.  I ended up with a pull request that did a bit of testing on each of the canned bot responses, but didn't achieve a full test of the full voting sequence as it was unclear how to check a series of interactions.  The BotMock had some example conversation tests, but they weren't working for me. 

This is a good start over the initial spike, but it's frustrating that the high level character of the app can't easily be read from the mocha tests, in the same way that it can be difficult to read them in RSpec.  Both Mocha and RSpec can give verbose test output that provides a nice summary of what's going one like so:

```mocha
  controller tests
    ✓ should return `help message` if user types `help` (208ms)
    ✓ should return `hello yourself` if user types `hello` (204ms)
    ✓ should return vote announcement if user types `start new vote` (202ms)
    ✓ should acknowledge vote if users direct messages `vote #` (208ms)
    ✓ should return results if users types `results` (205ms)
    - should summarise results after a vote

  Bot
    Controller
      ✓ should receive message

  summary_text
    ✓ should format vote text correctly for no votes


  7 passing (1s)
  1 pending
```

but I personally find it difficult to digest that output.  What I mean is that when I'm working on the code, usually with the spec on one half of the screen and the app code on the other half like so:

![code layout](https://www.dropbox.com/s/vkcavhglz2kkadq/Screenshot%202016-12-02%2009.59.23.png?dl=1)

Most of the mocha/rspec code is taken up with boilerplate that is specific to the testing framework and not the app itself.  The high level English language description is scattered in comments.  I really do love Cucumber for giving me that description in the editor and not in the separate terminal output.  So perahps it's time to try out Yada (cucumber nodejs alternative) in this project, but I think I can't get too sucked in to testing frameworks without pushing more functionality to actually help automate me away from tedious admin tasks.  We'll see.

What is exciting is that it seems that if we feed the bot an API token rather than a bot token, we can get the bot to behave as me (as Thomas and I found testing another bot).  So potentially I can augment myself on Slack directly.  Dangerous perhaps; in a test with Arreche I already tripped an infinite loop in a private channel.  Lots of testing required to avoid more mishaps like that :-)  I got the PR in and managed a separate flurry of coding/devops on ProjectScope later in the evening.  The new month meant my Heroku quotas had been reset and so I could get this overview of how the different AV projects were proceeding:

![Project metrics on AV projects](https://www.dropbox.com/s/emvw7cujo777itx/Screenshot%202016-12-01%2018.41.42.png?dl=1)

The graphics are interactive and when you mouse over them you get some explanations and pop-ups of who's got what level of activity in Slack:

![showing who's active in slack](https://www.dropbox.com/s/assku0q6ptxhp7r/Screenshot%202016-12-01%2018.52.19.png?dl=1)

I think this really is the way forward, automation of project management via Slack bot and breaking our monolith into microservices, e.g. metrics on all the projects in one service, manangement of hangouts/events in another service; so that we can grow to more and more projects supported in following Agile principles to keep them all delivering value to the end users and the individual team members participating in order to level up in the chosen skill sets.  Can we get into that sweet spot where all the effort an automation and reflection results in a great user experience all round without overwhelming the support staff?  Stay tuned to this blog to find out!

###Related Videos

* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=X3mWPUN4TC4)
* [Solo on AsyncVote part 1](https://www.youtube.com/watch?v=gK96bxINM88)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=XP3lyx0zZZY)
* [Solo on AsyncVote part 2](https://www.youtube.com/watch?v=RYRugWUmv74)


