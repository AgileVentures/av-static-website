I'm shattered.  Wife has been sick with a fever for four days.  I did all the school pick ups, laundry and cooking on top of work, football coaching, and running 13K last night to keep in with my half marathon training.  All that I could cope, it's the whisky and lying awake till 4am listening to audio comedy to try and stop my mind endlessly re-hashing the soccer game we lost yesterday.  I am just too emotional/analytical for my own good.  Rationally it's just a game, meaningless in the scheme of things, but the interactions with the opposing coaches, tactical positions on the field just play over and over in my left(?) brain driving me to distraction.

Now it's time to focus on work.  I did two hours updating Premium Subscription data last night, but that's still not finished.  At least I deleted all the null user subscriptions so that Michael can get clean data for the AV ML systems.  I'm still trying to process the big social prescribing meeting at the mayor's office that took up most of Friday.  Is that all just a talking shop, or is there something happening, and can I devote any time to it?  More immediate in terms of cash flow there's the APSoc database to finish up, and a proposal to write for mynoteboat.  Let's get down to them.  I've got a new list of issues from the APSoc secretary:

General
=======

1. Can “Save” (of a record) do “Save & Refresh All”?  Is that even possible?
2. Please can all forms, reports etc. have the Society logo at top left?  (Mailing labels excepted)
3. Ensuring that every form stays on the same record as you navigate between forms.  Can this be applied to the reports/address labels as well, please?

My thoughts
-----------

1. Maybe https://msdn.microsoft.com/en-us/vba/access-vba/articles/form-refresh-method-access
2. Shouldn't be a blocker
3. Assuming we can get some mechanism to stay on particular records https://stackoverflow.com/questions/38592699/access-how-to-open-a-second-form-to-the-same-record-as-the-first

Members Table
=============

1. Please can all dates be displayed as dd/mm/yyyy (some fields are currently d/m/yy)

My thoughts
-----------

1. Done - simple edit to table schema format

Member Form
=============

1. “Salutation” should default to “First Name” (or “Known As”, if present)
2. “Country” field not vertically aligned
3. “Member#” and “Last Updated” to be automatically generated and filled in
4. Updating member info on “Subs Paid” form then “save & refresh all” gives wrong display on “Member Form”: “Member Grade”, “Status” and “Expires” all contain numeric values.  Happens with new and updated records
5. “Last Updated” is not being automatically updated when anything changes
6. “Last Updated” and “Date Joined” should not be keyable, but automatic and protected (like “Member#”)
7. Fourth address line (“Address4”) should NOT take the values in the old "Address" field!
8. Can the title for the address fields be just "Address" and then if possible each of the four lines to be labelled 1, 2, 3, 4 to left of the input boxes (yes that will mean moving the 4 input boxes right a little, but that's OK)

My thoughts
-----------

1. Should be possible with VB
2. Fixed
3. I thought they were - at least Member# is but you don't see it immediately - not sure what we have on LastUpdated `AfterUpdate` hook?
4.  I'm not experiencing that issue? guess I have to set up with office 365
5. see 3
6. on my system they are - I've removed the calendar popup on all, but perhaps this is office 365 difference again?
7. fixed
8. first part done - can add numbers later once we've finished anything else tricky

Gift Aid Form
=============

1. All line item fields, when you tab to next field lose data entered – sometimes (it may be me!)
2. If a record line is completed but there's no “To Date” entered, can the Gift Aid flag be checked ON (and OFF if all the “To Dates” are present)?
3. Gift Aid flag set ON is not being kept – is reset OFF following save & refresh – sometimes, maybe, not sure it’s reproducible so may be me!
4. “Expires” field not being displayed

My thoughts
-----------

1. hmm, not for me - need office 365
2. I thought we had added this functionality - and it works for me - have to debug in office 365
3. see 2.
4. Bound column was incorrect - now fixed.

Subscription Payment Form
=========================

1. “Member Grade” should take same values as “Rate Paid”  (ie. “Subscription Rate Paid Table”)
2. If possible “Member Grade” should default to latest entry in “Rate Paid” rather than having to be keyed
3. When “Status” changes to “Paid” or “Expired” then “Newsletter” must be set ON; if “Status” set to other than “Paid” or “Expired” then “Newsletter” is set OFF – unless “NM N/L” is ON when “Newsletter” must be ON

My thoughts
-----------

1. Need clarification - The member grade here should update to whatever is the the last line-item "Rate Paid"?
2. Okay, that's the clarification - will need a VB hook
3. Another VB hook needed

Address Labels
==============

1. Should be set up for Dymo 11354 labels – but hopefully easily changeable
2. Blank fields should not be displayed/printed
3. Layout should be:

  Name
    Address1
    Address2
    Address3
    Address4
    Town
    County Code
    Country

4. Does not display/print lines “Address3” & “Address4”
5. No need for “Member#” on the label
6. No Society logo
7. Prefer it to show just the current record – so can print just the one label easily

My thoughts
-----------

1. Might need to create from scratch using wizard
2. Do that with `CanShrink` setting
3. Sure
4. Added
5. Removed
6. No action needed
7. Hmm


Individual Receipts
===================

1. There should be two different receipts: “Gift Aid” and “Non-Gift Aid”.  The overall design is flexible but they must contain the same information as now and be sized for A5 to print on an A4 sheet.  Would this be more easily done as a form?
2. Again would prefer if just the current record is displayed.
3. "I believe you'll be able to see individual receipt formatting in the "print preview" view."  No I see a blank page; also with the Address Labels.

My thoughts
-----------

1. hmm
2. not sure
3. hmm, need office 365


Membership Types Table
======================

1. Should take the same values as “Rate Paid” on the “Subscriptions Form”  (ie. “Subscription Rate Paid Table”)

My thoughts
-----------

1. did we unnecessarily create an extra table here? there is overlap here - we'll need clarification ...


Overall the priority is clear - I need Office 365 on my PC so I can check the issues the client is experiencing.  Now I seemed to be able to get into some sort of licensing center on my mac and start to download versions of access, but can't seem to do the same on my PC ... ah, hmm, seems they want me to add a TXT record to the agileventures.org DNS ... right then ...

 
