---
title: AV EcoSystem Review What The Hell Was I Doing?
author: Sam Joseph
---

![report](../images/report.jpg)


Hmm, my intention had been to work on the Azure Jitsi setup this week, but the last two mornings were taken up with finishing the AgileVentures monthly report for the trustees.  I did another quick bit of work on the main AgileVentures website to swap outgoing and incoming sponsor logos for Federico and deploy the new Premium member API for Michael.  Those are both now live, and I'm reflecting on whether to do more things that unblock others.  The first thing that springs to mind is the changes to the home page language that Lara has suggested based on analysis of the Google Adwords data.

Other things going on include closing out all these dependabot gem upgrades; and whether we need to shut them off so we can see what we are doing.  Other hanging pull requests (oldest first) include:

* upgrading to Rails 5 (blocked by mercury editor) - Matt
* replacing mercury editor with alternate (medium clone) - Matt
* automate streaming youtube from Jitsi - Lokesh
* add text search form to members page - Ghost
* bug fix for empty github repo - Praveen
* refactoring slack service - Me
* add tawkto chat client - Me
* add calendar support - Sigu
* add sponsor relation - Michael/Marian

I flicked my eyes over other older and unvoted tickets in the waffle board.  So much stuff that could be worked on.  Who knows which are the key set that take us where we want to be, i.e. a clear comprehensible site that allows people to onboard and work productively together on open source charity projects.  Part of me wants to knock off the oldest hanging pull requests by just taking over the work of those who've just dropped away.

However I did also start looking at alternate home page designs as part of thinking about how we can improve performance and do A/B testing on different landing pages in terms of how they bring folks on board.  And maybe that's the most important part of our funnel?  Anyway, here's some of the keyword searches that have had our Google ad served, and the number of click throughs they've received:

| Keyword                    | Quality Score | Clicks | Impr.  |
|----------------------------|---------------|--------|--------|
| code                       | 3             | 8897   | 137126 |
| github                     | 3             | 2588   | 39468  |
| open source                | 3             | 810    | 20893  |
| agile methodology          | 4             | 340    | 7837   |
| [coding]                   | 5             | 236    | 7865   |
| agile method               | 2             | 125    | 2167   |
| agile model                | 3             | 105    | 1279   |
| agile development          | 4             | 73     | 1363   |
| scrum                      | 3             | 58     | 1970   |
| website coding             | 1             | 49     | 787    |
| agile process              | 3             | 44     | 543    |
| Ruby on Rails              | 1             | 33     | 523    |
| applications development   | 1             | 25     | 827    |
| improve programming skills | 2             | 20     | 200    |
| agile project              | 2             | 17     | 379    |
| agile testing              | 1             | 17     | 407    |
| agile process models       | 3             | 14     | 167    |
| app developer              | 3             | 13     | 442    |
| what is agile development  | 4             | 12     | 136    |
| code programmer            | 3             | 10     | 142    |

Quality runs from 1 to 10 and our quality scores are low because these keywords are not appearing on our landing pages.  This means that we have to pay Google more of our ad budget for them.  Apparently our Google ad budget will go further if we can increase the instance of these terms on our home page.  In principle, a straightforward fix, and gets me thinking about getting set up for the A/B test of some alternate home pages, based on what we developed for our design sprint back in June.  

So I go ahead and create a [ticket](https://github.com/AgileVentures/WebsiteOne/issues/1982), actually using part of the form template that we have (should I adjust those?), and reflect on how I still don't know if any of our Google AdWords activity has actually led to any revenue.  I know we've got a couple of thousand additional users, but I don't know if any of them have helped make this whole enterprise more sustainable ...

Anyway, given I have a ticket, that gives me license to create a branch on which to do the work.  Let's review the chunks of text we have on the home page:

*Project Launch Pad*

> AgileVentures is the perfect place to kick-start a project. Our system of openness and lack of hierarchy means anybody can start a project. In fact, many of our current projects were initiated by our own members!

*Crowdsourced Learning*

> By crowd-sourcing our projects, we attract developers from diverse backgrounds: students, freelancers, designers, teachers, and many more from all corners of the globe. Our members are driven by their passion to learn code and make a contribution to society.

*Social Coding*

> We have regular daily meetings (scrums) online where our members get together, discuss projects, share knowledge and plan programming sessions. We love pair programming, nobody works alone in this community!

*Open Source*

> We love all things Open Source, we are fully transparent in our development process, we record videos of all our meetings through Google+ Hangouts, our code is freely available on the web on GitHub and our project plans are publicly viewable on Pivotal Tracker.

So there are things I'd love to change here, but the funny thing is that of the different keywords I'm supposed to be adding we do mention some of them several times:

* code: 2
* github: 2
* open source: 2
* agile methodology: 0
* coding: 1
* scrum: 
* website coding: 0

It makes me wonder if what we need are custom landing pages for our different Google AdWords campaigns, so that folks searching for particular content can land on a page that speaks their language?

Anyhow, I've taken a stab at adding more of the keywords into our langauge.  Just tweaks really, but also to mention the focus on charity and non-profit.  Let's get this up and see:

*Project Launch Pad*

> Want to start a new charity or non-profit coding project? Our community of open source practitioners can help you get started coding and using agile methodology to deliver value quickly to your end users.  

*Crowdsourced Learning*

> By crowd-sourcing our projects, we attract developers from diverse backgrounds: students, freelancers, designers, teachers, and many more from all corners of the globe. Our members are driven by their passion to learn to code and make a contribution to society.

*Social Coding*

> We have regular daily meetings (scrums) online where our members get together, discuss projects, share knowledge and plan programming sessions. We love pair programming, mob programming and shared code review to help everyone learn and improve.

*Open Source*

> We love all things Open Source and are fully transparent in our development process. We record videos of all our meetings through Google Hangouts; all our code can be viewed on GitHub and our project plans are publicly viewable on systems like Pivotal Tracker.

And I get that up in a pull request.  Gosh, I so don't know what's the key place to push on.  Doing things that people ask for is a reasonable first order heuristic, I guess.  Another is clearing up hanging PRs.  The clear pattern that I see is that when I reach out to people individually, that is what seems to have the highest success rate in terms of bringing in new contracts and subscriptions ... hmmm ...
