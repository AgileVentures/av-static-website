---
title: Non Pairing Day
date: 2016-10-12
tags: Async Vote PR pull request metatags SEO Elm ocaml meetup
author: Sam Joseph
---

So another day on non-pairing while me knee heals.  Managed to crunch through what feels like two or three async votes on LocalSupport and WebSiteOne.  That's starting to flow nicely, although I would love to get the AsyncVoter project up and running so I don't have to manage the logistics for that.  Main hangover business from yesterday was getting some example code up related to SEO with meta-tags on LocalSupport.  A CraftAcademy bootcamp graduate had put in a great pull request, and after I'd dithered about how to give feedback, we'd agree I'd push up a separate branch.  Ironically in the meantime, I was passing along Pat Bolger's fantastic advice about having all team members review PRs to the LocalSupport and WebSiteOne projects, and while I'd dithered, Raoul and John had both given feedback on that PR covering several of the points that I'd been thinking of.

There was still some refactoring work that wasn't covered, and I thought it was easier to show rather than to explain, so I pulled some of the controller logic into `before_filter`s and pushed that up.  I also refocused the cucumber story and fixed some of the issues Jon and Raoul had raised.   What was super-awesome was to see John and Raoul interacting in the PR like so:

![raoul answering jon's question](https://www.dropbox.com/s/029rqql7qrb5fdn/Screenshot%202016-10-11%2010.38.27.png?dl=1)

In my book this is the total antidote to the developer who's intimidated to submit PRs for fear of having someone criticise their code.  Here, Jon, one of our more experienced developers is perfectly comfortable just asking what 'og' means?  And Raoul, another experienced developer is supplying the answer. No shame, no embarrassment, just learning; and all this in the pull request of a recent bootcamp graduate.  Huge credit all round.

Part of the reason I had wanted to play with my own branch was to look at the app holistically outside of the GitHub view of the pull request.  Having tweaked a little (you can see my [branch here](https://github.com/AgileVentures/LocalSupport/pull/371)), I saw that while the new code was allowing our individual organisation show pages to have a more specific (and search-engine friendly) title, the changes were also removing some of the default meta-tags from the rest of the site.  After the "Martin Fowler" scrum, where I was pleased to introduce Kenyan Bootcamp founder Sigu to MetPlus project coordinator Pat, I did a quick hangout with the bootcamp graduate.  Not required, but I sensed that between John, Raoul and myself, we were at risk of overloading our new team member.  Having a hangout might have made that worse, but I went for it.

I was pleased that I did.  I think we were able to reach a good shared understanding, and find that yes, the new code was removing some functionality from the existing system, but, shock, there weren't any tests for the meta-tag stuff that was removed.  We talked about how some default methods in the ApplicationController could fix this, but also how that was maybe sensibly the subject of another pull request.  It would be easy to be strict here and say, "hey, you need to not remove any functionality in this PR"; but if that functionality doesn't have tests, then that's not really in the remit of the person submitting the PR, particularly if they are a volunteer.  Far better to get the current PR fixed up and pulled in and create refactoring tickets for the other tasks, which allows the original submitter to get a sense of completion, keep the team in the loop by voting on those other tickets, and allow someone else to jump in and help with tickets, or the original submitter to carry on if they want - great flexibility?

This all fed into a dry run of my Rails Remote conference talk on learning through pull requests, which I was really pleased to get done and share with the AV premium members and get feedback.  The key question seems to be how can I get myself to talk slower without a teleprompter? :-)  And that was pretty much the day. I reviewed other PRs, had little chats in Slack, posted to the MOOC TAs to say that the next week's material was ready for review.  Really not sure what to do with the AV102 course and the MOOC support infrastructure there when we go self-paced next year ...

One other thing that I did do as part of an async vote on a [story](https://github.com/AgileVentures/WebsiteOne/issues/1319) about coordinating our events with Meetup.com.  I'm an admin on the RemotePairProgramming group there, which keeps getting new sign ups but has been kind of dormant recently.  Actually as part of the discussion of that ticket I went ahead and created a parallel repeating event on Meetup to mirror the "Kent Beck" scrum, and several people signed up and we had one new person join the scrum and tell us about the Elm and OCaml stuff they were doing.  That was really cool, particularly since I was able to do another pair hookup and new premium member, Sasha, and Elm/OCaml dude, Junior, chatted Elm and AgileVentures for an hour or so off the back of the scrum.

I just love manual prototyping!  Coding and automating is great, but it's so lovely to validate that the thing you're about to automate (async voting, event syncing with another system) will actually reap some dividends.  So it was a non-pairing day for me, but I loved helping bring some others together, even if only for a chat :-)  


Related Videos:


* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=BLM8cmLBkWc)
* [LocalSupport PR review](https://youtu.be/S-zJT6rp-Xo)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=SfMb5n6Xsrs)
