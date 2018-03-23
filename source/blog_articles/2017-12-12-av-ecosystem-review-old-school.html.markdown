I'm in the second week of a cold - I think it's getting better, but I suspect that not resting properly last week is causing it to drag out for a second.  Totally distracted by Slack and Email this AM, but there we go. At least I have my PC set up with LibreOffice so that I can develop on a similar machine to that of the client.  What I also notice is that the project greeter bots seem to have stopped.   Would like to be working on them, but have to prioritise client work (I think).  Maybe I can just push an empty commit to see if they start up ... so I merge the outstanding pull request:

[https://github.com/AgileVentures/project_greeter_bot/pull/26](https://github.com/AgileVentures/project_greeter_bot/pull/26), 

and push that out to production.  And then I get sucked in, shaving several yaks, I update the installation docs for project greeter bot and asyncvoter so that I can link them into greeting bot messages for those channels, and then I push that out from a branch.  Okay, totally distracted; might as well go on twitter and tweet the podcasts I just listened to.  Okay procrastination explosion; now I need to get to the list of follow up items I had from yesterday's client work ... the outstanding issues were that:

* all dates should have 4 digit years
* explain the significance of white vs grey boxes
* first line of address in top box
* country drop down should be full list of countries
* adjust DB schema?
* add second email address to schema
* “Last Update”, “Date Joined” and “Member#” should be automatically generated and not keyable – these should have a grey background; other fields a white background. All other fields are potentially changeable although some will default

I also notice that the mobile phone text is the wrong font ...

So I'm tempted to make these changes on the PC and see how they transfer back to the mac, where I fix up the mobile phone text field.  Now the 4 digit year thing is interesting in that the data that we've imported has two digit years, although that might be an artifact of the import process.  I'm not sure if we have the date fields in the right format ... ah, it appears we do, so then there's a question about how they are presented in the form.  There's a section on date formats in the docs, but as with many things in LibreOffice it's not clear if it's specific to the base component:

[https://help.libreoffice.org/Common/Number_Format_Codes#Date_Formats](https://help.libreoffice.org/Common/Number_Format_Codes#Date_Formats)

Not very googleable, this issue, but when I edit the database table itself, I see an option to set a default format, so that sorts that.  The other non-trivial one is the list of countries for a dropdown menu, so I ask a question about that on the libreoffice forum:

[https://ask.libreoffice.org/en/question/140501/base-dropdown-country-list/](https://ask.libreoffice.org/en/question/140501/base-dropdown-country-list/)

but I get side-tracked into that when what I should probably be doing is mocking up the other interface screens ... and now I've got the country thing working, but I've somehow blown away a load of country data.  Frustrating; I'm getting distracted by additional work requested when I should be focused on the core ... at least I will force myself to copy the existing form and make it into the "Subscription Payment" form.  However now the software has locked up on the PC ... so I killed it and now I have it giving me a copy of the form which I'm working up to be the Subscription Payments form.  I've added a subform and I can see that migrating to new membership IDs is going to be non-trivial, given they are the primary keys for relating all these tables.  And where I'm sort of stuck is displaying a sort of spreadsheet view of all the subscription payments in the new form.  I can create tables, but I'm not sure that's the right thing, but aha, I have found the "table control" form element that I think we need, and that looks about right.  Phew, so looks like we can probably get all the functionality we need, although the migration feels like it will be painful ...

![](https://dl.dropbox.com/s/dm3liajv1l0lflh/Screenshot%202017-12-12%2011.40.52.png?dl=0)



