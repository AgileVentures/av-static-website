---
title: Where to Change?
date: 2016-09-08
tags: coffeescript node javascript testing TDD mocks microservices bots 
author: Sam Joseph
---


Trying to improve the test harness on the agile-bot yesterday was rather frustrating.  AgileBot is written in CoffeeScript and runs in the Hubot framework, which runs in Node.  It was originally spiked with no tests as a way of providing a service that would send notifications to Slack about hangouts for scrums and pairing sessions.  We made one adjustment to the spike a few months back to have it handle incoming information about the project that the hangout was associated with, so that certain projects could be re-directed to different messaging systems; the critical one being pushing MOOC pairing sessions to the MOOC Gitter chat.

So we got away with making that change, but we'd like to make other improvements, such as:

* Allowing the user to start the hangout from slack
* Make it so that hangouts related to projects are reliably posted to those project rooms
* Improve the formatting on the messages particularly hyperlinks

So following the example of the HangoutConnection app which used Jasmine tests, a few months back we added jasmine-node tests, and we now have some integration tests.  They are however somewhat brittle.  The other week Michael and I spent an hour or two trying to work out why the tests ran on his machine and not mine.  We were both on OSX, me on a slightly newer version.  It was interesting to investigate all the dependency management (nvm, node, npm etc.).  We spent some time getting on the same version of npm, node, and sorting all the other dependencies. Still my tests would just not run, no error message, nothing.  Somewhat frustratingly it all ultimately seemed to just start working when I used the auto-reformat feature on RubyMine that re-indents things.  CoffeeScript is sensitive to indenting, so perhaps there was some stray tab character, or mis-indented component that caused my machine to hiccough, but not Michael's.

Maybe it is just because of my experience in Ruby, and relative lack of it in Node, but this seems to be a consistent experience for me in Node.  It reminds me of PHP in the early days.  Things would not work, and then suddenly they would just start working for reasons that were not completely clear.  Perhaps it's rose-tinted glasses, but I don't think I ever experienced that in Ruby unless I was going multi-threaded.  I'm totally open to the idea that the problem is that I haven't conceptually mastered some aspect of Node.  I'm very comfortable with JavaScript on the client side, and I feel pretty confident with it there.  CoffeeScript and Node are two steps away from my comfort zone.

Michael and I came back to the test harness yesterday and I spent a little time trying to understand the tests that Michael had written in my absence.  I started doing a little refactoring because there was a method called mockSoAndSo and it was both mocking a network connection and then executing the actual network request.  We'd got set up with hand-rolled mocks as we had in the HangoutConnection tests.  In order to avoid a complete Hubot acceptance test we were passing in a Hubot "mock" that our CoffeeScript file would process an generate a routing table that connected URL paths with functions.  

