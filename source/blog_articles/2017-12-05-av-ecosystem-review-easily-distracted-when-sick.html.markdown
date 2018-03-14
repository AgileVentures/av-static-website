---
title: AV EcoSystem Review Easily Distracted When Sick
author: Sam Joseph
---

![sneeze](../images/sneeze.png)

So I didn't get far with the Anthony Powell work yesterday, but I did at least get all the software running and get my head back into the problem space.  That was enough to take the edge off my anxiety about getting it started early.  I'm also onboarding a few new developers to WSO which ameliorates my worries about bleeds there.  This morning I got totally distracted by email and Slack, but in the procrastination wheel picture of things I did get a few things done.  Now let's focus.  I'm tempted to see if I can bash the auto-generated form in LibreOffice Base to look like the exising Lotus Approach one.  The voice in my head worries that I should be writing out user stories.

1. Can enter new member information
2. Can browse member information in same form
3. ...

The database dump we have from Lotus Approach appears to have five tables:

1. Members
2. Subs
3. Gift Aid
4. Recurring Subs (transactional)
5. Member types

and we have a dump of each with the addresses randomized.

LibreOffice Base (like Lotus Approach and MS Access) provides a form interface where we can page through the rows in the database, and add new information.  I guess the key things to do are the make the forms look usable and ensure that the different tables have the right relationships set up.

I've also already imported three of the tables into the system.  I can't remember how I did first time around.  I've previously referred to:

[https://help.libreoffice.org/Common/Importing_and_Exporting_Data_in_Base](https://help.libreoffice.org/Common/Importing_and_Exporting_Data_in_Base)

which basically involves opening the existing dbf files and copying and pasting all the existing rows into the tables section of a LOB database, and then using the import wizard.  I had a go at trying to match up the datatypes, which reminds me that I'll probably need to start this over from scratch to get all the datatypes working properly ... as well as ensuring that I'm updated to the very latest LibreOffice version.   Hmm, I wonder if a Trello board or Pivotal Tracker is in order to track all the tasks?  I'm leaning towards Pivotal since being able to accept/reject by default will be a good match for this project.

I've got the form design system working so that I start manipulating the auto-generated form to look more like the existing Lotus Approach form:

![](https://dl.dropbox.com/s/5grezldftymxi4z/Screenshot%202017-12-05%2010.52.43.png?dl=0)

but now a key feature is being able to have a form field that refers to another table.  There are some videos online:

[http://thefrugalcomputerguy.com/libreoffice-base/Base17.php](http://thefrugalcomputerguy.com/libreoffice-base/Base17.php)

but I browse around and read a few help forums, and then I've pretty much worked out how to put sub-forms into a form that link to a different table and slave certain fields to the record being shown in the main form.  It's not working at the moment, but I guess that might be due to different field data types:

![](https://dl.dropbox.com/s/xi07mk1ckua2tgq/Screenshot%202017-12-05%2011.17.34.png?dl=0)

So I think the sensible thing to do is to re-import the data.  What I should also do is give our clients a sensible timeline, such as showing them mockups of the new interfaces by the end of the week, and a first version to play with by the end of the following week ... hopefully that should put a lid on both my anxiety and theirs :-)
