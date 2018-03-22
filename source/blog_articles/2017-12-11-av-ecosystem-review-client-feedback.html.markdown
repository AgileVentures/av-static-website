---
title: AV EcoSystem Review Client Feedback
author: Sam Joseph
---

![client feedback](../images/client_feedback.png)

I got out a screenshot of the main APSoc LibreOffice Base Member Information form to the client late Friday, and over the weekend they've given me very detailed feedback on changes they want to the form.  Agile is all about providing working software as early as possible, but this seems like a nice shortcut to increase the chances that the resulting system will be what the client wants.  I've ordered a new HDMI cable so I can start developing on my windows machine (although will that just be a distraction?), and so I'm going to have a go at making all the changes requested on my mac.

1. Adjust label font (Liberation Sans --> Tahoma)
2. Have membership number be numeric only
3. Align last updated field with Member Information title
4. Align all field names with left edge of inputs
5. Reduce size of date fields
6. All dates should have 4 digit years  <--- not sure
7. Reduce size of "Known as" and "Salutation"
8. Wants to know signficance of white vs grey boxes <-- can explain
9. First line of address in top box <-- data issue - some addresses in odd places
10. Move "Local Group" up to address along with "N/L"
11. Move up "NM N/L" also
12. Remove KCM only text
13. Move "City ... Country" row up
14. Align Country field
15. Country drop down should be full list of countries <-- will need to investigate
16. Fix Mobile Phone font
17. Remove Fax field (and data from DB) <-- we don't have final DB - am I supposed to provide a new schema?
18. Remove "Dietary Requirements"
19. Replace with "Email Address 2" <-- need to add this to DB
20. Remove “Gift Membership ...” from screen and DB (again the info can be moved to “Notes” by me after migration).
21. Expand “Notes” top full width of screen.
22. Align check boxes
23. “Last Update”, “Date Joined” and “Member#” should be automatically generated and not keyable – these should have a grey background; other fields a white background. All other fields are potentially changeable although some will default.
24. "Work Phone" and "Mobile Phone" fields should align with the fields in the row above.

So I've done most of the cosmetic changes and some of the data ones:

![](https://dl.dropbox.com/s/oz1u6wksr1c40mw/Screenshot%202017-12-11%2010.25.27.png?dl=0)

In about 30 minutes, but that leaves me with a set of things I need to check ... over the course of the day I also got LibreOffice installed on my Windows machine where it seems to run a little faster, and I can confirm that it looks sensible on Windows; and seems to have all the right basic functionality.

The feedback from the client is very specific.  Some of the items are just tiny ones that I would have fixed in a subsequent pass.  Others are micro-changes for alignment and so on that I would have got to eventually.  It feels like many of these changes are ones that they'll be able to make themselves when they are all set up with the software.  Others are more substantive.  Great to keep them in the loop.  For some reason I bristled at the font change suggestions; I think partly because I was having such trouble setting a default font, but as I familiarise myself further with the software I was able to make the requisite changes in short order.  It is great when one is able to show clients/users that you respond to their requests.  I think it builds a really positive relationship, but of course one needs to be able to say no when the amount of work explodes.

I'm optimisitic I can get the majority of the work done over the course of the next week without having to break stride, but we'll see.  The interesting question is whether we want to start paying for Chatlio since this work was generated through that system, or if there'll be any kind of similar work generated if we complete well on this.  I've got other people asking for quotes on website refreshes, so that's probably the place to start before re-opening the web-chat deluge ...
