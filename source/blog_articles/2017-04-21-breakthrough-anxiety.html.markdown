---
title: Breakthough Anxiety
date: 2017-04-21
tags: planning, WYSIWYG, autograders, wiki, PBwiki, Xwiki, bluespice, icons, usability, internet explorer, Glyphicons, font-awesome, MCE Editor, mechanise, cookies
author: Sam Joseph
---

![breakthrough](/images/breakthrough.jpg)

I just burnt 15 minutes taking out the recycling, replenishing the water bottles on my desk and discussing with my wife about Japanese textbooks for my kids.  Well not burnt :-) Time spent - all needed to be done.  If I'd been slightly more organised I'd have gotten my eldest son to take out the recycling this morning.  The strange thing about being a parent is that getting time when you're not being pestered or interrupted is quite hard.  All my boys are at school now - I get a certain amount of time in the day when I can focus on my computer in my home office where I have two external screens and I can work "efficiently".  The "in the momment" philosophy suggests to me that it's a little silly to consider time as burnt or lost; at the same time I do want to bring in money to feed my kids, and that seems to require focused time getting work done.  That said and despite my best efforts at prioritising, I'm still not sure if I'm really focusing properly on what will bring home the bacon.

Having used up a lot of my savings to work on AgileVentures full time for the last year I am definitely in a bit of an anxious state.  I did achieve a couple of breakthroughs yesterday that helped lighten my mood.  One was to find a WYSIWYG editor that worked on the NHS laptops.  The other was to resurrect the autograders for the AV102 TA training course.  Both are good stories I'd like to blog out.  I also note that I'm well behind on reviewing LocalSupport pull requests and many other things.  I'm torn about whether to use the blogging time to force a focus on catching up with AgileVentures Premium related work (e.g. pull requests) and reporting on the breakthroughs.  Let's focus on the breakthroughs for the purpose of self-documentation and I'll get to the pull requests in the next chunk of my day and blog about them Monday.

So, I was at the NHS head office again yesterday for a meeting with a member of the communications team, which was a critical meeting and very positive.  The communications person was enthusiastic about the proposed Social Prescribing wiki, and naturally concerned that many of the stakeholders might find it difficult to contribute.  More on the details of that meeting soon, but just after the meeting I was able to sit down with an NHS laptop (with IE 11 on the NHS intranet) and review the problematic interface issues we'd seen in the usability sessions a fortnight before.  For the usability testing we'd selected three different wikis and tested them on NHS staff computers.  The wikis had worked, but the key problem across all three instances (PBwiki, XWiki, BlueSpice) was that certain key icons just didn't show up:

