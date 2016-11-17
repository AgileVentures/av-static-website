---
title: Clearing the Decks
date: 2016-09-20
tags: memory profiling gems heuristics coding acceptance unit testing
author: Sam Joseph
---

Monday involved continuing on two "clear the decks" tickets that Michael and I had started on the previous Thursday and Friday. The heuristics in my mind thus included:

* clear the decks before starting on something else big
* finish what you've started
* deal with things in the "in progress" column in your Kanban board to reduce cognitive overload for the team
* drive-by coding, i.e. try not to get too bogged down in any one thing, make tickets for additional features, refactorings and so forth
* focus on completing one task rather than constantly worrying if it is the right task

and that's forming a nice-ish set, even if some of them aren't that pithy :-) Lots of room for improvement there.  There are parallel collections of heuristics being applied in the actual coding going on, e.g.

* be confident (don't keep nil checking)
* don't mock what you don't own; prefer to introduce adapters you control
* follow the testing pyramid (few acceptance, some integration and lots of unit tests)
* keep methods/classes short
* try to name methods/classes for understandability and maintainability
* start DRYing once it's clear there is a pattern
* use Dependency Injection to avoid locking in what objects are collaborating with

Many more could be added to the list, but the above are the set that are more to the forefront of my mind at the moment.  There's a cross-talk between the two levels though, because seeing a place where you could use dependency injection, or introduce an adapter can extend the amount of time on task.  If you're practising "drive-by" coding, then that's a key place to ask if that refactoring needs to be done now, or if it's something to create a refactoring ticket for.

You might say, "you must immediately do any refactoring you think of!", and "if you put it in a ticket it'll never get done!".  However there's a counter-example from the LocalSupport project, where we had a nasty case statement, I created a refactoring ticket, and worked on it with someone later, and eventually we completely removed the case statement and replaced it with a gem using the strategy pattern; so that's a proof that refactoring tickets do sometimes get done :-)

The thing about refactoring is that there's always a little more you could do, but fundamentally we don't know what the future holds.  As needs change maybe that refactoring will have to be undone, or that code thrown out.  That isn't to disregard general code smells, but it's all relative.  Either way you have the interplay of the different heuristics.  Say you notice you could use some dependency injection, and/or some adapters, which do you work on first?  Or put both in a ticket?

For the set of heuristics at the higher level there's less conflict for me at the moment.  Focusing on completing one task in a drive-by fashion is all about not getting too distracted, and if the distraction is important, get that distraction into a ticket that you can review later for relative importance and complexity.  Huge ideas about a different direction?  Sure, but let's clear the decks by finishing what we started, especially those things we identified as going in the ready/backlog column in the current sprint.

So that's all well and good, however the memory leak task that Michael and I started on from the "in progress" column -- taking on something another developer didn't seem to be progressing on -- was not simple to resolve quickly.  Just investigating the task involves leaving a process running overnight to see what the memory profile is like after 12 hours.  That wasn't too hard to fit into the heuristic since once we had set up a memory profiling experiment we could leave that and move on to something else, but the ticket was still in the "in progress" column. Hmm ...

Mixed shout out to Heroku; positive for their memory profiling beta tools, negative for not getting back to me about the sudden spike in charging us for our group account without warning.  Still I'm enjoying seeing memory traces like this one that show the critical memory errors our app is generating in the resting state on develop.

![memory trace with errors](https://www.dropbox.com/s/z20m133bcd4b3ph/Screenshot%202016-09-20%2009.09.00.png?dl=1)

Interestingly those errors are removed from develop by having the setup match production:  

![lower trace with from matching production puma workers and threads](https://www.dropbox.com/s/jqwv7pxtdw41nd0/Screenshot%202016-09-20%2009.09.46.png?dl=1)

Production itself still experiences memory errors, but it's under a higher load:

![production memory errors](https://www.dropbox.com/s/poi23wzp8i0wvg8/Screenshot%202016-09-20%2009.34.00.png?dl=1)

We're basically following Heroku's tips on getting better [memory performance from Ruby apps](https://devcenter.heroku.com/articles/ruby-memory-use) which has pointed us to the `nearest_time_zone` gem as being our biggest memory hog.  Removing it cuts our baseline by about 10% (based on leaving things running overnight):

![reduced baseline profile after removing nearest_timezone](https://www.dropbox.com/s/8zg6xgvbzz1nn2x/Screenshot%202016-09-20%2009.10.12.png?dl=1)

Which leaves us with a complicated choice.  Would that 10% reduction actually help us reduce memory errors in production? Can we re-arrange the way the main app works so that we don't use that gem?  Is that worth it?  Following the heuristics, it feels to me we are getting to the end of the "investigating the memory errors" issue, and into new issues of trying different solutions to the memory errors, as well as needing to consider how serious these memory errors are, in that the app still seems to be running despite them.

The other task we're bashing away at in parallel is setting up an admin mailer to notify us when the slack invites fail (as they sometimes do).  I'd love to drive that with an acceptance test, but the difference in settings in test from production (invites are turned off), and the use of async tasks, means that is a more involved process to create an acceptance test than usual.  We've gone for a couple of unit tests and pushed the acceptance test into a separate ticket.  This is an example of the higher level heuristics overriding the lower level ones; which makes intuitive sense.  The admin mailer was something we spiked somewhat to get a tracer bullet to see the end to end connection, i.e. see the emails sent.  Although it was ultimately wrapped in a unit test, I berated myself at the end that *if* I'd written a really clear unit test at the start I'd have spent less time in the spike, but then again it was seeing the output from the email spike that helped me understand what I needed to be testing.

Drive with tests?  Sure, but let me spike to understand the problem.  More importantly, please let me drive with an acceptance test.  An architecture that doesn't let me easily add an acceptance test always makes me a little uncomfortable.   More heuristics at the coding level.  My intuition is that it's the higher level heuristics that should over-rule the lower level ones.  So on with clearing the decks.  We'll see how that goes ....


###Related Videos:

* [Pairing on AV main site](https://www.youtube.com/watch?v=Qveh4RtiWN4)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=UBo8d0Yebyw)
