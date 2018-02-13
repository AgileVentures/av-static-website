Urgh, so it's really tricky to move through the elements in a subform in Access, so I just give up and remove the `onCurrent` code and work through all the rest of the outstanding requests, sending the client a new beta and following email:

> Hope you're well.  We've made some good progress, but not as much as we'd like.  A good portion of time has been spent on the phone with Microsoft ensuring that we have Office 365 Access via the nonprofit plan so that we can ensure that any issues you are experiencing are not a function of us working with different versions of the software.

> As you suggested we have a second beta for you to review:

> <DROPBOX LINK>

> Apologies for not following up earlier but it's been a challenging period with staff illness. 

> To take each of your Items in turn.

> General

> 1. When you say "Save & Refresh All" you mean that every currently open form should also refresh?
> 2. Added
> 3. If we can get this to work reliably in access, certainly

> Members Table
> 1. Fixed

> Members Form
> 1. Defaults set
> 2. Aligned
> 3. The member number does get automatically filled in for me, but only once a new record is saved, similarly for LastUpdated
> 4. We're not experiencing this (even on Office 365) - perhaps we can get screenshots?
> 5. Fixed, but note that you may need to navigate away and back (or refresh all) to see the LastUpdate date change
> 6. Fixed
> 7. Fixed
> 8. Done

> Gift Aid Form

> 1. We can't replicate this at present, even on Office 365
> 2. This functionality works for us, including in Office 365 - do you have macro's enabled? see https://support.office.com/en-us/article/Enable-or-disable-macros-in-Office-files-12b036fd-d140-4e74-b45e-16fed1a7e5c6
> 3. We've adjusted this, but there is an issue here when there are multiple line-items - our global check sometimes sticks on the first one.  We've turned off the global check for the time being, and the status is checked whenever a "To Date" is adjusted.  It should be more stable now, but see how you find it.
> 4. Fixed

> Subscription Form

> 1. We're having some challenges in access about ensuring that when you load a form that you will get complete data consistency, but it's straightforward to ensure on the basis of a user change to the form - see 2.
> 2. Fixed
> 3. Can add, but query, this should happen irrespective of where status is set?  Since status appears on all four forms.
Address Labels

> 1. We can't see the Dymo 11354 labels as an option for access.  They appear to be 57mm by 32mm.  We can adjust - do you know if they are equivalent to any other address labels
> 2. Fixed
> 3. Fixed
> 4. Fixed
> 5. Removed
> 6. Understood
> 7. Working on this - print preview not blank for us ...

> Individual Receipts
> 1. Still working on this - maybe form might be easier
> 2. Working on that
> 3. Strange; "Print Preview" works for us ... although we do get a popup warning us about page width ... here's what it looks like for us:

![](https://dl.dropbox.com/s/udg3x1bhtd6mcj8/Screenshot%202018-02-09%2020.54.27.png?dl=0)

> Membership Types Table

> 1. Clarification needed here.  Membership types currently has these contents:

![](https://dl.dropbox.com/s/id773d79px1iyh9/Screenshot%202018-02-09%2020.56.04.png?dl=0)

> While Subscription Rate Paid Table has these:

![](https://dl.dropbox.com/s/6gyxjmkoog1ebhj/Screenshot%202018-02-09%2020.56.52.png?dl=0)

> i.e. the latter makes the distinction between Student O/S and Student, while the former does not.  Should we be consolidating on one or the other?

> Apologies again that we're not completely finished, and hopefully you'll be satisified with the incremental progress.

> We look forward to your further feedback and completing the project ASAP.

> Best, Sam
