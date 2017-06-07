---
title: More MediaWiki
date: 2017-04-27
tags: Bitnami, Azure, MyWikis, VisualEditor, SSL, training, wiki, Miraheze, moderation
author: Sam Joseph
---

![mediawiki](/images/MediaWiki.svg)

So the zone file propagated and we can now access our bitnami/azure instance at http://hlp-wiki.agileventures.org.  MyWikis support got back to me having fixed their VisualEditor which is now working for me on my Mac.  That means that MyWikis might actually be a good option for us.  I'm agonizing about whether I burn time on setting up SSL, VisualEditor and other things on our Bitnami/Azure system, against the other less technical things I need to do such as work out a training plan for the NHS staff, a communications/marketing strategy for the wiki release and a moderation plan.  I guess I've got to look carefully at the time that's available over the next 10 days or so.

* May 2nd Tues 2:30pm - HLP Staff training (editing and moderating)
* May 4th Thu - Soft Launch of wiki
* May 8th Mon - Meeting voluntary sector group to talk about wiki
* May 12th Fri - Hard Launch of wiki

MyWiki's pricing seems pretty reasonable, and under the circumstances it seems like we should at least sign up for one of their solutions and pay $15 or $35 for the first month to see if we can get the feature set that we need.  Things that I need to fit into the schedule include:

* Reaching out to 8 other stakeholders that the NHS has recommended I speak to (arrange for week of May 8th?)
* Going back to earlier stakeholders who said they were interested to see what we'd created
* Coordinating with Enterprise Wellness on the moderation (invite Mina to HLP staff training)
* Coordinating with HLP communications on the URL and the launch of the wiki
* Updating the content in the wiki itself 

The gap that I'm really feeling at the moment is knowing whether we need a separate moderation package that the moderators will need extra instruction in, or if the basic elements of MediaWiki will be sufficient.  There appears to be a [moderation extension](https://www.mediawiki.org/wiki/Extension:Moderation), but will it be more complex than we need?

After being distracted by Slack station-keeping for half an hour, I've texted the communications manager to see if she can make the training, and asked about getting wiki.healthylondon.org set up. I've emailed all the remaining stakeholders to ask what their availability is during the week of May 8th, and so I think the critical thing for me to work on now is a training script for Tuesday, and from that I can drive the sets of technical requirements we'll need by each stage.

So the training will need to consist of:

1. Where to go to access the wiki
2. How to create (request?) an account
3. How to search to find a page
4. How to edit a page
5. How to add a link to another wiki page
6. How to upload an image

A big decision is whether we require an approval process for people requesting accounts.  There does appear to be a [stable extension for confirming accounts](https://www.mediawiki.org/wiki/Extension:ConfirmAccount), which I've requested on Miraheze to see how it works.  For the purposes of the Tuesday training, simple creating of accounts is sufficient to have people practicing editing, but if the moderators are going to be approving requests for accounts, then we need that set up for them to see the process.  There's also the moderation part of the training which will include at least some of the following:

1. Approving account requests?
2. Receiving change notifications
3. Approving changes?
4. Reverting inappropriate changes
5. Blocking/Banning repeat offenders

There's a reasonable MediaWiki training video https://www.youtube.com/watch?v=F8irbbwNo2E that talks about the basics and setting up categories and talk pages, but making the above list makes it seem like a critical thing to check for is the email notification support.  I set `$wgUsersNotifiedOnAllChanges` to an array with my email address on our Bitnami/Azure instance but didn't yet receive an update.  I guess I'll request the same from miraheze and maybe ask them how they did it. 

So in summary there's still a lot of little details to work out, both in technical and policy terms.  I really wish I could parallelize all this - I guess making lists is the first step.

### Related Videos

* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=4Kp6RPci8pM)
