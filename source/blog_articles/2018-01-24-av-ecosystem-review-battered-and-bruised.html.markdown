I feel pretty rough today.  Couple of funny stomach episodes over the weekend from slight overeating - thought I'd got through it, but late in the day and early morning I'm feeling completely exhausted.  Pattern feels a little like some sort of stomach virus. Am I insane to push myself to work each day?  Should I sensibly be taking days off on days like this?  If I was in a company with some sort of sick policy maybe I would? Maybe even then I wouldn't.  It's funny not commuting - so easy to carry on working at home.

Up next is some more logic for the APSoc membership database:

> Member Information Form NM N/L should work as follows. (a) If person is a non-member then NM N/L checked should force N/L to be set ON. (b) If person is non-member then NM N/L unchecked forces N/L to be OFF. (c) If person is a member then NM N/L has no effect.  

Supported by:

```vb
Private Sub NMNL_AfterUpdate()
  If Me.[Current Grade] = "Non-Member" Then
    Me.NL = Me.NMNL
    Me.Refresh
  End If
End Sub
```

We also need the following

> Gift Aid Declaration Form If there is a completed record of a declaration, with a blank "To Date" then the "Gift Aid" checkbox should be checked

which I think is a replica of the first item in this list

* GA --> Yes if GiftAid is in effect
* SO --> Yes if Standing Order in effect
* Overseas --> Yes if member is overseas

So these GiftAid/StandingOrder things are more complex - need to query data in another table on each form load/current, or assume the data is good, and proactively set data in members table when an update is made in the forms for GiftAid and StandingOrder forms.  That latter is probably better ...

Otherwise I could use this [DLookup approach](https://msdn.microsoft.com/en-us/library/office/aa172176\(v=office.11\).aspx)

I'll just knock up the overseas update:

```vb
Private Sub Country_AfterUpdate()
  If Me.Country = "United Kingdom" Then
    Me.O_S = "No"
  Else
    Me.O_S = "Yes"
  End If
  Me.Refresh
End Sub
```

And work on the cross-form operations tomorrow.