Then to test we would run the function associated with a URL path, stubbing out the network calls to the 3rd party APIs of Slack and Gitter using the nock library.  In my mind it is the node equivalent of WebMock.  We had previously tried to get more realistic tests of the full network stack (for the slack/gitter calls) by using [Sepia](https://github.com/linkedin/sepia) which seems to be the Node equivalent of VCR.  We got some recording and playback working, but it didn't seem to have the VCR functions that we expected, which was failing when unexpected network connections were made, so we went with nock, which involves more work in terms of precisely specifying what you expect, but does complain when something unexpected happens.  The caveat there seems to be it will ignore extra key/value pairs in a post body, but if the key/values that are in the specified set then we'll get an error.

The process of putting all this in place originally had been a somewhat laborious one.  It felt a bit like C programming with segmentation faults, but without the benefit of a stack dump to analyse.  Basically we had to insert `console.log`s to see how far the program was getting, and then ultimately identifying that the problem was the rollbar automated notification system, which if called in test mode just led to a complete crash.

Trying to refactor the tests yesterday I got caught out badly changing the wrong function, as there are two different mocks in the same file - one for posting hangouts, and another for posting video links.  That was frustrating.  There's this trade off in all programming between having everything you are working on in the same file, so you can see all the relevant things at once, and that file getting too big, so you have to start jumping back and forth.  One mental note from yesterday: "reduce those file sizes!". 

Even with that slip out of way, and the tests refactored somewhat to make it clear that the mocking was separated from the network call, with code like this:

```coffeescript
  describe 'hangouts-notify for scrum', ->
    beforeEach (done) ->
      @slack = mockSlackHangoutNotify(@routes_functions, 'C0TLAE1MH','Scrum', 'localsupport')
      makeRequest(@routes_functions, 'Scrum', 'localsupport', done)

```

When I tried breaking the tests to check that they would fail in the right way, the test suite just crashed, no output, no nothing.  Now I'm sure this at least partly comes down to my incorrect mental model of how things are working.  In order to handle JavaScript's single threaded nature we have to pass this `done` object through to the makeRequest function so that we can set a timeout like so:

```coffeescript
makeRequest = (routes_functions, type, project, done) ->
  res = {}
  res.writeHead = -> {}
  res.end = -> {}
  req = {body: {host_name: 'jon', host_avatar: 'jon.jpg', type: type, project: project}}
  req.post = -> {}
  routes_functions['/hubot/hangouts-notify'](req, res)
  setTimeout (->
    done()
  ), 1

```
This ensures that our request has a chance of hitting the HTTP stack, before we check in the test that nock has received the right kind of HTTP request:

```coffee script
    it 'should post hangout link to general channel', (done)->
      expect(@slack.isDone()).toBe(true, 'expected HTTP endpoint was not hit')
      done()
```

It took a fair amount of fiddling to get this all working, and it doesn't fill me with confidence that we're on the right track to something maintainable.  The point of having a test suite is so that we can refactor the underlying code with confidence, but if we don't trust the tests ...?  So what should we do differently?  Michael says that jasmine-node is kind of old, and that he thought we'd identified a better alternative.  The [JasmineNode repo](https://github.com/mhevery/jasmine-node) hasn't been updated in a couple of years.  The [JavaScript Jabber podcast](https://devchat.tv/js-jabber/226-jsj-test-doubles-with-justin-searls) that I listened to this morning was talking about a new [test double framework for JavaScript](https://github.com/testdouble/testdouble.js) as an alternative to [Sinon](https://github.com/sinonjs/sinon) and they were talking about a lot of the same issues that Michael and I were running into.

I'm tempted to want to change more.  I'm frustrated that the stack-traces we do get can't be used to pinpoint the lines that things are going wrong on, because we are being given the line numbers for the Javascript that the CoffeeScript is being compiled into.  We tried getting CoffeeScript source maps set up; and we failed.  Maybe that would work with a little more effort, but one of the most frustrating things for me is that the logical functionality of the agile-bot is so simple, I feel like I could re-create it in a couple of hours in Ruby/Sinatra, without all the unknowns.

It could even be pulled directly into the WebsiteOne codebase where we already reach out to the slack API to send email invites.  Of course that would be increasing the mass of code in our Rails monolith, and there's an argument for maintaining our limited micro services architecture with a separate service for this logic.  There's also perhaps an argument for having it in another language than Ruby in order to make our microservices architecture interesting and appealing to different OS developers.  The system was designed in Hubot initially as an experiment with the idea that we might be using other interesting functionality from Hubot.  That hasn't happened, and although the different tech stack of the agile-bot has been an interesting diversion, it's frustrating that I just want to be able to make some simple tweaks to our notification system to improve the day to day experience of AV users, and instead I'm tinkering around with JavaScript testing.

A quick googling gives me the [updated Jasmine version for node](https://github.com/jasmine/jasmine-npm), and so the route of just changing one thing at a time would be to upgrade the tests to this.  I guess I have to consider that the underlying AgileBot code works, and has worked reliably for some time, and that's got to count for something.  Further steps would be trying out Mocha/Chai - for which [I found a good overview of the differences](https://www.codementor.io/javascript/tutorial/javascript-testing-framework-comparison-jasmine-vs-mocha).  Another step would be to move away from CoffeeScript to JavaScript, or even to remove the Hubot framework and build the thing in pure node/express.  And the biggest step would be to another stack, which could be back to familiar Ruby territory or even off to new horizons like Elixir or Go.

If I'm pairing with Michael it probably makes most sense to start with that single step to JasmineNPM.  Although if I have a spare couple of hours it might not hurt to see if I really can spike an alternate Ruby/Sinatra micro service as fast as I think I can :-)