![XWiki icons not showing up](https://dl.dropbox.com/s/il7lzum1kbx6ev3/Screenshot%202017-04-21%2009.40.08.png?dl=1)

In the above image you can see how the login panel for XWiki looks on my Mac, and on the left you can see how it looks in IE11 on the NHS laptops.  The icons are missing.  Now that's not the end of the world for this login panel, but it's actually catastrophic in terms of usability for the rest of the XWiki, and not just XWiki, but also PBWorks and BlueSpice.   The XWiki "hamburger" drawer icon that serves to indicate where to open the login panel is not visible on the NHS laptops, so you can't login without someone standing next to you telling you where to click.  On PBworks and BlueSpice the icons are used to indicate the place to click to start editing, or to use various text font and styling buttons.  Here's an example from BlueSpice, which for some reason had also switched to Russian:

![BlueSpice text editing toolbar on the NHS laptop](https://dl.dropbox.com/s/dpc9vqhyj0yzgl6/Screenshot%202017-04-21%2009.45.05.png?dl=1)

Interestingly all these icons loaded correctly on a virtualized IE 11 on my laptop running over the NHS guest wifi.  The failure of the icons to show up was only happening on the NHS laptops hard linked to their internal network.  What I really should have done was tethered one of their laptops to my wifi hotspot on my phone to further isolate the problem, but I was out of time, and the suggestions from XWiki support, such as clearing the browser cache, were not possible on the sys-admin restricted NHS laptops.  I took lots of screenshots, dumped network interactions and noted that the failures for XWiki were for the Glyphicons, for PBwiki they were font-awesome icons, and for BlueSpice they were MCE Editor icons.  It was a dark moment - in my desperation I tried loading Wikipedia on the NHS laptop, and the icons in their basic editor showed up!

![wikipedia basic editor working](https://dl.dropbox.com/s/2kwtz5ebey0jmug/Screenshot%202017-04-21%2009.49.52.png?dl=1)

I investigated the source and found that they were using background images rather than icons.  I'd initially ruled out a pure MediaWiki offering, with the special mediawiki syntax as being too confusing for the majority of stakeholders, but I noticed that Wikipedia offered a visual editor solution that also worked on the NHS laptop:

![wikipedia visual editor](https://dl.dropbox.com/s/5ltrfl1c0ha10cy/Screenshot%202017-04-21%2009.52.42.png?dl=1)

Did we have a winner?  At least I had an angle of attack.  If MediaWiki actually worked out of the box on NHS laptops then it was streets ahead of any other solution we'd tested so far, regardless of any other shortcomings.  Still lots to work out, but light at the end of the tunnel, perhaps?

So that was one breakthrough.  The trip into London for the NHS meeting and then the laptop research, getting home, investigating options for MediaWiki hosting left me having spent no time on my other major blocker which was that the legacy autograders for the AV102 TA training course weren't working.  I'd logged into the grader earlier in the week and got the following message:

```
The queue name is BerkeleyX-cs169x
/home/ubuntu/rag/lib/edx_controller.rb:74:in `get_queue_length': Bad queue length response:  (EdXController::BadStatusCodeError)
        from /home/ubuntu/rag/lib/edx_client.rb:242:in `each_submission'
        from /home/ubuntu/rag/lib/edx_client.rb:49:in `run'
        from ./run_edx_client.rb:9:in `<main>'
```

Over and over.  The AV102 grader hadn't been updated to the new version that UCBerkeley students created the summer before last.  I'd fixed the AV102 grader to keep running, about the same time last year, by hacking away at the authentication code, and I wasn't optimistic that I could fix it again.  The "sensible" path would be taking all the AV102 assignments and converting their specifications to work with the new grader and then pointing the AV102 course on edX to a new grader instance.  The problem with that path is that it felt like it would be multiple hours work, with no guarantee of success.  In principle the new grader system is supposed to allow us to insert new assignments more easily - the assignment specification can be located in GitHub and be sent to the grader via the edX course itself.  I'm 90% sure that I could get it all working, but it feels like it would take a few hours, with bumps and starts, and I didn't have a few hours.  Or at least didn't feel like I could make space for them.

I prodded at the failing grader code for five minutes and saw that the problem on the legacy grader was again an authentication issue.  The initial authentication step was not working, causing the request to the edX queues to be redirected to login.  This was exactly the problem I'd fixed a couple of times before in under an hour of debugging, and the exciting thing in both those cases was that all the existing infrastructure would then start working.  No need to convert multiple assignments to a different format etc.  However the day was at an end, and my physio had now cleared me to join in my sons' Karate classes.  What to do?  I took my laptop to Karate and sat out.  Fortuitously, just as the Karate class was finishing and I had to take my sons home, I'd done it again.  Analysing how mechanize (which the TAs had used in the new grader) was successfully authenticating on the new grader develop server, I saw that again we had a problem in the legacy code as to how the "Set-Cookie" operation was being interpreted.  Here's what the edX queues was sending back in response to authentication requests:

```
"set-cookie"=>"csrftoken=kFWOZK6FnT....eLAvMDsyi; expires=Thu, 19-Apr-2018 17:15:44 GMT; Max-Age=31449600; Path=/, sessionid=y4i9hip...j3x1i; expires=Thu, 04-May-2017 17:15:44 GMT; httponly; Max-Age=1209600; Path=/, AWSELB=E7E741EF08F209.....E1C1FA69F3B681EB87F2DFC9E7C01B23665183B022A6A8E876;PATH=/", "vary"=>"Accept-Encoding, Cookie", "content-length"=>"61", "connection"=>"keep-alive"}
```

With debugging hooks going down through a couple of gems, I determined that the working mechanize based grader was only setting the csrftoken, sessionid and AWSELB elements of the set cookie request.   I got the same effect on the legacy code with a really messy hack:

```
@session_cookie = all_cookies.scan(/[\d\w]+\=[\d\w]+/).select{|str| str.start_with?('AWSELB') ||str.start_with?('csrftoken') || str.start_with?('sessionid')}.join('; ')
```

but it worked - suddenly the AV102 homeworks were grading successfully.  Such a mess, and it could have been concealing many other bugs behind the authentication failure, but I gambled and won on this occasion.  I'm not getting paid anything to work on this code or run the AV102 training course.  It would be hugely beneficial for the long term of the autograder project if I was regularly inserting new assignments into the new system, documenting the process and smoothing off the rough edges, but again, no one is paying for that.  Or at least, no one is paying me for that.  I do get a share of the proceeds of the sales of the relaeted textbook (since I'm the editor) and part of my responsibility is to keep the graders running on the main course and the SPOC, but the TA training course is my own separate initiative.  Ironically this messy hack in 45 minutes in my free time while my kids do Karate will keep the AV102 TA training course running for a little longer.  I haven't even had time to take the debugger statements out of the develop grader (although a git checkout should sort that).

Two breakthroughs, but I'm still anxious.  Is this hack to keep AV102 running a wonderful piece of pragmatism, or is it the technical debt that breaks the camel's back?  Can I find a good MediaWiki hosting provider or will we have to host ourselves?  Lots of deep breaths, nose to the grindstone, enjoy the moment! :-)

### Related Videos

* ["Kent Beck" Scrum](https://www.youtube.com/edit?o=U&video_id=OFKKyZbJqlk)
