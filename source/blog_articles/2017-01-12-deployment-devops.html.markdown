---
title: Delpoyment DevOps
date: 2017-01-12
tags: presentation, operations, development, continuous integration, pull request, production, staging
author: Sam Joseph
---

I'm thinking alot about DevOps at the moment.  Partly because I'm giving a talk titled "DevOps on a Budget" a week from today at the remote DevOps conference.  I'm talking to folks on Slack about it, listening to the Ruby Rogues talk about it, listening to talks by some of the other presenters at the conference as preparation.  I'm also doing more DevOps ..., I think.  My sense was that DevOps was all about breaking down the barriers between the sometimes isolated areas of development and operations.  In large organisations developers would "hand off" their code to operations for deployment.  Naturally these areas are likely to be merged in smaller organisations, but the DevOps movement seems to be about joining things up so that development isn't considered in a vacuum, and developers are involved in the operations processes of deploying and maintaining.

That said, the term "DevOps" is perhaps being used casually to apply to operations, so that one starts thinking of developers and devops folks?  We use Continuous Integration (CI) systems (such as Travis and Semaphore) in our AgileVentures projects, so that anyone submitting a pull request kicks off a completely fresh build on a fresh virtual machine which involves installing all the libraries and running all the tests.   This means that every individual developer cannot escape the effects of the code they wrote on the test suites, because a tick or cross is added to their pull request.   Does this make every developer a DevOps person?  Not quite.  Nothing forces the developer to care about those tests, except that we won't merge their PR until they pass.  There's also the point that the code is building on a fresh Linux box, so in order to get their PR merged, the developer has to operate as if their code is being deployed in a another location, because it is.

It seems to me that running the tests in a virtual machine in the cloud doesn't quite constitute deployment, because there's the additional step of getting the app running in production mode on a machine that's accessible to the stakeholders.  Raoul had been focusing on the deployment and release role in our pairing on the project management of WebSiteOne.  Last year I had been focused more on coding and PR review, and Raoul had been taking the code from the develop server (where features get merged to) on to staging and then into production.  Despite fairly comprehensive acceptance tests there's always still room for something to go wrong on one of the Heroku servers in "production" mode.  Note the potential linguistic confusion there.  Rails apps can run in "production mode", but when we talk about the "production server", that's the one the main stakeholders are using.  The "staging server" will also be running in "production mode", but it won't be "production" if that makes sense? :-)

We have it set up so that whenever a pull request is merged in, this will kick off a deployment to the relevant servers.  So merging to develop kicks of a run of the tests and if they pass, the code is deployed to the develop server.  Similarly for the staging branch (merge will deploy to staging) and the master branch (merge will deploy to production); in each case the deployment depends on all the tests passing.  As Raoul has got busier with Craft Academy I've taken the lead on merging PRs to the develop (branch and server) and then doing manual tests on the develop server, followed by pushing the code from develop to staging, and then staging to production, with manual tests on each.

This process seems to be strongly operations-ish to me.  I'm calling it DevOps because it certainly involves some development skills.  For AgileVentures to fully embrace DevOps, we'd have more folks involved with this DevOps process, as we do with development, but I think it does take a higher level of committment.  There's also more to go wrong here.  Now that I write out the details in preparation for the presentation I see that each individual AV project does have its own DevOps component, so there are a healthy number of AV members getting a strong taste of DevOps experience.  It might be even stronger if we could afford Heroku's deploy PR feature that has every submitted PR lead to a deploy based on a default set of environment variables. This would potentially connect every dev with a full deploy.  We're working with our sponsor drie on a more affordable version that will deploy every PR.

Anyhow, after a crazy and distracted start to the week I finally managed to get down to some WebSiteOne DevOps done.  I took the latest code on develop and merged it into staging, after doing some preliminary manual tests on the development server.  The development server has less realistic code and settings on it, so there was only so much I could check there.  Note that while I am doing a sanity check on the site in general there, as well as a more detailed look at the new features, I'm still relying on our acceptance test suite to catch more complex issues that might arise from interactions between old and new code.

On the staging server I was able to see the new sorting of projects by Github update recency feature was working, and also that starting a hangout would still ping Slack for us, despite the changes we'd made to the EventInstances Controller.  However the feature I wanted to test was preventing Twitter posts of the YouTube stream link before it had gone live.  That was broken on staging.  I saw this in the log:

