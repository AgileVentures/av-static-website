Pat was commenting that my recent blogs don't have so many technical deep dives and are more admin focused.  That's probably a reflection that I'm doing less coding and more admin recently.  I've spent a couple of hours over the last few days trying to hook up the Google NonProfits AdWords grant, which will apparently give us up to $10,000 a month in AdWords funding.  An exciting prospect, but it's taken me several admin hours to file the application on the right email address - go round the houses with Google online help suppport etc.  This week I'm also trying to get ready for a presentation at the DevOps conference, which sucks up time, but at least it's enabled me to get the NewRelic monitoring setup on WebSiteOne correctly, and identify the source of strange health check activity on the LocalSupport servers.  The latter seems to be an artifact of using legacy NewRelic monitoring tools - time to upgrade!

I did however manage to snatch a chunk of time to do WebSiteOne devops.  The bug fix to prevent un-recorded YouTube links being tweeted was stuck on staging since we had stubbed the mock incorrectly.  While we waited for that fix some other PRs had piled up and I'd merged in our refactoring the Premium plans to support easier creation of new plans.  I rebased all the latest code from develop into staging, which finally worked smoothly since I'd previously merged the production fixes from last year back down to develop.  All the new functionality worked as expected on staging with me running the set of rake tasks that went along with the various new bits of code:

```
$ heroku run rake db:create_plans -r production
$ heroku run rake db:create_plans -r production
$ heroku run rake fetch_github_commits -r production
```

I still had to manually test the hangouts/twitter interaction.  It occurs to me that we might be better off with things that are being merged into develop following a faster cycle into staging and production, rather than allowing them to bunch up.  I guess the difficulty is the complexity of setting up individual developers to be able to manually test all the 3rd party hookups.  Perhaps with automated deploy from drie that will change.

What I did immediately notice was that the language in the tweets coming from the staging server were odd:

![odd language in tweets](https://www.dropbox.com/s/6s33zipjbpj1e8o/Screenshot%202017-01-17%2015.34.10.png?dl=1)

As a result of the latest changes we now only tweeted a link to the YouTube once the host had clicked the "start broadcast" button in the hangout and the YouTube video was reporting a non-nil `actual_start_time` via the API.  However the langauge in the tweet said that the session had finished, when clearly it was potentially still ongoing.  I groaned as I thought about needing to make another change before we could push this out, but talking it over with Michael in the scrum I realised that this was an existing bug. 

The original setup (as had been running for the last year or so) is that as soon as the server receives a notification from the Hangout plugin that the hangout has successfully started, the server automatically tweets both a link to the hangout (with text saying the session has started) and a link to the YouTube video saying the session has finished.  The original bug-report from Thomas was that some of the YouTube links were duds.  We're pretty sure that this is due to those situations where no one starts the broadcast/recording, or there's a technical fail on YouTube.  So we have fixed that bug, but in the process uncovered other inconsistencies in the user experience.

I guess it's not such a big deal, because for the lifetime to the tweets the wording will be correct as soon as the recordings do finish.  Not that anyone is necessarily paying attention to our Twitter feed (although I do notice more and more followers stacking up recently) but anyone paying close attention would think that all scrums and pairing sessions shut down pretty soon after they start.  At least with our latest fix, anyone reviewing the tweets a few hours after the fact will encounter fewer dead YouTube links so that's probably a good thing, but we've got no evidence that our Twitter feed is a source of any value for our organisation.

Working on this bug has been an interesting learning experience.  It's included myself, Sasha, Matt, and Michael and we've covered all sorts of interesting topics related to BDD, 3rd party APIs, mocking, caching, testing etc.  Particularly since we may lose the entire Hangout plugin in April it does make me wonder if we've just been painting a tiny portion of the system that's unlikely to have any significant impact on our sustainability.  My mind leans towards adjusting the site to have a stronger call to action on the front page, e.g. to sign up as a member and then ultimately upgrade to Premium, as being something that might have a chance of strongly impacting our sustainability.

When Matt started looking at a new feature the other day I suggested that maybe we should look at some bug-fixes first.  Together we managed to get to the bottom of the slack invite bug, which strangely seemed to be just a missing setting on production, which in principle saves me a few minutes each week spent inviting new users into Slack.  The priority now must be to get these latest changes out onto production.  There's one other minor fix that could be done first, but I've created a ticket for it.  We need to experience any pain from the collection of new changes rather than holding off I think.

Maybe it's smoothing the upgrade process to the different levels of Premium that's really going to make the difference to sustainability?  Or the next admin task that will unlock some more funding.  I guess I just keep swapping between coding and admin-ing in turn until it all becomes sustainable.

###Related Videos

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=V2KfoeCcp-o)
* [WSO DevOps](https://www.youtube.com/watch?v=Yc4c0QL8Efg)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=h3b5Og-umnU)

p.s. this blog took 20 minutes to write


