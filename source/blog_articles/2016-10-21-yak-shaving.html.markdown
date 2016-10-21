---
title: Yak Shaving
date: 2016-10-21
tags: devops heroku pull-requests automation database permissions accounts admin
author: Sam Joseph
---


So I thought I'd try and get a little solo programming time in today, but it turned into a yak-shaving devops session.  Yak shaving?  It's when you end up needing a ladder to paint your house, and you have to borrow a ladder from your friend, but you need to go get his keys from his neighbour who has to finish shaving his yak ... I know, it makes no sense - the point is about how you have one goal and then you get successively distracted by a series of activities that become more and more tangentially related to the original goal.  I was trying to get out premium plus upgrade functionality into production.  This was going to involve doing a data migration.  I wanted to get an instance of the branch with that code up in heroku to test the migration.

Heroku's automatic deploy of PRs to heroku instances is nice when it works.  It seems flaky.  I started poking at trying to get it working.  First up it was saying that there wasn't permission to deploy the app.  I'd just moved websiteone-develop from my personal account to my company NeuroGrid Ltd group account.  Raoul had originally set up the automated deploys on the develop server so somehow the attempt to deploy the PR was coming from his account.  I played with moving him from a collaborator to an admin role, which moved things forward a little.  The next error was the deploy was failing due to a [database error](https://github.com/AgileVentures/WebsiteOne/issues/1348).  One of the frustrating things about this was the delayed feedback loop.  I'd make a change in the `app.json` file that specifies how heroku will auto deploy PRs, or a change in Heroku's admin console, and it takes five minutes or so to find out if the deploy has worked or not.  I was task switching between trying to fix the automated PR deploy and cleaning up what looked like a puffing billy sandbox leak on the branch itself. Ultimately though it was the PR deploy that was taking up more and more of my brain.

Was this another banana?  Really if I wanted to get the premium upgrade feature out I should focus on pushing that to some new Heroku instance and leave fixing automated PR deploys to another time.  Of course automated PR deploys are great when they work, and they help project managers (including me) review PRs faster, and if it worked I wouldn't have to do all the fiddly deploy to get the ENV vars set up for a new heroku instance to test if the new feature.  The great thing about heroku pr deploys (when they work) is that they copy all the environment vars from a specified server (in this case develop) and things are good to go.  However, running `rake db:seed` to pre-populate the database as part of the `app.json` post-deploy script was giving this error message:

```
FATAL:  permission denied for database "postgres"
DETAIL:  User does not have CONNECT privilege.
```

but ironically, immediately after that we had all the output from what looked like a successful run of db:create and de:seed.  Confusing.  We'd had some similar [issue on LocalSupport previously](https://www.pivotaltracker.com/story/show/116276111), which had ultimately been fixed by ... I'm not sure.  LocalSupport automated heroku pr deploys had been working for a while.  They were now stuck on a separate issue of us having too many apps, even though they were set to auto-delete after 5 days of inactivity.  Although now I type it out, I think the problem might be that the LocalSupport develop server is on my personal account, and I have a load of junk apps there.  Basically the last few months appears to have seen Heroku gradually introducing new limits which we are falling foul of in new and unpleasant ways.

Anyway, the LocalSupport ticket (and an old Heroku ticket) included some ideas for changing app.json, e.g. including an explicit migrate step, and this from the Heroku folks:

> There is a caveat when building review apps that we have requested a new database addon, but it's not guaranteed to be provisioned during the build phase. It sounds like something in your application is trying to access the database before your database is up and ready to receive connections.

> The ideal fix is to track down why the app is connecting to the database during build and try to prevent that. If that's not an option, we also have a buildpack that you can use to wait for your database to come up: https://github.com/heroku/heroku-buildpack-addon-wait.

I didn't get to trying the build pack suggestion, instead going through a series of changes to app.json; an analysis of the stack trace associated with the above database fail, which seemed to possibly implicate [airbrake](https://github.com/airbrake/airbrake/issues/620) but probably not.  Ultimately I did not get it working, but did succeed in spamming Raoul and myself with failed slack invites that get generated as part of the `rake db:setup`.  I posted support requests to Heroku, which as of this morning have not been picked up and as of this morning I tried removing the `rake db:setup` completely, which allowed the app to report "successful" deployment, but when I looked there was just nothing there.

Despite being admin Raoul reported that he couldn't even see the develop server in the Heroku GUI, and had removed his account over night.  I re-added him, put the `rake db:setup` back in, adjusted the config to allow us to set slack invites to be disabled on develop, and tried another push.  I feel like I could burn a lot of time on this today.  The next sequence of things to try with this nasty delayed feedback loop are:

1) remove `rake db:setup` again - maybe I can then just run from command line to a) get a working instance and b) understand the error better
2) try the suggested `heroku-buildpack-addon-wait` from heroku
3) re-create the github web hook that is presumably lending Raoul's credentials to every attempt to deploy an instance for a pr

Conversely I could say that this is yak-shaving, and yak-shaving with a time delay is a real pain.  if I really wanted to get back on track to deploying the feature what I could be doing is just deploying a heroku instance from scratch and starting work on prodding at the feature, and getting my head back around the data migration we need so that I can write some manual instructions, or get a script together.  We will see.  Having blogged I guess I will now work through all the email and slack messages that are backing up, and look through/add-to my priority list to work out what's the most profitable way to spend the next few hours.  Yaks!