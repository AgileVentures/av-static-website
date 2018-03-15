---
title: AV EcoSystem Review Crawling Along
author: Sam Joseph
---

![bug](../images/bug.jpg)

Well I think I've forged a reasonable anxiety management strategy with the following schedule for the Anthony Powell Membership database:

* by Dec 8th - screenshots of membership database interface for you to approve
* by Dec 15th - alpha version of membership database for you to approve
* by Jan 5th - beta version of membership database adjusted as per feedback
* by Jan 31st - release version of membership database adjusted as per feedback, including full migration 

which the client says they are happy with, and I've queried about how the Membership Types table doesn't seem to be used in the interface.  After yesterday's blog I was fiddling with the system on the train and once I have the fields in the right data format, subforms linking in related tables was all working.  Ironically it seems we don't immediately need that, but good to know that all works.  The form designer is not dissimilar from Keynote or PowerPoint:

![](https://dl.dropbox.com/s/eqeny8wkgv225x0/Screenshot%202017-12-06%2011.53.53.png?dl=0)

I'm slowly pulling the fields around, trying to work out how to set the default font.  One thing I need to do is to be able to switch Member Grade to a drop down with different options ... I've got that working using the list box wizard, but as soon as I try to add any formatting to the list box the drop down functionality stops working:

![](https://dl.dropbox.com/s/tlrx6xodqs4fw1p/Screenshot%202017-12-06%2013.46.39.png?dl=0)

So I've asked on a forum:

[https://ask.libreoffice.org/en/question/140023/base-list-box-dropdown-breaks-when-any-formatting-added/](https://ask.libreoffice.org/en/question/140023/base-list-box-dropdown-breaks-when-any-formatting-added/)

but in the short term, unformatted should be fine.  Better work through the rest of the interfaces quick to see if I find any other sticking points ...
