---
title: Resilient Code Bases
date: 2016-11-09
tags: pull requests node cucumber features feedback refactoring architecture rearchitecting domain driven design DDD
author: Sam Joseph
---


How to create a resilient code base?  I daily wrestle with the legacy code base of WebsiteOne, wanting to make more changes and but being afraid of tests with strange dependencies, that sometimes don't test the right thing; while others lock in low level behaviour that doesn't connect to high level features that we need any longer.  As Michael says, the WebSiteOne codebase is better than many.  At least it has tests and was written with good software engineering principles in mind.  However it was a learning project for many.  A really good question is how to create a resilient code base where the developers are learning as they go?  It's a more general question than you might think since all developers are learning as they go, just that some of them are moving further out of their comfort zone at any given time.

I've been maintaining the AutoGraders codebase for a few years and that's even more scary.  At least with WebsiteOne we have the Rails Model View Controller (MVC) architecture so you know approximately where to find things.  AutoGraders has its own custom architecture that I'm not sure I completely understand and I don't know that anyone does.  I've not succeeded in being a developer of the AutoGrader, just a maintainer.  I've been able to trace some of the logic paths through the system and tinker here and there, but re-architecting is a big undertaking.  My first attempt stalled on the complexity of trying to wrap the entire system in consistent acceptance tests.  

Eric Evan's Domain Driven Design (DDD) and the concept of being able to refactor legacy systems when they have tests serve as inspiration.  I have more hope and motivation for the WebSiteOne project.  Its Rails architecture means that consistent acceptance tests are more likely, although the frustration of working with JavaScript in acceptance tests is an ongoing drain.  However 100% acceptance testing, like 100% test-coverage and 100% TDD are ideals with diminishing returns.  It seems to me that any time you get religious about one of these things you end up sinking an increasing amount of time as you try to get closer to the perfect 100%. 

It makes me think that having a clean and consistent domain model under the hood is the antidote, in some respects at least.  If you accept that having completely reliable acceptance tests on every single one of your important features is an unrealistic goal, then what else can you do?  At least if you're repeatedly evolving the underlying domain model towards something that represents more or less the core of your operations there's a chance you can make up for less than perfect acceptance tests.  Of course one person's coherency is another's confusion ...

The AsyncVoter project is steaming ahead and I'm hoping we can have a consistent model and manageable test suites in order to make it maintainable in the long run.  I'm hoping to learn the lessons from WebSiteOne and avoid AsyncVoter's maintenance debt creeping up.  That's also got to be balanced against not pouring cold water on the enthusiasm of the developers.  My battle cry has to be "keep that Pull Request small!".  Every time you think of another thing you could add, write a feature and put the backlog.  I was reviewing two AsyncVoter PRs yesterday.  Both a lovely green of Cucumber and Mocha tests; both for specific features that we'd voted on.

Both of them had high level stories that I think should have been tweaked a little, but here's what they were when the developers started working on them:

```gherkin
Feature: List Votes
  As a developer
  In order to cast my vote and see the status of the project
  I want the ability to check what stories are currently being voted on
```

and

```gherkin
Feature: Cast Votes
  As a developer
  So that I can indicate my thoughts about a story
  I want to cast my vote on the story and provide some notes
```

In the name of simplicity I would have changed the former to specify only a single current vote, and the latter to not mention anything about notes.  I'm sure that we'll ultimately need to have support for those two things, but every little extra bloats the pull request, means more code for review, more work for the developers and means it takes longer to get the code merged into master so others can build on it.  Buck stops with me here; I should have got in there and pruned those features aggressively when work started on them.

There might also have been push back from the developers on the scope of the stories.  What's interesting is that others don't seem to be quite so afraid of writing code as I am.  Perhaps I'm paranoid, or old, but I deal with so much legacy code, any time I'm working on a feature I want to see what is the absolute minimum I can get away with.  Others seem happy to start working on other bits and pieces of functionality, different error paths etc.  Mapping out all the sad paths for a feature is arguably more relevant.

Still there's this ongoing danger in coding of too much anticipation.  You Ain't Gonna Need It (YAGNI).  Right now I'm focused on getting a tracer bullet for the simple possible single voting framework.  At this point every additional element like being able to add notes to votes, track who did the votes, catch errors where the vote or person is not specified, are all things keeping us from getting to that tracer bullet demonstration that data can flow through the system and we can complete a high level story of an asynchronous vote on some remote infrastructure.  

I was also quite scared of giving this sort of feedback.  I've read people really wrong in the past in terms of how they will take different sorts of feedback.  Either way, push people too hard on something and you're likely to really irritate them.  There's also a humility required.  I know I've been really religious about different approaches in the past, and I've got to be open to the fact that I don't have all the answers.  I'll likely be pushing for variations on my current approach as we go forward.  People want the freedom to use their ingenuity to fix problems.  We had a mob review session and went through the above with a few members of the AsyncVoter team.  My screen lag didn't help, but I think it was a good session.

I've got some PR updates from the devs to review now, and I'm hoping to get these PRs merged in today, if I need to make a few changes myself, and I think I've cleared that with the devs so that I won't be stepping too heavily on anyone's toes.  Proof will be if any of them want to submit PRs again in the future :-)

After the AsyncVoter MobReview session Michael and I dived into some WSO refactoring.  We got as far as creating a Cucumber background step that doesn't use the spec fixtures, which ultimately allowed the more effective exposure of a bug.  Most of the time was spent tracing the logic paths through the system.  I really hope that we can evolve the domain model of WebSiteOne sufficiently so it doesn't go the way of the AutoGrader project ....

###Related Videos

* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=NcyTQ_kiV8A)
* [AsyncVoter Mob review](https://www.youtube.com/watch?v=4khJglSo8s8)
* ["Kent Back" scrum](https://www.youtube.com/watch?v=NcyTQ_kiV8A)
* [WSO Pairing](https://www.youtube.com/watch?v=EiBb-KFQDF4)







