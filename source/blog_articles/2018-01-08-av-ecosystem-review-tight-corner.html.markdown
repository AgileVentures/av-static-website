---
title: AV EcoSystem Review Tight Corner
author: Sam Joseph
---

![tight corner](../images/tight_corner.jpg)

So my estimates were all wrong.  I had hoped to complete the membership database beta for APSoc in two mornings last week, and then be able to move back to things that were arguably more core AV ecosystem.  The task itself was no harder than expected, but the repeatedly crashing LibreOffice Base lost two mornings' work and then slowed me to a crawl as I instituted a variety of different (and increasingly extreme) backup options.  Having missed the deadline for Friday, I've pushed back to the following Friday, and now potentially I lose another week of mornings that I would like to have devoted to the AV ecosystem.  

I'm now weighing up whether I try and combine the dropbox backups I have with git, and I see there's some interesting SO posts about this:

[https://stackoverflow.com/questions/1960799/using-git-and-dropbox-together-effectively](https://stackoverflow.com/questions/1960799/using-git-and-dropbox-together-effectively)

And it looks like it could work.  But I think I'll burn time with set up for potentially not much gain.  I think the key is doing the LibreOffice double save and restarting it each time I make any progress.  It feels late in the day to try and switch to another piece of software such as MSAccess.  Interacting in read/write mode with LibreOffice feels fairly stable.  It's the occasional random lockups, when actually editing the interface, that cause me headaches.  Hopefully for the client it will be solid, but if not I guess I'll have to pay for MSAccess and re-do it all there.

The other thing I've been experimenting with is boosting the allocated memory for the app.  Let's see if that helps today.  Maybe I can sort out the Countries DB to have USA and UK at the top ... I try to create a countries table from scratch, but I have trouble setting the primary key.  And almost immediately I have the spinning beachball of death.  Is this something about the size and structure of the system I've created here within LibreOffice DB?  That I'm using it on Mac? Although I do get similar issues on PC.  Or is it that both my computers are too old and slow?

Anyway I managed to create the adjusted countries table by just allowing the db to create its own primary key, and then finally pasting in data from Google sheets:

![](https://dl.dropbox.com/s/rkw7tt19t85jtqu/Screenshot%202018-01-08%2009.54.07.png?dl=0)

But now I can't insert any records.  Seems like we lost all the auto-value settings on all the table IDs somewhere last week.  I'd better send this revised version off to the client, before they start testing the buggy one I sent on Friday.

> Hope you had a good weekend and apologies again for the deadline slippage.

> We've just been working on the system and if you do run any more tests I'd encourage you to work against this version (rather than the one sent on Friday):

LINK TO BETA VERSION 2

> This one has the the UK and USA at the top of the countries list, and another insert bug fixed (or rather refixed).

> Given that we're behind schedule we'll try and give you daily updates this week.  However - please don't feel obliged to test things daily.  We just want to make sure that when you do get a chance to test that you're working against the latest and greatest.

> Again, we plan to have all your items of feedback addressed by Friday.

> Best, Sam

A tight corner indeed - can I bank effectively and avoid coming a cropper?