```
2017-01-11T15:01:10.165957+00:00 app[web.1]: Request waited 5ms, then ran for longer than 20000ms
2017-01-11T15:01:10.166341+00:00 app[web.1]: /app/vendor/bundle/ruby/2.3.0/gems/yt-0.28.1/lib/yt/request.rb:237:in `sleep'
2017-01-11T15:01:10.166385+00:00 app[web.1]: /app/vendor/bundle/ruby/2.3.0/gems/yt-0.28.1/lib/yt/request.rb:237:in `sleep_and_retry?'
2017-01-11T15:01:10.166388+00:00 app[web.1]: /app/vendor/bundle/ruby/2.3.0/gems/yt-0.28.1/lib/yt/request.rb:201:in `run_again?'
2017-01-11T15:01:10.166422+00:00 app[web.1]: /app/vendor/bundle/ruby/2.3.0/gems/yt-0.28.1/lib/yt/request.rb:80:in `run'
2017-01-11T15:01:10.166458+00:00 app[web.1]: /app/vendor/bundle/ruby/2.3.0/gems/yt-0.28.1/lib/yt/request.rb:81:in `run'
2017-01-11T15:01:10.166489+00:00 app[web.1]: /app/vendor/bundle/ruby/2.3.0/gems/yt-0.28.1/lib/yt/request.rb:81:in `run'`
```

Getting to this point had involved sorting out an enviroment variable for the Yt gem to contact the YouTube servers. On develop I had checked that it worked from the Rails console, but I did have to reload the Yt gem config, which seemed odd.  On staging I dropped into the Rails console and found the same thing; this timeout error occurring if I didn't re-apply the Yt gem config.  I became suspicious that the main Rails app was not loading the config.  I had advised the developer (Matt) to drop the Yt config into the rails `config` folder, thinking it would be auto-loaded from there like the `puma.rb` file in the same location.  I had mis-remembered and advised poorly.  The file needed to be in the `config/intializers` folder.  I made that change on the staging branch and deployed it directly to confirm my suspicion.  I was correct, so I pushed that change back down from staging to develop.

Clearly my involvement in both the operations and development sides had helped us resolve this issue, but we weren't out of the woods yet.  Next I started the broadcast on the hangout I had started from the staging site.  No twitter posts.  I could now use the Yt gem functionality from the Rails console to check the duration of the livestream associated with the hangout.  It was showing the duration as zero.  It seemed like one of our assumptions was false, i.e. that the livestream starting would lead to an increasing duration reported by the gem.  I'd been experimenting with that with another developer (Sasha) several months previously.  Using the interactive console, and with a live streaming hangout in progress I was able to determine that what in order to check whether the livestream had actually started, we in fact needed to be checking was that the `live_stream_details.actual_start_time` was not nil,.

We'd missed this in the development phase, where our tests were green because we'd stubbed the Yt gem to return a non-zero duration for a live streaming video.  If we'd used VCR to capture the full network interaction we might have encountered this problem earlier, but the Twitter tokens being used had expired and testing the full network interaction had been left out.  I could have gone on and deployed to production.  I didn't, but if I had the code in its current state would technically have fixed the underlying problem (tweeting unrecorded videos), but in a rather aggressive fashion by never tweeting any video links, unless a user finished the broadcast and left the hangout running.

I left the code on staging and opened a [ticket](https://github.com/AgileVentures/WebsiteOne/issues/1489) for the problem.  I'd had the original developer (Matt) in several of the hangouts associated with this DevOps work so I think I have a reasonable claim to the idea that we are doing joined up "Development" and "Operations" in this particular project :-)  Following through it occurred to me that we could have a DevOps acceptance test to check for the presence of the YT gem configuration, and we had the alternatives of just updating our mocks or a full VCR test for the tests covering the action of the YT gem.  A DevOps acceptance test might look like this:

```gherkin
Feature: Able to access YouTube API via Yt gem
  As the admin
  So that we have information about videos generated by our members
  I would like the Yt gem to be configured correctly
  
  Scenario: check Yt gem configuration
    When the app is running
    Then I would like the Yt gem to have it's api_key set to the GOOGLE_PROJECT_API_KEY environment variable
```

I've got a few "devops" cucumber tests in both WebSiteOne and LocalSupport relating to things like rake tasks that might need to be run in production etc.  Perhaps I'm completely crazy here.  Total overkill when this bug is very unlikely to recur?  Or is this actually nice executable documentation that wraps a bug we encountered on staging and provides a clear readable representation of our experience here?

What we've got here is a configuration bug that wasn't caught by our tests.  Perhaps it should be an integration test at the RSpec level?  As long as I'm reviewing future PRs, I should notice if anyone jiggles with the Yt project config, but then again I won't always be around to check things ... or perhaps switching to a VCR acceptance test would cover all this?  It might not check for the presense of the key (which WebMock might), but then again, we may completely lose the ability to get updates from Hangout plugins on April 25th (which this all relies on), so burning time here might be counter-productive compared to spending time on other potentially revenue producing code.

At least I've blogged all this and I'll make some tickets.  We want to get these changes deployed.  Adjusting the mock is the quick fix, given that we've checked the behaviour.  Is all of this DevOps?  Or is it only DevOps if you're using Chef, Docker and Kubernetes?  These are the kinds of questions I'll be pondering in my DevOps conference talk ...

###Related Videos:

* ["Martin Fowler" scrum]()https://youtu.be/_GBJLaKbTxo
* [DevOps on WSO part 1](https://youtu.be/1_Zkh4JsBik)
* [DevOps on WSO part 2](https://www.youtube.com/watch?v=8q4RBgjzijY)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=39I9UXJizCY)
* [DevOps on LocalSupport](https://www.youtube.com/watch?v=xAPWS7PKprc)

p.s. this blog took 51 minutes to write
