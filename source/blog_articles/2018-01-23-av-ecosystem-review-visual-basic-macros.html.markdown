Right, so now we've got the visual basic macros working, I'm hoping there shouldn't be too many blocks on the APSoc membership database.  Famous last words.  Let's see ... following the database table definitions we need these other automatically updated items:

1) CountyCode --> State + PostCode  
2) Newsletter --> Yes/No; yes if member
3) Full Name --> Title + FirstName + MiddleName + LastName + Letters DID YESTERDAY
4) GA --> Yes if GiftAid is in effect
5) SO --> Yes if Standing Order in effect
6) Overseas --> Yes if member is over seas


We can take care of the first with:

```vb
Private Sub PostCode_AfterUpdate()
  Call State_Province_AfterUpdate
End Sub

Private Sub State_Province_AfterUpdate()
  Me.CountyCode = Me.State_Province & " " & Me.PostCode
  Me.Refresh
End Sub
```

The second with:

```vb
Private Sub Member_Grade_AfterUpdate()
  If Me.[Current Grade] = "Non-Member" Then
    Me.NL = "No"
  Else
    Me.NL = "Yes"
  End If
  Me.Refresh
End Sub
```

The third we did yesterday.  I'm not quite sure how we check 4 and 5 ... For 4 I guess it's if that person appears in the GiftAid table, and the TO_DATE hasn't passed.  Similarly for SO I guess, and for 6) overseas I imagine we just check for country != 'United Kingdom'.  So I could take a stab at those last three but first I'm going to take a crack at the memberno conundrum.

> Currently Member_No is constructed as (Approach formula): 

> Combine(AP_Soc_Member_Types."Code Letter", '/', NumToText(AP_Soc_Members.RecNo, '00000'))

> So in the new system that would be

> CODE_LETTE from MembershipTypes Table / RECNO from Membership Table (as 5 digits with leading zeroes)

> I want the CODE_LETTE deleted from this formula and from the MembershipTypes Table.

> So MEMBERNO becomes just RECNO (as 5 digits with leading zeroes)

Since this is a different kettle of fish.  There's no form update that leads to this - just when a new record is created, so sensibly this is a query rather than a calculated formula, or even just a formatting adjustment on the various forms to show the leading zeros.  Also, it makes me think that we need to set auto-increment and primary key on the RECNO field.  Now it seems we can't make an field with existing values into an auto-increment field, so I've had to hack this up a little:

```vb
Private Sub Form_Current()
  If Trim(Me.RecNo & "") = "" Then
    Me.RecNo = 2000 + Me.ID
  End If
  Me.Refresh
End Sub
```

Where we're basically checking on any form that the RecNo isn't nil or blank and then trying to set it to the new autoincrement value plus 2000 to get it clear of any existing record numbers ... slight downside is that the record number won't show up until the record is saved, and we could use something like `nz(dmax("ID", "tablename"),0)` to grab the next number in advance but let's see what the client thinks.  However I think I'll try to fix a few more things before sending it their way:

> Member Information Form
> NM N/L should work as follows.  (a) If person is a non-member then NM N/L checked should force N/L to be set ON.  (b) If person is non-member then NM N/L unchecked forces N/L to be OFF.  (c) If person is a member then NM N/L has no effect.

> Gift Aid Declaration Form
> If there is a completed record of a declaration, with a blank "To Date" then the "Gift Aid" checkbox should be checked 

And then there's creating some reports ..., can maybe get it all done this week ...
