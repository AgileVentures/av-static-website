---
title: Automating What To Do Next
date: 2016-11-16
tags: planning team meeting voting distributed asynchronous driveby whistlestop pairing waffle github 
author: Sam Joseph
---

After the "Martin Fowler" scrum I caught up with Raphael to talk about the dynamic of the AsyncVoter project.  Raphael had been suggesting a team meeting.  Team meetings can be great.  I think if a team wants to move forward fast it's great to get everyone in a room, and ideally match that with a shared working space that everyone can come to.  However that usually takes a lot of money to set up.  Actually having the team all distributed around the world might have advantages in terms of pulling in more influences and perspectives.  Of course you don't need a shared space for a team meeting.  We've run them regularly on different projects at different times.  The ability to have them is a function of team members' availability.  Sometimes teams will form or dissipate based on whether there is sufficient overlap in their schedules. 

Part of the idea behind the AsyncVoter project is to reduce the need for team meetings.  Not that a team shouldn't get together if they want to, but in WSO at least I'd got the sense that people weren't enjoying the meetings, which usually involved voting on tickets in the backlog.  In LocalSupport people were just not on at the same time, or couldn't use hangouts.  I wondered if part of the issue was understanding what to do next.  In a session with Raphael off the back of the "Martin Fowler" scrum I wrote up the following:

```
Using Waffle and GitHub to work out what to do next
---------------------------------------

0) if there are any [open PRs](https://github.com/AgileVentures/AsyncVoter/pulls) then review them and if possible give feedback

1) are there any tickets "in progress" on [waffle](https://waffle.io/AgileVentures/AsyncVoter)? If you'd like to help reach out to the assigned team member to see if you can help

1) is there an estimated ticket in the ready column on waffle that I am interested in working on?
1a) yes --> then start work on it, pairing or not

1b) no --> find a ticket from the backlog (on waffle) that I'm interested in and start a vote (in a scrum, or async)

2) throughout process rather than adding new features, new elements or major refactoring to the existing ticket, freely make new tickets to go on to the backlog (on waffle)
```

Hopefully that was some help.  It seems transparent in my mind as a way of doing distributed Agile work, but there's no reason why others should have that pattern in their minds.  Would be great to have one of those animated videos showing the flow.

Michael and I started off following the above pattern for WSO after lunch.  We went through the open pull requests, "in progress" tickets, the "ready" column and the "estimated" column.  We have an additional column in WSO called estimated for those tickets we have voted on but yet to prioritise:

![WSO waffle board](https://www.dropbox.com/s/o2kc9kx6gimfmx0/Screenshot%202016-11-16%2009.31.25.png?dl=1)

As usual, I agonised about all the different things we seemed to need to get done on WSO, but ultimately followed the "drive by" or "whistle-stop" approach that says finish things that are in the rightmost columns on the board, and if there isn't easy progress move back across the board from right to left so that columns get cleared out.  I'm a terrible person for leaving stuff lying around.  I've also noticed that as you clear stuff up you make more space for new things.  We attacked a ticket in the ready column related to AgileBot that looked simple.  Michael drove for an hour, and we got the functionality we needed, but identified oh so many [refactoring](https://github.com/AgileVentures/agile-bot/issues/45) needs.  I'm still intimidated by the apparent brittleness of the Hubot stack we have on AgileBot.  We followed the drive-by pattern and moved on to pastures new after the "Kent Beck" scrum.

Raoul had joined us in the scrum, which gave us a chance to talk about the need for team meetings on WSO and whether the AsyncVoting was replacing the team meetings effectively.  Michael reflected that there were a few people who attended those meetings who weren't around now.  Neither Michael nor Raoul seemed passionate about re-instating regular long meetings voting on tickets, and I reflected that what I really wanted was the AsyncVoter bot to kick in and keep us regularly voting on tickets.  We've built a little RESTful core, and I'm worried that we have to hook up fast to something that actually benefits the community.  Got to get a slack bot shell working even if it's only semi-automated.

After the scrum I drove on a ticket addressing missing video links on the main WSO.  That was fairly straightforward, and although there's still lots of technical debt to pay down on both WSO and AgileBot, we managed to get a feature out and a bug fix in place while not making the code any worse.  In fact I think we improved the test config for AgileBot and reduced the numbers of line of code in both.   In the ideal world we'd have a bigger team of folks helping us knock off these additional refactoring tickets we keep generating.  In slack Mikael was making some great suggestions about AgileVentures letting people know what they could do to help, given the amount of time they have to spare, e.g. 

* <15 minutes to spare?  Review a pull request, vote on a ticket, or even start a new vote
* <30 minutes to spare? join a scrum and say hi, give us an update on what you're doing, read a project's documentation; observe a pairing session
* <60 minutes to spare? Start getting set up with a code base for a new project
* <90 minutes to spare? Start working on a ticket, join a pairing session

What I'd really love is a performant version of WSO that could give every user a feed of things to do with associated time commitments.  Almost like the feed of unanswered questions from StackOverflow, but a feed of chunks of work to be done on tickets, voting on things, reviewing code:

![StackOverflows unanswered question feed](https://www.dropbox.com/s/pvp2i3q3d3xkpmm/Screenshot%202016-11-16%2009.48.54.png?dl=1)

Maybe that will be easier to achieve than the wall of pairing/meeting live stream video feeds I keep imagining.

###Related Videos:

* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=aYCGCegwjvY)
* [Pairing on AgileBot](https://www.youtube.com/watch?v=qRCeF-IECaU)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=1gMF_0oALyY)
* [Pairing on WebSiteOne](https://www.youtube.com/watch?v=tKqG-pSQcfU)


