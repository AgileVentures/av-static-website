Ironically after getting more sleep last night I feel groggier this morning.  Sunday night I couldn't sleep re-hashing my 9-year old twins lost football (soccer) match (I'm the coach).  Don't want to be strung out thinking about it, but I am every time they lose.  Despite the lack of sleep yesterday was a solid day.  I slept properly last night, but is this a cold coming on? Urgh.  Been distracted this morning, but maybe by the right stuff?  I've done a quick Slack/Email review and edited the [AV Premium F2F offer page](https://www.agileventures.org/premium-f2f-offer) to include connecting to me on LinkedIn.  I keep wondering what to do with the random folks connecting to me there - might as well be offering them a free hour of pairing on a charity project.

And now I want to just quickly follow through with Will's advice on changes to the Premium Sponsorship form, and get the revised version out to a few folks:

> Okay. This is only a side note, but I read this interesting article about names: http://www.kalzumeus.com/2010/06/17/falsehoods-programmers-believe-about-names/ TLDR: have a single "name" field

> Each question should have a reason. I want to know why you need to know what country I live in, for a premium membership.

> The resume could be a lot of work. Shouldn't the questions ask everything you want to know?

> "If you're working remotely, where is the company based? *" shouldn't have an asterisk.

> Edited-down mob programming videos might get more folks applying.

> "Please upload a cover letter indicating how you think you would benefit from the Premium membership you are applying for" could become just "How do you think you would benefit from the Premium membership you are applying for?"

So now it's much shorter:

![](https://dl.dropbox.com/s/m29aa7wl36fiuw5/Screenshot%202018-01-09%2010.32.39.png?dl=0)

but now I must jump back to the APSoc membership database, and my new backup ritual which involves copying the previous days file in dropbox and relabelling it with the day I'm starting on.  Giving the system 2GB of memory does not appear to have helped with the lock up issues.  

But let's start with correctly populating the Local Group field in the membership form.  I create and populate a new table, and in short order I have the Local Group dropdown as requested:

![](https://dl.dropbox.com/s/w93hp5cyr700a9i/Screenshot%202018-01-09%2010.45.43.png?dl=0)

Now I duplicate the version and restart LibreOffice.  And then I work on a new dropdown for pay method on the subscription payment form.  I create the table, but then trying to set up the relation we get stuck again.  I go make coffee, come back, still stuck.  I kill the app, and re-start.  I'm lucky - this time we're able to recover the corrupted file:

![](https://dl.dropbox.com/s/cms13nfe5rtu8nd/Screenshot%202018-01-09%2011.09.14.png?dl=0)

![](https://dl.dropbox.com/s/ac9vdcxbhwlfpkk/Screenshot%202018-01-09%2011.10.30.png?dl=0)

but I've got no basis to diagnose the issue ... I make a quick extra backup and continue, and I get it working:

![](https://dl.dropbox.com/s/anclrh7bi983wpb/Screenshot%202018-01-09%2011.13.11.png?dl=0)

There is no particular pattern - although sense that when I double up on things, e.g. add a table in the SQL view by double clicking, but then also click the add button ... okay, enough for today, I'm going to pass this version on to the client as I said I would do daily this week.









