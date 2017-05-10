---
title: NHS Design Sprint Day Four
date: 2017-03-30
tags: wiki, google, thoughtbot, social prescribing, self care, hosting, code climate, lightning demos
author: Sam Joseph
---

![design sprint](/images/design_sprint.png)

I'm getting a bit punch drunk keeping up with all the different AgileVentures projects and driving the NHS design sprint forward at the same time, as well as doing all the admin and legal stuff to get the NHS contracts all sorted before the end of the financial year, which is tomorrow.  I've just about managed to stay on top of the LocalSupport project, but my check ins on the AsyncVoter and Redeemify projects did little more than serve to remind me of the places we are blocked, or I am blocking things.

On Redeemify we're stuck with CodeClimate not working on our pull requests, and no production environment setup.  On AsyncVoter we need to complete the move from Digital Ocean to Azure hosting.  I think I'm going to have to empower some new project managers with greater devops rights to move those projects forward.  On the NHS project we'd wrapped up Tuesday starting to work through with the change manager on some of the different hosted wiki platforms.  The idea is that this was part of the design sprint day two "lightning demos" where we look at great solutions from a range of companies.  Ideally every member of the sprint team would be bringing examples from a wide range of sources and industries.  I can ask the change manager to prepare some going forward, but here's where the lack of members in our sprint team is a real handicap.

At least over the remainder of Tuesday and then during Wednesday morning on Skype we managed to review a good 10 or so hosted wiki solutions.  We paused briefly Wednesday morning to get the timeline straight for the project and the next few days.  The original "tender" had included an aggressive timeline, but our selection as vendor had been delayed.  The change manager and I came up with this adjusted timeline based on that delay and the upcoming public holidays:

* 27th March - Work begins
* 5th April - Prototype developed
* 6th-7th; 18th April - Prototype tested
* 19th-27th April - User feedback incorporated into wiki
* 4th May - wiki is live with moderation

I'm still not clear what the outside constraints are that are driving this timetable.  Talking with the decider it seems there is a need to deliver something fast to help the folks on the ground (commissioners, GPs, patients, carers).  I'm concerned that if one wants to grow a successful community around a technological solution like a wiki then one needs to grow it slowly.  Rushing forward to a public launch of something increases the risk of long-term failure, but these are apparently the time/resource constraints, and we are a "can-do" bunch of folks, so we will do our best.

We looked at eleven different wiki hosting services including:

* Wikispaces
* Wikidot
* Wikia
* Tettra
* Socialtext
* PBWorks
* Ourproject.org
* Nuclino
* eXoCloud
* Confluence Hosted

We worked through the first few with the change manager driving.  Here's us trying to add comments to a wikispaces wiki:

![failing to see added comments on wikispaces wiki](https://www.dropbox.com/s/hn9zulbam61b4pp/Screenshot%202017-03-30%2010.19.20.png?dl=1)

where we were able to create a wiki, but for some reason our posted comments would not show up.   As we moved through the different solutions, I started to see a pattern emerging in terms of a kind of activity script.

1. Create a new page
2. Edit the page
3. Add a heading
4. Add a list
5. Add a document
6. Add (and resize) an image
7. Add a comment

The discussions so far had made it clear to me that HLP staff were also key end-users of the imagined solution, so seeing the change manager (an HLP staff member) trying to navigate these wiki environments was very interesting.  We were getting blocked from signing up to some of the solutions as I couldn't receive my email over the NHS wireless, but we were able to try Tettra, which had a really nice clean interface, reminiscent of the medium blogging tool:

![Tettra's interface, reminiscent of the medium blogging tool](https://www.dropbox.com/s/88nk1nwpyvdc4lm/Screenshot%202017-03-30%2010.23.22.png?dl=1)

The next day we continued with me remote at my home office over Skype with the change manager at NHS headquarters.  We tried wikidot where we found that the process of adding an image was somewhat laborious.  We went back to Tettra to see how adding an image was there and it was pretty straightforward.  Next we tried PbWorks, where the interface was a little busier than Tettra, but there was good support for re-sizing an image:

![Resized image with PbWorks](https://www.dropbox.com/s/74xnvyany1dzhxm/Screenshot%202017-03-31%2009.22.30.png?dl=1)

We moved on to Atlassian's wiki confluence, which I was familiar with from my programming background.  I quite liked it, but I sensed that the interface might be a bit busy, given its usual target audience of tech specialists and programmers.  The problem here was also that I was interacting with the interfaces, and the change manager was just watching over Skype, unlike the day before when I'd been seeing him trying to manage the interfaces himself.  I consoled myself that this was all still really just us doing lightning demos.  We were trying to bring some of the possible alternatives into our minds, rather than conducting usability tests per se:

![Atlassian's Confluence wiki](https://www.dropbox.com/s/pbauj6vdnw45jy9/Screenshot%202017-03-31%2009.19.54.png?dl=1)

We also tried Nuclino which had a nice interface following the approach popularised by the Medium blog that Tettra also seemed to follow:

![the Nuclino wiki](https://www.dropbox.com/s/9wzt2ogq6xdqlpl/Screenshot%202017-03-31%2009.19.14.png?dl=1)

On the ExoPlatform system we got quite lost in the interface and couldn't work out how to even make an editable wiki.  We tried iMeet and Wikia.  On iMeet we didn't get past creating a workspace (I note they've been pinging me every couple of days since, with their best shots at drip emails).  And the sign up process in Wikia took a while and ended up getting left in the background. 

There's more we could be looking at.  It was frustrating that we lost half an hour to trying Skype and Hangouts at the beginning of my remote day.  The NHS firewall appears to block Skype and maybe hangouts.  When I saw Brendan's laptop the next day, it seemed like it might just have been wifi connectivity issues that could have been solved by plugging into a hard LAN cable.  The setup at NHS headquarters is interesting.  Open plan offices with flat screen tvs showing the BBC news channel.  Lots of meeting rooms with no computers in.  Staff work at their computers in the open plan office, but then generally seem to leave their computers to go meet in rooms where they can have discucssions without disturbing their co-workers.  Or make phone calls where they can only talk, but not show.  I would find it extraordinarily frustratingto be in meetings and discussions in the absence of the digital tools (the laptops) where you can really show each other things or collaborate on specifics.  Some meeting rooms appear to have sophisticated remote meeting systems in them, which is ironic when they could be using Skype or Hangouts, although of course there will be security and support constraints that the the NHS must adhere to.

I can see that a large government organisation would be inclined to pay for a "it just works" secure remote meeting system, when Skype and Hangouts require a fair amount of technical know how and support.  Naturally NHS England will be very concerned about the security of all their systems.  It's just a funnily different world than I'm used to where the computer is the primary conduit for both work and conversation.  The HLP team had been interested when I'd shown them Slack the previous day.  Perhaps through working together there can be some side-benefits via me introducing the team to some digital tools and practices that they aren't yet familiar with.

The big question now of course is how to continue the Sprint?  We were getting out lightning demos done, which was supposed to by the morning of day two, but we hadn't seen the majority of our experts, and we needed to make time for sketching possible solutions; although I was questioning whether that made sense since the budget constraints required us to pick from existing solutions ... find out how we handled it all in tomorrow's blog!
