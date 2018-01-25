Another day another little bite of progress on MSAccess for the APSoc.  A bit of googling around I think I can see how to have logic related to the subforms update the parent form data, e.g.

```vb
Private Sub TO_DATE_AfterUpdate()
  If Trim(Me.TO_DATE & "") = "" Then
    Me.Form.Parent.GA = "Yes"
  ElseIf Me.TO_DATE < Now() Then
    Me.Form.Parent.GA = "No"
  End If
  Me.Form.Parent.Refresh
End Sub
```

Now I just need to do the same thing for the standing order form, and for good measure I would out how to make these methods get called whenever we view the form, so that should ensure that the data is always consistent.

```vb
Private Sub Form_Current()
  Call Form.StandingOrders_subform.Form.CANX_DATE_AfterUpdate
End Sub


Public Sub CANX_DATE_AfterUpdate()
  If Trim(Me.FROM_DATE & "") = "" Then
    Me.Form.Parent.GA = "No"
  ElseIf Trim(Me.CANX_DATE & "") = "" Then
    Me.Form.Parent.SO = "Yes"
  ElseIf Me.CANX_DATE < Now() Then
    Me.Form.Parent.SO = "No"
  End If
  Me.Form.Parent.Refresh
End Sub

```

and so I can do similar to ensure that GiftAid form will update similarly whenever the form loads:

```vb

Private Sub Form_Current()
  Call Form.GiftAid_subform.Form.TO_DATE_AfterUpdate
End Sub 

Public Sub TO_DATE_AfterUpdate()
  If Trim(Me.FROM_DATE & "") = "" Then
    Me.Form.Parent.GA = "No"
  ElseIf Trim(Me.TO_DATE & "") = "" Then
    Me.Form.Parent.GA = "Yes"
  ElseIf Me.TO_DATE < Now() Then
    Me.Form.Parent.GA = "No"
  End If
  Me.Form.Parent.Refresh
End Sub
```

Okay, so I think that's all the technical issues.  I'm sure there's other things I've missed, but I think I'll do a pass at some reports tomorrow AM and then try to deliver the beta version for a new round of fixes from the client before the final release ...
