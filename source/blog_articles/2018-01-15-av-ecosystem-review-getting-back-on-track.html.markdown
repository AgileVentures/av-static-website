---
title: AV EcoSystem Review Getting Back on Track
author: Sam Joseph
---

![track](../images/track.jpeg)

I'm a bit of a wreck this morning.  I didn't sleep well - woke up at 4am brain full of kids football.  I did write down a new drill for training (I coach 7aside soccer for nine year olds) and then listened to standup comedy on audio for 2 hours to stop my brain endlessly turning over the same thoughts again and again.  Not dissimilar to last Sunday night/Monday morning.  Emotionally charged football matches.  This weekend's was less intense (draw instead of a loss), but I worked three hours in the afternoon to get the APSoc's membership database recreated on MS Access now that I got the green light from the client to switch.  This is all because LibreOffice is crashing too frequently and ZohoCreator looks like it doesn't might not have all the functionality we need.

I made reasonable progress yesterday although I was popping paracetamol and ibuprofen to push on in the last hour.  I think I've got us back to the place we were in LibreOffice, and actually got a few more supporting tables to populate the dropdown lists correctly.  However there's a clear additional chunk of functionality that I hadn't really been focusing on.  A load of specification for default values, computed fields and so forth.  I'd love to have a break from the project this week, so I think the thing to do is label what we have as an MS Access alpha version and re-work the schedule.  Here are screenshots of the different forms done in Access.

![](https://dl.dropbox.com/s/ydoubyw95wb1zhc/Screenshot%202018-01-15%2009.28.44.png?dl=0)

![](https://dl.dropbox.com/s/eoi4nm5e655wnhq/Screenshot%202018-01-15%2009.29.14.png?dl=0)

![](https://dl.dropbox.com/s/rel3yqy4ur1rujc/Screenshot%202018-01-15%2009.31.58.png?dl=0)

![](https://dl.dropbox.com/s/jqch3s2dbyujrzi/Screenshot%202018-01-15%2009.32.19.png?dl=0)

Wonder if I should set up a Trello board for all the different bits of functionality?  I guess I'll get the new schedule approved.  The old schedule had been:

* by Dec 8th - screenshots of membership database interface for you to approve
* by Dec 15th - alpha version of membership database for you to approve
* by Jan 5th - beta version of membership database adjusted as per feedback
* by Jan 31st - release version of membership database adjusted as per feedback, including full migration 

so if we have the alpha version now, then perhaps we can set the beta version for Jan 26th, and then release version on Feb 9th, so that's

* by Jan 26th - beta version of membership database adjusted as per feedback
* by Feb 9th - release version of membership database adjusted as per feedback, including full migration

which I put in an email to the client:

> Thanks for your email - hope you had a good weekend.

> We now have an alpha version in MSAccess, which you can access here:

[DROP BOX LINK]

> In case you don't have a copy of MSAccess please find screenshots of all the forms attached.
Thanks so much for all the detailed feedback over the last few weeks.  I believe we have a good chunk of the issues addressed in this MS Access alpha version, but certainly not all. In particular, as you noticed, the majority of fields are not folllowing the defaulted/calculated/midified rules from the database definition.   This is simply as we hadn't started work on implementing them yet.   Please rest assured that they will be implemented in the next version.

> Given the software issues we encountered with LibreOffice we'd like to propose the following new schedule for delivery:

> * by Jan 26th - beta version of membership database adjusted as per feedback
> * by Feb 9th - release version of membership database adjusted as per feedback, including full migration

> Our sincere apologies for the slippage and we hope the new schedule meets with your approval.

> Best, Sam

and I'll send that later today.  Now what I really want is some coffee and perhaps I can dive into my outstanding pull request on updating the WSO Premium model so that it can support the sponsor relations we need to more accurately represent our Premium membership data ...
