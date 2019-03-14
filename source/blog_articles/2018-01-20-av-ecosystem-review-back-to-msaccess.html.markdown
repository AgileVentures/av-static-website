---
title: AV EcoSystem Review Back to MSAccess
author: Sam Joseph
---

![msaccess](msaccess.png)

Another week, and I need to switch back to the APSoc project and its continuing development on MSAccess.  I did manage to complete the new Premium-Subscription data model update in WebSiteOne last week, but I did not manage to update all the data to take advantage of it, and I still haven't completed the monthly report to the AV trustees.  Anyway, let's do a chunk on the APSoc project.  The client apparently has Office 365 (aka Office 2016), so arguably I should be working on the same, but since what I've created so far seems to be working for them okay, I'm tempted to continue in MSAccess 2007 and see how far we can get.  I think we've now got access to the latest Office products through our charity status, but I'm worried about burning hours on install and getting used to the new interface when I want to properly wrap by head around all the nuances of the data requirements from the client.

I guess I can kick off a download of the latest office in the background without investing too much time.  Although it seems from trying to log into the service center that our account isn't recognized.  Just the kind of thing I could burn time on.  Let's get into the client requirements.  MS send me a verification code, so I follow through on my windows machine ... the other issue is that I'm on Windows 7 Ultimate and I've no idea how that will work (if at all) with Office 365.  Anyway, client requirements.  I'm still conflicted about whether I should move all this into a Trello, but taking their last email, we've got the subscription payment form "rate paid dropdown" including the items they want.  They want the membership number to change somehow:

> Currently Member_No is constructed as (Approach formula): 

> Combine(AP_Soc_Member_Types."Code Letter", '/', NumToText(AP_Soc_Members.RecNo, '00000'))

> So in the new system that would be

> CODE_LETTE from MembershipTypes Table / RECNO from Membership Table (as 5 digits with leading zeroes)

> I want the CODE_LETTE deleted from this formula and from the MembershipTypes Table.

> So MEMBERNO becomes just RECNO (as 5 digits with leading zeroes)

I'm going to leave that for the moment.  They also want to be able to flip between forms and stay on the same record.  That isn't happening by default in Access 2007.  There's an [SO post](https://stackoverflow.com/questions/38592699/access-how-to-open-a-second-form-to-the-same-record-as-the-first) on that that might help, but again I'm tempted to leave this.  Although I can't help myself googling a little more.  There's a very old [Arstechnica post](https://arstechnica.com/civis/viewtopic.php?f=20&t=745702) and a few other forum posts on the topic for where we might have buttons to link to other forms that would open that new form with the same record.  Seems fiddly, will get into that later.

The status and expires dropdown on each form I fixed up the week before last.  The outstanding issues from the last email (which admittedly was feedback on the last LibreOffice version) are defaults for the GA Type dropdown and the Member No and Member Grade.  Let's start on those ... For the GA Type dropdown the two values required are "All donations from" and "Single donation"; apparently the first of these should be the default.   I use a value list here instead of a separate table.  Seems pretty straightforward.  I've already got "Member Grade" and "Date Joined" defaulting correctly in the main member form.

I notice that some of the dates aren't fitting in their boxes, and switch them all to the more compact general date format, as well as reflecting on how it would be nice if the surrounding "member form" on each form was just some sort of single boilerplate ... but anyway, this all brings me to the various notes on the data model about default values for different fields in the membership database.  Previously I was trying to create fields in the database tables that were automatically generated from other fields, but that didn't seem to work.  I guess I can set default values, but really we want the combined values like full name to be recalculated on the fly ... I find a [good article](http://allenbrowne.com/casu-14.html) on using queries of calculated results.   So I implement that, but it only works some of the time and I'm not sure why ... an initial problem was that I had the macros disabled.  Switched those on, but it still only worked sometimes.  I ended up adding a series of refresh statements and it now seems to have worked:

```vb
Option Compare Database

Private Sub First_Name_AfterUpdate()
  Call Last_Name_AfterUpdate
End Sub

Private Sub Last_Name_AfterUpdate()
  full_name = Me.First_Name & " " & Me.Middle_Name & " " & Me.Last_Name & " " & Me.Letters
  Me.[Full Name] = full_name
  Me.Refresh
  Me.Recalc
  Me.Requery
End Sub

Private Sub Letters_AfterUpdate()
  Call Last_Name_AfterUpdate
End Sub

Private Sub Middle_Name_AfterUpdate()
  Call Last_Name_AfterUpdate
End Sub

Private Sub Title_AfterUpdate()
  Call Last_Name_AfterUpdate
End Sub
```

This is exactly why I did not want to spend an hour trying to download Office 365 as I suspected it would take me an hour to ensure that I could have calculated fields working, which is the key thing for getting most of the rest of the functionality that the client wants ...
