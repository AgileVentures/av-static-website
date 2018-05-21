---
title: AV EcoSystem Review Damn Estimates
author: Sam Joseph
---

![distraction](../images/estimates.jpeg)

So I had an email from the APSoc client yesterday evening:

> Thanks for Beta 3.  I’ve started having a look at this but not got far.  However some initial feedback …

> General

> 1. Whenever try to do any updates to a table, or use the wizard to set up and edit a query, the system locks me out (seemingly indefinitely) while using up to 20% of CPU and trashing the hard disk.  I end up having to kill the task in Task Manager and restart my machine to clear the problem.  This may well be something peculiar in my system and not down to you – but any clues appreciated.

> 2. Otherwise the forms generally look much better.

> Member Information Form

> 1. NM N/L should work as follows.  (a) If person is a non-member then NM N/L checked should force N/L to be set ON.  (b) If person is non-member then NM N/L unchecked forces N/L to be OFF.  (c) If person is a member then NM N/L has no effect.

> Subscription Payment Form

> 1. Expires field needs a simple list of dates, plus N/A and 9999.  Currently contains lots of duplicates.

> Gift Aid Form

> 1. Doesn't need checkboxes O/S, SO, 5for4

> 2. Also doesn't need Letters, Salutation

> Standing Order Form

> 1. Doesn't display!  Just have a blank pink screen.

> More when I get a chance over the next couple of days.

Which is all great feedback, but makes me think we cannot continue with LibreOffice Base.  Maybe that's reactionary, but I've now created the Standing Order Form twice and although I now have backups of it, it's just randomly gone blank in the latest version that I've shared with the client.

I had been thinking that the lock up problem had only been affecting me when I'd been editing the system and the querying portion of the system had been stable on their platform, as it appears to have been on mine.  To hear that they are having serious lock up issues makes me think there's no real way forward here.  I've got no clear way to debug the crashes that I'm getting; and although I can see some chatter about this on the message boards, I don't think I can afford to invest 10's of hours into tracking down the cause of the crash, particularly when there's no guarantee that it'll even be fixable.  The workflow with LibreOffice Base with the crashes is appalling.

Now maybe I've done something terribly inappropriate with the system importing the legacy data and the way I have the forms setup etc. but I'm tempted to suspect something related to having switched from Mac to PC and back.  It all seemed fine, but maybe some weird line endings got in there.  In particular even though the system is open source, the lack of transparency of the system being developed makes me despair of getting to the bottom of things in short order.  In principle the database file is a zip file that can be unpacked and have its components inspected, but I was unable to unzip when the app was crashing.

I thought LibreOffice Base was a good option as there was a large responsive community and an initial import of the client's data went fine, and an initial setup of various relations went well.  It looked like it has all the functionality that we need, and that's been born out during further development, but the crashing, lack of automatic backup and the monolithic file format makes me think we are better off cutting our losses and switching to Zoho or MSAccess.  The options as I see them are:

1. Continue to push on and try to somehow debug the crashes
2. Try to recreate a version from scratch on PC only (might avoid crashing issue, might not)
3. Set something up in Zoho
4. Set something up in MSAccess

I'm leaning towards options 3 and 4 in that I suspect there's a lower likelihood of encountering the crash issue, and perhaps more cohorent support, although I'm not sure.

So I just got set up with a Zoho account and emailed them about non-profit discounts, but I don't seem to have an XLSX file for the main members db, and although Zoho says it will import mdb files it can't handle the ones from the APSoc dump.  The MSAccess Office 365 offering seems to be only available on PC.  TT-Exchange, which we're already a member of, has MSAccess 2013 for £12, while the main MS page has Office365 for £1.50 a month per user.  Guess I'll apply for the latter, but I get stuck - I start the application process, but then can't select AgileVentures - we already have a charity account, but then I can't log in to it.  Ugh, so stuck on two fronts there.  At least I've pushed forward on both pathways ...

Gah, so I need to ask the client for an XLS export of the member database to even test Zoho, and I maybe need to contact MicroSoft support to even get set up with their charity plan ... I think I'm going to email the client to let them know my concerns and ask for the appropriate files.
