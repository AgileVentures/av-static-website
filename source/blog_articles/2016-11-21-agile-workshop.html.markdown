---
title: Agile Workshop
date: 2016-11-21
tags: agile manifesto scrum XP kanban opensource principles pullrequests code review pairing 
author: Sam Joseph
---


Friday was mainly consumed with a dry run of an Agile Project management workshop for non-profits and charities.  I've been working closely with Voluntary Action Harrow Cooperative (VAHC) for the last three years on the [Harrow Community Network project](https://www.harrowcn.org.uk) which itself uses the [AgileVentures LocalSupport](https://github.com/AgileVentures/LocalSupport) software

![Harrow Community Network Website](https://www.dropbox.com/s/j1n566aoa7hregq/Screenshot%202016-11-21%2009.36.51.png?dl=1)

Over those three years we've been using an Agile project management approach to developing the software and coordinating the efforts and contributions of over 30 volunteers from [all around the world](https://www.harrowcn.org.uk/contributors).  We've been following the principles of behind the Agile Manifesto:

> Our highest priority is to satisfy the customer through early and continuous delivery of valuable software.

> Deliver working software frequently, from a couple of weeks to a couple of months, with a preference to the shorter timescale.

> Working software is the primary measure of progress.

I meet with VAHC at least once every two weeks to show them the latest deployments to the website and get feedback on any changes, and well as showing them work in progress. 

> Welcome changing requirements, even late in development. Agile processes harness change for the customer's competitive advantage.

Every meeting we review the [project board on Pivotal Tracker](https://www.pivotaltracker.com/n/projects/742821), with a particular focus on the features in the backlog.  The backlog is maintained with higher priority items towards the top of the backlog.  As requirements change we follow the clients advice about moving things up and down in the backlog, and even move things back to the icebox if they suddenly become of low relevance.  For example the story on "[being able to override doit vol ops](https://www.pivotaltracker.com/story/show/116028767)" was top of the backlog at one point, but got moved onto the icebox as circumstances changed.

Any new suggestions from the client are immediately added to the icebox and go through a voting process to estimate their technical complexity.  Once they are estimated they can be moved onto the backlog with the client's consent and subject to the client's preferences regarding priority.

> Business people and developers must work together daily throughout the project

This can be challenging with a distributed team of developers all around the world.  We broadcast the client meetings via YouTube, and run a google hangout that developers are encouraged to join.  Any developer can participate in a client meeting, and can communicate with the client via email.  However as project manager I tend to funnel questions from developers through to the client, and bring them up in the client meeting.  Given the part time volunteer nature of our project it is difficult to support daily interaction between client and developers; and since we are all developers our "business people" are arguably the client, although we make no strong distinction between devs and non-devs in our distributed team.  Everyone is learning, everyone is participating.

> Build projects around motivated individuals. Give them the environment and support they need, and trust them to get the job done.

Every developer is free to work on what they are interested in.  The client's preferred priorities are expressed through the priority ordering on the backlog, but developers can start any story that takes their interest.  We do strongly encourage all developers to put any unestimated story to a vote (either synchronous in a scrum, or asynchronous in a text vote) before starting work on it just to help increase awareness that they are interested in working on something.  They don't need to wait for the vote to complete in order to start a spike or some experiments.  However it makes strong sense to let others know what you are thinking about so you can benefit from their thoughts and suggestions.  Even so this is not a hard and fast rule.  The software is open source and anyone can submit a pull request at any time, but getting involved in the voting and estimation process is a great way of staying on top of any information relevant to the task and increases the chance of a smooth merge of code later on, while reducing the chance of duplicated effort.

> The most efficient and effective method of conveying information to and within a development team is face-to-face conversation.

We agree, but we can't afford to fly all the developers into the same location for this purpose.  Google hangouts gives us a close second to a face to face conversation, and I try to meet with the client in person where possible.

> Agile processes promote sustainable development. The sponsors, developers, and users should be able  to maintain a constant pace indefinitely.

We've been maintaining a slow and steady pace for the last three years.

> Continuous attention to technical excellence and good design enhances agility.

All code is submitted to the project via Pull Request for open review.  We encourage everyone to use google hangouts to pair program online in order to get more immediate ongoing feedback as code is developed, although the logistics of time zones and lives don't always support pairing.  Even with paired code, everything coming into the project is automatically checked for merge conflicts, tests passing, and style/coding guidelines.  Any developer can comment on any pull request, which we try to either close or merge within a week to avoid widely diverging branches.  The ultimate decision for merging rests with myself as project manager, and I try to maintain a match between any code coming in and the domain model that we have developed over the last three years.

> Simplicity--the art of maximizing the amount of work not done--is essential.

I strongly encourage all developers, whether working on a chore, a bug fix or a feature, to submit the absolute minimum of code.  Only things related the specific ticket being worked on should be in the pull request.  The smaller the code changes in the pull request, the happier I am.  Small pull requests are easier to review, easier to merge, can often be done faster, and provide a more frequent sense of completion and progress for everyone involved.  Seeing something else that needs to be fixed? Open a refactoring ticket and submit a separate pull request for that.  There are always new folks coming into the project who will love a really simple refactoring ticket to allow them to get started on something where they don't have to generate new tests. 

> The best architectures, requirements, and designs emerge from self-organizing teams.

I'm a single point of failure in this project, although two or three others have access to all the necessary to continue merging and deploying if I was removed.  Other AgileVenture's projects have more self-organising structures where PRs can be merged with the agreement of any 2 or 3 devs.  All structures are subject to revision.  Maybe ultimately the LocalSupport project management structure needs to evolve and change.

> At regular intervals, the team reflects on how to become more effective, then tunes and adjusts its behavior accordingly.

Earlier in the project we had weekly retrospectives, but with few dedicated full timers these have fallen away.  We've switched from many synchronous standups to more asynchronous voting in group text channels on Slack. The Agile principles are written around the idea of a full time co-located team, and we have adapted them to a volunteer part-time distributed team.  Our adaptations certainly need ongoing review and adjustment.  It's not yet clear how best to ensure reflection, tuning and adjustment happens frequently enough.

The workshop dry run (remember that?) involved a 90minute session in which we used the Agile process itself to try to answer the general challenge of better understanding Agile.  I initially presented the Agile Manifesto and talked about related concepts such as scrum and XP for a few minutes.  The overall structure, which compressed a two week sprint into 90 minutes was as follows:

1) Present concept of Agile
2) Generate stories relating to Agile, and how it can be used in non-profits
3) Organise stories by theme, to identify core item and vote on them
4) Form teams and have each team pick a story
5) Teams each work on an individual story, preparing to present their thoughts
6) Each team presents their conclusions
7) Retrospective: each individual looks back on what went well, what could be improved

The key stories that came out of the 2nd and 3rd stages above were:

* How does Agile handle dramatic shifts and changes? (1.5 points)
* How does Agile work with other non-Agile organisations? (3 points)
* How does Agile work with fluid staffing? (2 points)
* Agile Logistics (how to scrums and standups work) (1 point)
* Is Agile a good fit for non-profits and charities (3 points)

Our three teams of two each chose one of the above and completed the 4th and 5th stages before presenting their thoughts to the whole group.  It all seemed to work pretty well, particularly with a group number of 6 sitting round a large table with the cards organised in front of us.  Everyone said they felt they understood Agile better and there were a lot of productive and interesting discussions.  One participant summed up Agile as an integration of strategy and operations, and I concurred.  Agile is not an absence of planning; it is a commitment to ongoing planning and reflection.  One confusion was that we were using the Agile process to understand the Agile process, and it was all rather meta.  How would you use it to solve specific problems and implement the solutions?  I proposed that after a session like this, one could do a full day inception event with a team where the story tickets would be actual challenges to the organisation, and the retrospective component would take place 2 weeks later.

Anyway, first up we'll be running a full version of this "Understanding the Agile Process" with frontline charity and non-profit folks in a group of 10-20 people.  Stay tuned to this blog to find out how that goes!









