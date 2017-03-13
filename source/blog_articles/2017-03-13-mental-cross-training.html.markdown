Apparently everything is complete in the moment.  Nothing else is needed.  The truth is there in immediate experience and everything else is the flapping of our cortices that will just subside if we simply watch and don't judge.  Sounds wonderful.  Not quite there myself.  Can't stop my frontal lobes from getting all agitated about work, about coaching my twin boys soccer team.  At least coaching the soccer team gets me out of the house at the weekend and stops me obsessing continually about work :-)

It's relationships with colleagues, children, their parents that are the stickiest.  Did I say the right thing? What does he think, she think? I should have done this, I will do that in future.  I won't go into to the details of the weekend's soccer antics, but my mental cross training was dropping into a couple of productive Premium F2F sessions Sunday afternoon.  Marian and I stamped out a quick JavaScript feature in EventManager, and Michael and I did similarly for data-mining our Slack data.   In the background I pushed out the latest changes to WebSiteOne, which include:

1. Adjusting the Google+ login to the new Google logo
2. Having the newly signed up users redirect to the "getting started page"
3. Fixing the a bug that was exlcuding the SHF-project from the recently active project listings
4. Fixing a bug I introdduced for the flash message backgrounds

Since I pulled all the changes from master through to develop and now started using the same rebase deployment strategy as I do on LocalSupport, the git commit logs are now somewhat easier to read.  A big part of this is using the GitHub "squash" strategy when merging pull requests, so that however many commits are in the pull request that are all collapsed into a single summary message:

```
* 6f42b57 2017-03-10 | Adjusting G+ buttons (#1592) (HEAD) [Sam Joseph]
* 74a117e 2017-03-10 | make sure we redirect to getting started after oauth signup (#1588) [Sam Joseph]
* f8c7e8d 2017-03-09 | Fix most recent commit update job for projects with hyphens in repo name (#1585) [mtc2013]
* b869e00 2017-03-09 | Add charity id to footer (#1586) [mtc2013]
* 83e90aa 2017-03-08 | fix for missing flash background with associated test (#1580) [Sam Joseph]
* e669512 2017-03-08 | get users onto getting started after sign up (#1576) [Sam Joseph]
```

The G+ buttons adjustment was done by a new member who found us through the ["up for grabs"](http://up-for-grabs.net/#/) open source site. We've had LocalSupport and WebSiteOne connected to there for nearly a year, and I think this possibly the first contribution we've had through that mechanism.  About a year ago we made WebSiteOne a "priority" project, with the idea that only Premium members could make contributions.  The intention was to try and ensure that those contributing were serious about staying involved, as well as help generate revenue to cover the cost of hosting the servers.

If memory serves I've gently turned away a handful of people, directing them to the LocalSupport, MetPlus or ProjectUnify projects.  It's certainly served to keep down the number of contributions, and helped motivate me to give extra to support and attention to the few Premium members who have submitted pull requests and bug fixes.  I could have done the same with the latest contributor, but he was so enthusiastic and self-deprecating, and I'm starting to question the whole "priority" model, so I gave him a fair amount of support during the week in terms of managing git branches, PRs and so forth.  He actually grabbed a ticket that I had been planning to work on, but I was happy to hand it over.  It might have taken me less time to get it done, but I think it was a good learning experience.  What's not clear now is whether we'll have any further contributions or if our efforts to onboard the person will lead to any more.

We're still struggling with the economics of AgileVentures.  Every company faces the challenge of carefully selecting new staff and then investing in them through onboarding and training them, which can be painful lost deposit if the person leaves sooner rather than later.  If we emphasize to people the costs of onboarding and helping them we run the risk of quashing their enthusiasm and dis-incentivising them, just at the point where they perhaps need most help and support to buoy them up and give them confidence to contribute to an open-source project.  I was just hearing about CodePath, which sounds like it has a much more sustainable model.  They deliver 8-week evening courses to help developers level up in mobile coding.  It's all free but highly selective and maintained as free with contributions from A-list technology companies.

AgileVentures was born from the "welcome to everyone" Massively Open Online Class (MOOC) spirit, and I still love that big-tent idea.  Selectivity and filtering frustrates me.  I want information open for all to browse.  I love full transparency, but it does seem like going for high selectivity of engineers and companies is a way to get a sustainable beachhead established that one can spread out from.  That said FreeCodeCamp shows a welcome all philosophy and makes use of an automated self-filtering processs.  I don't think AgileVentures can suddenly pivot to something like CodePath, but perhaps our ongoing changes to the flow through the WebSiteOne interface can introduce a self-filtering process so that developers and nonprofits can find their way into the right groups of folks to generate maxmimum value for all.  It continues to be a process of heavy mental cross training to get us to that bountiful area of sustainability ... :-)

###Related Videos:

* ["Martin Fowler" scrum](http://youtu.be/f_jXT8JCA4A)
* ["Kent Beck" scrum and WSO kickoff](https://www.youtube.com/watch?v=teoB6i5lCOE)
* ["Bob Martin" weekend scrum](https://www.youtube.com/watch?v=2SQgXH-u79k)

