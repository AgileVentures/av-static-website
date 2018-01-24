I feel pretty rough today.  Couple of funny stomach episodes over the weekend from slight overeating - thought I'd got through, but late in the day and early morning I'm feeling completely exhausted.  Pattern feels a little like some sort of stomach virus. Am I insane to push myself to work each day?  Should I sensibly be taking days off on days like this?  If I was in a company with some sort of sick policy maybe I would? Maybe even then I wouldn't.  It's funny not commuting - so easy to carry on working at home.

Up next is some more logic for the APSoc membership database:

> Member Information Form NM N/L should work as follows. (a) If person is a non-member then NM N/L checked should force N/L to be set ON. (b) If person is non-member then NM N/L unchecked forces N/L to be OFF. (c) If person is a member then NM N/L has no effect.  


> Gift Aid Declaration Form If there is a completed record of a declaration, with a blank "To Date" then the "Gift Aid" checkbox should be checked

and

* GA --> Yes if GiftAid is in effect
* SO --> Yes if Standing Order in effect
* Overseas --> Yes if member is overseas

So these GiftAid/StandingOrder things are more complex - need to query data in another table on each form load/current, or assume the data is good, and proactively set data in members table when an update is made in the forms for GiftAid and StandingOrder forms.  That latter is probably better ...

Otherwise I could use this [DLookup approach](https://msdn.microsoft.com/en-us/library/office/aa172176\(v=office.11\).aspx)

