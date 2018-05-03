The start of a new year; some of the same conflicts remaining, though.  Leaving off from the last week of last year, I really want to adjust our Premium membership domain model to more accurately reflect the way in which members up and downgrade.  Perhaps even more critically I want to set up private event functionality that would automate some of the process by which potential members try out premium sessions and get presented with the option to upgrade.  Unfortunately I don't think I can work on either, as I must deliver the beta-version of the membership database to the Anthony Powell society.  The timeframe is entirely of my own making of course, and it will be reasonably interesting and pleasing to get the membership database done, but I've not got much interest from many Premium members in my LibreOffice development work ...

The concept behind AgileVentures is that folks can skill up by working/observing real projects with real customers as they progress.  The problem seems to be that in many cases it's not the latest new tech that's needed to get the job done.  Or maybe it's that the process of learning through client work is just too traumatic?  Anyway, if I want to make time to add new features to WebSiteOne I'll need to crack on with the APSoc database ...

Here's the list of issues with the alpha version that the client identified:

* MembershipTypes DB
  - Doesn't need "Code Letter" field

* Countries DB
  - Can we have UK and USA as the top two please as they're the most used

* Member Information Form
  - Unable to add/save a new member record – see error below! – probably applies to all forms
  - Cannot key into "Mobile Phone" – cursor won't go there!
  - "Local Group" needs to be correctly populated – maybe we need a table of the groups?  The groups should be: London, Baltic, Germany, Great Lakes, Toronto, NE USA, Australia, California, Texas
  - Switch positions of "N/L" and "NM N/L"
  - Tab sequence should be – see Member_Form_Tab_Seq.pdf – similar sequences for other forms.  Unnumbered keyable fields do not need to appear in the tab sequence (but could after numbered fields)

* Membership DB
  - We can remove the fields: “Gift_Donor”, “Fax”, “Diet_Rqts”, “Address”, “Crap”, “C_N” – see comments to the DB specification
  - Add the field “Address4”
  - Reformat the content of “Member_No” 

* Subscription Payment Form
  - Don't need "Member Card" field as we don't use membership cards
  - "Amount Paid", please can it display with a £ sign
  - "Pay Method" dropdown needs populating; options should be: PayPal, Credit Card, Cheque, Bank Transfer, Standing Order, Cash, Other.  Worth putting this in a new table so it can be changed easily?
  - "CC No" and CC"Expiry" fields can be removed as we don't store this
  - "Rate Paid", please can the items in the dropdown be sequenced as
  - Whenever try adding a record of new subscription I get:

![](https://dl.dropbox.com/s/fleeiuezj046z4b/image003.jpg?dl=0)

  - Removal of fields means "Notes" could be positioned to the right of the payments lines to reduce the size of the form
  - Keying say "15" in a date field adds the date from the field above or is it the last date enetered?), unless the field above is blank

* Subs DB
  - Remove "Member Card" field as we don't use membership cards
  - Remove "CC No" and CC"Expiry" fields as we don't store this
  - Form doesn't need to show "Letters", "Known As", "Salutation"

* Gift Aid Declaration Form

  - If there is a completed record of a declaration, with a blank "To Date" then the "Gift Aid" checkbox should be checked
  - Declaration line items, can the fields be less wide, and then again "Notes" can go to right of the line items and form size reduces
  - Same error as in Subscription Payment Form when adding a record

* Standing Order Details Form
  - This is a duplicate of the Gift Aid Declaration Form
  
  
Lots to do here - I'm going to start with the section on the Membership information form:

* Member Information Form
  - Unable to add/save a new member record – see error below! – probably applies to all forms
  - Cannot key into "Mobile Phone" – cursor won't go there!
  - "Local Group" needs to be correctly populated – maybe we need a table of the groups?  The groups should be: London, Baltic, Germany, Great Lakes, Toronto, NE USA, Australia, California, Texas
  - Switch positions of "N/L" and "NM N/L"
  - Tab sequence should be – see Member_Form_Tab_Seq.pdf – similar sequences for other forms.  Unnumbered keyable fields do not need to appear in the tab sequence (but could after numbered fields)

I knock off some low hanging fruit by switching the positions of "N/L" and "NM N/L".  I confirm that the "Mobile Phone" can't be entered - can't see anything in the settings about why it wouldn't work, so I delete it and copy over the "Work Phone" text box.  That doesn't fix it, hmm.  I realise the problem is that the text label above it is too large and preventing cursor selection, so I resize that and we have two minor issues fixed.  I'm tempted to work on other less threatening issues like the tab sequence but this inability to save is likely to be the big blocker, so I'm going to hit that next.  I replicate the error:

![](https://dl.dropbox.com/s/yi6szspyshvrmgl/Screenshot%202018-01-02%2010.29.22.png?dl=0)

So the problems seems to be that our automatically added ID columns from the data import are not set up as primary keys that will get an autoincremented ID added with new records.  That's a simple fix thanks to [this forum post](https://forum.openoffice.org/en/forum/viewtopic.php?f=61&t=64324), and that now works fine, although I can see all sorts of little niggly issues now that I start entering data rather than just focusing on the form layout.

Ironically, just adding UK and USA at the top of the countries field turns out to be non-trivial, but I can clearly do it if I re-import the countries data.  Let's check the process of adjusting the tab sequence to flush out anything else that could turn out to be harder than I expect.  The client has sent this diagram of the tab sequence they want:

![](https://dl.dropbox.com/s/gw06wl2inhioh69/Screenshot%202018-01-02%2010.50.01.png?dl=0)

and sorting that isn't too hard once I find [this forum post](https://forum.openoffice.org/en/forum/viewtopic.php?f=39&t=9484) that helps me navigate to the tab ordering tool.  Of course now LibreOffice is gradually slowing, and I'm worried it's going to crash again.   I think I've worked out the trick, to avoid losing too much work, of saving not just in the form view, but also in the main database page.  Now it's spinning beachballs on each screen.  Guess it's time to grab a coffee ...

Still stuck - I kill it, recover the db and lose only a tiny bit of work.  I make changes to the tab order, save, and that seems to work.  There's a chunk more to do, but with a run up I could maybe get it all done tomorrow ...
  
  
  
