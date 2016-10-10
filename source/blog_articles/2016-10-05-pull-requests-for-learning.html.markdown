---
title: Pull Requests for Learning
date: 2016-10-05
tags: pull request PR Github branch stackoverflow context vygotsky technical debt tracker voting chore story bug
author: Sam Joseph
---

Call me crazy but I love pull requests.  Well let me place a caveat on that.  I love small pull requests, and the smaller the better.  I guess I still love the big ones, even if my response to them will usually be, "that needs to be smaller".  Part of my PR joy is the way that GitHub displays them to me, and lets me comment on them.  I'm not so keen on the web interface for creating them, but I now primarily use `hub pull-request` from the command line, so I don't fight with the web UI like I used to.

What I love about giving feedback on a pull request is that it has so much context.  It's on a specific repo, it shows everything that's changed.  Given appropriate configuration it will also include the results of running all the test suites, and a link to the deployed instance of the system.  I'll generally be encouraging submitters to include screenshots to highlight any UI changes (would be great to automate that too somehow ...) and a hyperlink to the ticket that the pull request addresses in Pivotal Tracker or Jira or whatever.  If it's a Github issue then that linkage is partially automated.

I was talking about StackOverflow posts [yesterday](http://nonprofits.agileventures.org/2016/10/04/anatomy-of-a-question/), and I gave examples of two questions from some former students, and pretty much the first thing I was asking was for more context on their codebase.  Asking questions in the form of a pull-request removes the chance that context will be missing.  Call me crazy or extraordinarily lazy, but trying to help someone stuck on something when you don't have the full context is asking to burn a lot of time.  As [Kent Beck says](http://nonprofits.agileventures.org/2016/10/04/anatomy-of-a-question/), any decent answer to an interesting question being, "it depends", and if you ask me, it depends on the context.

Back in the day I was always trying to pop up three levels of abstraction and ask, do you even need to be trying to cross that bridge?  If I'm stuck on anything I'm always trying to do that, but other parties don't always appreciate that approach.  Often I think they just want to stay at the level of abstraction they are at.  They want to work with the particular technology because that's what they think is the right pathway to a job, or with a particular system they are building because they think it will solve some sort of problem for themselves, or for someone else.  Pushing them too strongly out of the zone of their original question will generally make them uncomfortable.  Not that occasionally making people uncomfortable is wrong.  Sometimes that discomfort is Vygotsky's [zone of proximal development](https://en.wikipedia.org/wiki/Zone_of_proximal_development), but other times it's a panic or resentment zone.  What I'm working on is getting better at knowing how much discomfort others can take.

Which brings me back to pull requests.  In AgileVentures we field a lot of incoming pull requests on open source projects from developers of all experience levels.  Our goals are two-fold.  One, we want to be improving the quality of our codebases in order to deliver value to the users of our software.  Two, we want the submitters of those pull requests to be having a positive "team" learning experience.  Not that other open source projects don't have the same goals, but we're explicit about the learning part.  We really want the submitters to feel that they are part of an Agile team, getting involved with voting on the complexity of stories, bugs and chores; hanging out with each other online, and having access to meetings with the end clients who are generating the feature requests in the first place.

There are several patterns that I have noticed:

1. many people are reluctant to make pull requests until they think their code is "finished"
2. some people are quite intimidated to make pull requests at all, perhaps thinking they might break something
3. some people can feel quite negative after receiving feedback on their pull request
4. most people will get quite frustrated if you keep on asking for additional changes on their pull requests

A few years ago in my naivety about interacting with humans, I had implicitly assumed that making a pull request was an emotional non-event for people, and that they would appreciate ongoing requests for changes in order to get their code into the best state possible.  Looking at things now that seems self-defeating if our objective is to help people learn.  How have I evolved my PR management strategy?  Here's a few things:

1. encourage people to submit "Work in Progress" pull requests, but don't push too hard
2. always start by thanking the submitter for their contribution
3. try and find at least one good thing to say about their contribution
4. limit the number of constructive criticisms to avoid overloading
5. be kind, because everyone is struggling
6. use automated code-assessment tools like houndci and code-climate, and tune them to not be overwhelming
7. tell people that automated tools can be ignored
8. gently encourage people to read the CONTRIBUTING.md and follow the PR submission guidelines
9. if there is a trivial fix that I want in the PR, push it in myself

But the most important and revolutionary thing to me has been the strategy of refactoring tickets.  I'm still wondering if these aren't actually evil technical debt in disguise; but now we've been using them for a while, and I've had a fair few follow-up refactoring tickets closed with pull requests from other developers, I'm starting to think they really are a good way to go.  They are a pretty good way of avoiding two unpleasant and difficult situations.

One thing that happens is that a person submits a pull request, and as project maintainer you ask for a refactoring.  Next, the submitter doesn't necessarily agree with the refactoring, or just doesn't have time to get to it.  So the pull-request sits there getting further and further out of sync with the codebase, and either requires a lot of work for a later merge, or ends up getting thrown away, which is a waste.  One way round this that I mention above as item 9) is making critical little changes in the pull request myself.  In the past I've got contributors to give me access to their forks, and now GitHub has a checkbox to make it easy for submitters to allow that.  From a learning point of view I think it might be better if the submitter made the changes themselves, but often the original submitter is exhausted, frustrated, or just won't have time to get to the work for a few days.  For the sake of the project it's best to get the PR pulled in sooner rather than later before the codebase diverges; and the submitter can still learn from the changes you made in the closed PR view.

The other difficult situation is that refactoring tickets are made, but no one ever works on them and so the overall quality of the codebase suffers.  In a couple of projects we've started voting on refactoring chores, and this has had the effect of increasing their visibility, and now new team members have started working on them as a lower intensity way into the codebase.  Pivotal recommends not assigning points to bugs and chores, but in true Agile fashion we came to reflect on whether that was working for our situation.  In a project with a full time team, points for bugs and chores are dangerous because they distort estimates of when work is going to be completed.  We're not a full time team; we have a lot of part-time volunteers with a high churn rate.  For those volunteers it's important to know how complex a bug or chore is before they decide to work on it.  We're not working to tight deadlines and so the estimation process is a nice to have, rather than a must to have.  In addition, our "staffing" model means that our projects sometimes suffer from lack of capacity; and so it's good to have points assigned to anything that consumes our volunteers time. Finally points on bugs and chores also reinforces the concept that refactoring stories can be just as important as new-feature stories.

A typical project team at Agile Ventures will have some very junior developers. Some will have just finished a MOOC or a 3-month coding camp, and might feel uncertain about their capability to contribute in a meaningful way. As noted above, working on a refactoring ticket is a lower hurdle to get started. The developer can learn an enormous amount by examining existing code, and gain confidence in their ability to assess design intent and code quality. This is a critical skill (and also a morale booster), and its great when this benefit can be explicitly reinforced by the more senior members of the team.

Futhermore the addition of asynchronous voting allows more of our distributed part-time volunteers to participate in the process of assessing the complexity/difficulty of stories, bugs and chores. Maybe it's a coincidence, but I've been enjoying a little wave of refactoring pull requests, and I think we're getting a win-win here.  We're breaking up bigger jobs into smaller more manageable chunks that new team members can start on in order to get familiar with our codebases and our processes.  Our code quality is improving and I think there's a fair bit of learning going on :-)





