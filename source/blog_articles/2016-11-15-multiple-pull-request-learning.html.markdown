---
title: Multiple Pull Request Learning
date: 2016-11-15
tags: performance REST features cucumbers focus twitter social media analysis technical debt
author: Sam Joseph
---

I didn't get much solo or pair time coding today. It was a day of helping Premium member's with bug fixes and reviewing pull requests.  The "Martin Fowler" scrum was crowded and brought up various requests for help, support and review.  I started by creating a hangout to look at Sasha's WSO pull request to check whether YouTube videos were valid before tweeting them.  Sasha was concerned whether he'd sandboxed correctly, and I explained the `@vcr` tag we had that meant individual VCR cassettes weren't necessary.  More interestingly we were seeing that the tweets going out were slightly out of sync with their messages.  Sasha was investigating the issue of invalid YouTube links going into the tweets:

![AV tweets](https://www.dropbox.com/s/845stvx878zuumo/Screenshot%202016-11-15%2009.23.27.png?dl=1)

What we were seeing was that the tweets to post the hangout link and the finished video were both going out as soon as the hangout started.  Sometimes the person starting the hangout wouldn't press "Start Broadcast" so the YouTube video link would lead nowhere. There was no feature test covering this twitter functionality, and so I wrote one out:

```gherkin
Feature: Tweeting Live Events
  As a site admin
  In order to increase participation in events
  I would like live events to generate twitter notifications

  Background:
    Given an event "Scrum"

  Scenario: Event going live causes tweet of hangout link to be sent
    Given that the HangoutConnection has pinged to indicate the event start
    Then an appropriate tweet has been sent  

  Scenario: Event stream going live causes tweet of the youtube stream to be sent
    Given that the HangoutConnection has pinged to indicate the event start
    And youtube stream has gone live
    Then an appropriate tweet has been sent # e.g. see live stream

  Scenario: Broadcast termination causes tweet of the youtube URL to be sent
    Given that the HangoutConnection has pinged to indicate the event start
    And youtube stream has gone live
    And that recording has finished
    Then an appropriate tweet has been sent # e.g. see recording
```

Sasha asked the important question of did we really want to be posting all this stuff to Twitter.  I guess the motivation is that by posting hangouts, live streams and videos to Twitter then we increase the chances that people will watch out videos and join our hangouts.  Looking at my YouTube traffic report for the last year I see that 5.3% of watch time on my video account comes from Twitter:

![pie chart](https://www.dropbox.com/s/z66n3h4046zp3oi/Screenshot%202016-11-15%2009.32.54.png?dl=1)

![pie chart legend](https://www.dropbox.com/s/hjdtgfld0lph838/Screenshot%202016-11-15%2009.33.30.png?dl=1)

compared to 60% from edX, 8.3% from Google search and 6.6% from the AV site itself.  While social media traffic to our AV site (over the last year) was dominated by Google+, Meetup, StackOverflow, Facebook and LinkedIn.  Twitter came in 8th with only 3.46%.  Now maybe this is because of broken video links, or because the feed is almost entirely automated. Either way, what we were putting out there was inconsistent, and the simplest fix was to only tweet the video link once the broadcast went live, and Sasha and I saw there was an easy check we could make for that through the YT gem.   Sasha had to run off to do a tech test; which made me think it would be awesome if tech companies would take AV participation as a tech test equivalent!

After lunch I merged an [exquisitely small and updated PR on LocalSupport](https://github.com/AgileVentures/LocalSupport/pull/396) from new contributor Kostas and then jumped on an AsyncVoter performance testing issue, running a profile of the AsyncVoter app running on drie and heroku.  

```
→ ab -n 50 https://async-voter-production.herokuapp.com/stories

Time per request:       381.987 [ms] (mean)

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:      257  280   7.4    284     288
Processing:    92  102   7.3    100     130
Waiting:       92  102   7.3    100     129
Total:        353  382  11.8    384     414

Percentage of the requests served within a certain time (ms)
  50%    384
  66%    385
  75%    387
  80%    389
  90%    399
  95%    401
  98%    414
  99%    414
 100%    414 (longest request)
```

drie's average was slightly faster, but the variance was much larger:

```
→ ab -n 50 http://master.async_voter_production.app.push.drieapp.co/stories

Time per request:       355.193 [ms] (mean, across all concurrent requests)

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:       88   91   2.2     93      94
Processing:    92  264 474.9    100    3093
Waiting:       92  264 474.9    100    3093
Total:        180  355 475.1    193    3186

Percentage of the requests served within a certain time (ms)
  50%    193
  66%    196
  75%    220
  80%    486
  90%    881
  95%   1102
  98%   3186
  99%   3186
 100%   3186 (longest request)
```

This was a very rough test and not really fair on drie since we'd just used both the apps, so Heroku was awake and didn't suffer a start up cost.  But drie currently sleeps their "push" apps every 30 seconds, which explains the variance and means we would suffer the slow start up costs much more frequently.  I was able to take that data to discuss with the drie folks in our regular catch up meeting.  No sooner was that done that I was on a bug hunt with new Premium member Rose, identifying issues with seed files and cucumber tests.  After we got the BURideShare cukes green, I took a quick pass through an [updated Redeemify pull request](https://github.com/strawberrycanyon/redeemify/pull/43) which had been updated really nicely.  

I was trying to get a handle also on the new SHF-project, reviewing the new [CONTRIBUTING.md](https://github.com/AgileVentures/shf-project/blob/develop/CONTRIBUTING.md) from Ashley and then joining a hangout with Ashley and Susanna who were discussing a [pull request](https://github.com/AgileVentures/shf-project/pull/22).  Susanna was keen to get it pulled in after having followed one set of advice from one mentor, but Ashley was concerned that things were getting too complex, and I was inclined to agree with her.  I offered what advice I could and then had to go to supper.  Bottom line is that it's okay to accrue technical debt from things like using non-RESTful controller methods, but it's good to be aware that you're doing that and that you'll pay a price later on.  If you've got to move fast right now then guidelines can be ignored, but short term speed has to be balanced against long term progress.

In summary a productive day I think, although I didn't spend much time focused on one thing.  Great to see so many active projects on AgileVentures, and so much learning arising from all the pull requests coming in.  We just need lots more of this and lots of processes in place to ensure my head doesn't spin off trying to keep track of it all :-)


###Related Videos:

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?feature=player_embedded&v=kkm2KdUDuC8)
* [WebSiteOne Code Review](https://www.youtube.com/watch?v=k4UrtG6Q0vw)
* [AsyncVoter Performance Testing](https://www.youtube.com/watch?v=rwc4luv4_6c)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=eNfi_j6BT0k)
* [Bug Hunt on BURideShare](https://www.youtube.com/watch?v=qjfV2_DFG18)