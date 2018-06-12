Time for another private project Tuesday. I'm half tempted to get a few admin tasks first, but maybe I've leave those for my breaks and later in the day when the dust has settled.  I think I need to blog on the higher level tasks and goals of the private project in order to get the most out of the day.  The lead developer has set up an ASAP milestone with the high priority work.  That makes it quite clear where the focus needs to be ...

I think the best approach is to dive into the remaining features and at least spike something working.  The lead developer has dropped another 10 or so commits into the project branch, which I review in github and then pull in locally.  The unit tests fail, but that's probably due to an update of the associated NLP project which the lead developer had flagged.  It is.  I move on to the acceptance tests, where the three that are stable on OSX all pass, so we are ready to dig in to what else we need on the "project" feature.  My existing projects look a little odd, but that might just be the old naming conventions.  What is it that needs to get done here ...?

Reviewing my notes I see that I did have most of the main functionality done here, but I was worried about bugs arising from creating projects with clipboard contents, and thinking about whether we could get acceptance tests in place.  It was going off to the acceptance tests that lead to the snarl up where all the node_modules stopped working.  Do I dare add some sort of acceptance test for the functionality we already have (and encounter another snarl up - since acceptance testing on my mac does not seem stable by a long shot) or focus on first finishing the requested functionality for menu and commandline changes?

I guess I need to start with at least some clarity on what is actually working.  I do have the cucumber outline and maybe the sweet spot is to use it as a manual testing guide until such time as we have good acceptance test stability on OSX.  I'm going to start by deleting all the exising projects so I can check the experience of creating a new project from a file.  First I have to remember where the project files are stored.  Okay, ticket to see if that info could be exposed in the interface - find them, move them to `/tmp` and the interface opens clean.  I run through a few manual testing scenarios - everything appears normal.  Time to move on to command line and menu updates I guess.



Notes

* re-opening project does not re-open partial translation?
* long project names can be a problem (will we see them often?)
