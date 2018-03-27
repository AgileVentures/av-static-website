---
title: AV EcoSystem Review Client Project Focus
author: Sam Joseph
---

![focus](../images/focus.jpeg)

So despite pushing out fresh code all the dokku/azure bots still appear to be offline.  Would love to fix that, but yet I feel I should prioritize the client work.  I guess I can restart the dokku containers.   Has something in azure changed, blocking a new port?  Has rolling out metplus into dokku had some knock on impact?  Ah, I'm wrong; it looks like the main greeting bot did work overnight.  So I go back and post the welcome message manually to the few new slack folk.  Not sure about the greeter bots, but I've posted all the active project links in `#new\_members` channel, so maybe we'll see a few folks join channels and then we can tell if the project greeter bot is working again.

Yesterday I found the little widget that allows me to embed a spreadsheet view of another table in a LibreOffice Base form.  Now I need to be able to embed list boxes in it (which is relatively easy) and maybe have them point to other tables, which perhaps needs a sub-form in the table widget, hmm.

In the background someone has answered my [question about countries in list boxes on ask libreoffice](https://ask.libreoffice.org/en/question/140501/base-dropdown-country-list/) pointing to a countries table that comes with MySQL. Not sure if LibreOffice has MySQL installed, or that's just a suggestion about where to get the data from, but they also point out that the SQL query needs to return the name of the country twice, so I try that, but the program locks up on me.  I think there's something about list boxes in LibreOffice that I haven't got quite down yet.  In the table widget I've got listboxes that are giving drop downs from the table data, but aren't showing the relevant field itself ...

It's a funny thing here - should I be trying to read the documentation about how to get it to work - I keep falling asleep whenever I try to read almost any documentation.  I generally make more progress just bashing on the interface and posting to help forums.  But perhaps I should just be getting mockups of the last two forms and sending them to the client, for what I will assume is reams of feedback that I could easily get distracted by.

I force quit LibreOffice on the PC and re-start and adjust the query to return "Name" twice, but that leads to no particular change to the countries list box.  I guess I'll need to re-import the data, since the countries column got wiped out last time around.  There's also the issue that the country names in the existing data don't necessarily match the spelling/abbreviations in the list that I added.  Need to leave that for now; does the double name return help in the subscriptions payment form? ... it does, but needed to be combined with intelligence from another answer on [ask libre office](https://ask.libreoffice.org/en/question/65146/a-dropdown-list-inside-a-control-grid-table-with-sql-consult-in-base/?answer=65195#post-id-65195).

Okay, so maybe now I can bash out the other two forms ...?  Urgh, but it's locking up on the PC again ...
