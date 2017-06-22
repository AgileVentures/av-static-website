---
title: All in One Day
date: 2017-05-09
tags: blogging, focus, community, sustainability, design sprint, terms & conditions, voluntary sector, wiki, MediaWiki, prototype, moderation testing
author: Sam Joseph
---

![four seasons](/images/four_seasons_one_day.jpg)

So I'm struggling with what each blog should be.  Should it be a focused dive on one thing?  Should I be trying to cover everything that happened since the last blog?  Yesterday was pretty intense.  I could spend the whole blog on the AV community meeting we had at the end of the day yesterday.  There'd been a spurt of conversation in the #av_community_talk channel triggered by Mikael's reaction to my blog of a month ago about having to switch my focus away from AgileVentures.  The discussion was all around trying to understand how we could make AV sustainable.  It was Joao, Matt and myself in the meeting, which we could probably have publicized better, but then who takes the lead on these things?  I'm close to drowning trying to attend to the NHS project, ramping up the new run of the MOOC and keeping AV ticking over.  Perhaps we can repeat weekly ...

I'd really liked all the independent conversation taking place in the #av_community_talk channel and had resolved to stay in the background in the meeting.  I started off well and we went round trying to answer Joao's question about what the AV product is.  We had a good chat, but I noticed that by the end it was me doing most of the talking :-( Joao had to go, but Matt and I carried on a little as I evangelised the design sprint concept.  We didn't end up trying the "Market and Product Validation Checklist" that Matt had mentioned in the chat ... well, anyway, Matt got quite enthused about the idea of doing a design sprint so we'll see.

That all came at the end of a full day, where I was just hanging in there to get emails out to move forward on the private contract that will hopefully sustain me for the rest of year, and the communication with the lawyers about the NHS wiki terms & conditions.  The majority of the day had been taken up getting ready for a usability/outreach session with the [Kensington & Chelsea Social Council](https://www.kcsc.org.uk/); an umbrella group supporting the non-profit sector in the London borough of Kensington & Chelsea.  I have the feeling they are the equivalent entity to the Voluntary Action Harrow (VAH) group who we've built the Harrow Community Network for through the LocalSupport project.  I'd been put in touch with folks at the Kensington & Chelsea Social Council via NHS HLP as a group that were implementing Self Care in their area and might have interest in the wiki project we've developed.  Our NHS design sprint has sprawled out from its initial week (a few weeks back), and I've got a meeting with a different stakeholder each day this week, and I'm using those meetings to get further usability feedback on the MediaWiki system we've deployed.

I'm just about hanging on, making the arrangements to meet everyone, fitting in the travel etc.  I say just about because I had to push the marketing meeting back an hour yesterday at the last minute when I realised that I couldn't be back in time for it.  Not ideal.  I could have tried to do it in a coffee shop in Kensington & Chelsea, but I felt back at the home office would be more efficient.  I've got an extra meetup to fit in this week and I'm not sure where my lunch breaks are going to fit in through the week!  Maybe I need to move the Martin Fowler scrum.  Pat has just said he can't commit to share the hosting of the meeting.  I could move it earlier or even just put it on hold for while ...

Anyhow, we got the marketing meeting done.  At least I'm remembering to review the outstanding action items.  I feel like there must be some better meeting management system somewhere in between Trello and the text notes I'm pasting into Slack, but then again.  The main technical challenge for the day was seeing if I could get the HLP banner into the wiki.  I was getting stuck on it, and managed to hand off the task briefly to Lara, who played with a few settings and came up with a solution.  I had been adding the MediaWiki vector skin to insert a div for the HLP banner/navigation from their main site; but certain mediawiki elements would not move down.   Thanks to Lara's input we got a great mockup editing the common.css for the site:

```css
#hlp-banner {
    background:url('https://www.dropbox.com/s/2yokso0fkd3bivt/Screenshot%202017-05-08%2010.40.18.png?dl=1');
    width:100%;
    min-height:123px;
}
#mw-head{
    margin-top:123px;
}
.mw-wiki-logo{
    background-image: none;
}
```

I had just taken a quick screen shot of the HLP site banner to play with.  I just got this into the main site on the train on the way into meeting Kensington & Chelsea Social Council, and then I also managed to make emails required in the site.  All a bit skin of the teeth, but it allowed me to get valuable feedback at the stakeholders' offices.

I sat down with their strategic health lead and the manager of their Self-care program, and we could have easily been continuing the expert interviews of the previous weeks.  I could tell that there would be lots of great stuff to learn about how K&C was implementing self care, but I couldn't afford to burn all the time on that.  We had to move on to a more testing footing.  We had our working "prototype".  After understanding a bit more about how things worked at KCSC, I convinced them to try out our wiki on their systems.  Simply having them navigate to the site and seeing that it appeared correctly was already useful, particularly given the problems we'd had with some wikis on the NHS laptops/network.

I encouraged the Self-care manager to navigate through the site, try some searches and then some editing.  It was all rather slap-dash from an academic usability standpoint, but there were some clear knowledge gains:

* If you weren't logged in, searching for things did not suggest page creation
* Searches for multiple terms were not matching anything in our system (not much content in there)
* The combination of required email confirmation and the visual editor could lead to a confusing user flow 

Two reasonably high priority items are now to see if we can make the banner fully dynamic, and also whether we can drop the email confirmation requirement, since the moderation step still catches anything a user tries to post.  I'm definitely leaning towards having almost no requirements for becoming a user and push the burden onto the moderation queue - at least at the beginnning when there are few users.  If the moderation burden becomes too heavy, then we can start imposing additional constraints on user sign up; which makes me think I need to get the moderation plan and budget out of my head and onto paper.  Of course the ongoing interaction testing (training?) is what's helping us understand how the moderation will actually work with this system.   We're doing our hard launch on Friday.  I'll take a deep breath; say to myself that we'll address each issue in turn each day of this week and next week will be calmer :-) Taking it one day at a time ...


### Related Videos:

* ["Kent Beck" scrum](https://www.youtube.com/edit?o=U&video_id=_JPjZvDJ2VQ)
* [AV Community meeting](https://www.youtube.com/watch?v=4gqu79SLxRc)
