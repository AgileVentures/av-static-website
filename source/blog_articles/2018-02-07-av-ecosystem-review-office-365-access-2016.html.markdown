Right, so we're approved for nonprofit use of Office 365, for £1.50 per user per month, and I have Office 365 (32bit - don't ask) installed on my PC.  The next step is to try and replicate the bugs that the client is experiencing.  I'll start with the GiftAid checkbox failing to update, and I do indeed get the same issue as the client reported.  The TO_DATE AfterUpdate hook is still in place, and it all seems to work when I step through it using the debugger ... and it's working when I turn off the debugger, hmm.

The issue seems to occur on start up where the initial flag status is not necessarily reflecting the current data, and then you have to make changes a couple of times before it all kicks off.  Maybe a form load hook could fix that, as well as maybe a record save hook?  Seems like record save is not recommended, and I already have a `Form_Current` hook.  It occurs to me that the problem is that we have multiple records in the sub form and that when we are calling the subroutine when the form becomes current that we are updating based on the first record in the subform.  Originally we have the Form_Current method as:

```vb
Private Sub Form_Current()
  Call Form.GiftAid_subform.Form.TO_DATE_AfterUpdate()
End Sub
```

So I google around and try to see if we can loop through the records in the subform and run the subroutine on each ... the main problem seems to be that I can't grab a RecordsetClone from `Form.GiftAid_subform.Form`, since that doesn't seem to exist as an entity the first time the form becomes current, which is strange since there's no complaint about calling the `TO_DATE_AfterUpdate` method on it ... so I try to hedge and check that we have a Form object to talk to, but the following doesn't work for me.

```vb
Private Sub Form_Current()
  If Not Form.GiftAid_subform Is Nothing Then
    Set SubForm = Form.GiftAid_subform.Form  ' <-- errors out here
    If Not SubForm Is Nothing Then
      Set rs = SubForm.RecordsetClone
      Do Until rs.EOF
        Call Form.GiftAid_subform.Form.TO_DATE_AfterUpdate(rs)
      Loop
    End If
  End If
End Sub
```

I've also updated `TO_DATE_AfterUpdate` to take an optional parameter so we can specify the particular record that we want to be checked for a change in the `TO_DATE`:

```vb
Public Sub TO_DATE_AfterUpdate(Optional ByRef Record As Recordset = Me)
  If Trim(Record.FROM_DATE & "") = "" Then
    Record.Form.Parent.GA = "No"
  ElseIf Trim(Record.TO_DATE & "") = "" Then
    Record.Form.Parent.GA = "Yes"
  ElseIf Record.TO_DATE < Now() Then
    Record.Form.Parent.GA = "No"
  End If
  Record.Form.Parent.Refresh
End Sub
```

but we can't have `Me` as a default value.  Anyway, I switch from trying to check for a null object to using `Try Catch` but although I have the syntax right it gets complaints:

```vb
Private Sub Form_Current()
  Try
    Dim rs As DAO.Recordset

    Set rs = Form.GiftAid_subform.Form.RecordsetClone
    Do Until rs.EOF
      Call Form.GiftAid_subform.Form.TO_DATE_AfterUpdate(rs)
    Loop
  Catch ex As Exception
  End Try
End Sub
```

In the process I work out how to do block commenting in VB and a few other tricks, but I'm not really getting anywhere.  I could be setting the `Me` in another line,  but I'm questioning this whole approach.  I comment out the whole `Form_Current` operation and things seem to improve.  Maybe the fix here is just removing the `Form_Current` methods.  Have we got two separate issues of the consistency of the data in the starting setup, and consistency once a change takes place?  I'm not sure that I can guarantee that loading a form with inconsistent data will correct all problems with that data ...?  It seems possible in principle but I don't know that I can justify burning more time on `Form_Current` ... and every little bit of complexity here has to be manually tested.  At least Access isn't crashing and burning like LibreOffice Base ...
