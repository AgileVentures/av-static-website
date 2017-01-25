There's lots I'd like to talk about.  Premium pairing on new non-profit IT support plans, [helping Kenyan students get started with LocalSupport](https://www.youtube.com/watch?v=P66oJafqqng), [meeting with the LocalSupport client](https://www.youtube.com/watch?v=zrz6ct9wIUs), deploying LocalSupport via the new drie push Github interface, mob programming on the Hash#fetch method, but I'm presenting my talk on "DevOps on a Budget" on Thursday, so things have got to be DevOps pricing focused :-)

I'm torn between focusing the presentation on the costings and the actual process of DevOps I'm doing on AV projects that I blogged about last week.  The talk summary is as follows:

> This talk will cover how we've been keeping our hosting costs down at the AgileVentures online IT and Education charity over the last 4 years, touching on how we've been using Heroku, AWS and other less well known services like NineFold and drie. In particular we'll look at how we manage DevOps in an ever changing team of volunteer participants. We'll also cover our migration from Heroku to Ninefold and back again when Ninefold went bust, and our most recent ongoing migration from Heroku to drie.

I guess the key thing is to get down some of that narrative about how our longest running project has been managed from a DevOps point of view over the years.  A quick trip to to Google Analytics showed me a slightly perplexing view of the traffic to the site over the last 2 years that we've been tracking:

![Harrow Community Network users](https://www.dropbox.com/s/zrhi3tit04i44s0/Screenshot%202017-01-16%2009.42.25.png?dl=1)

The site traffic was an order of magnitude heavier in 2015, and I can't really think of an explanation other than some sort of error relating to analytics of some service pinging the site that shut down.  Throughout 2016 we've had about 300 visitors a month, which seems about right.  I can't imagine why 2015 would have seen such high traffic.  In contrast WebSiteOne has been much more consistent:

![AgileVentures site users](https://www.dropbox.com/s/p5inkiun3j1993r/Screenshot%202017-01-16%2009.45.43.png?dl=1)

The extra challenge with the Harrow Community Network site has generally stemmed from needing https for logins to ensure they are secure.  Arguably this is an issue for every site, but the AgileVentures site handles login via GitHub and G+ which have their own security.  As a cash strapped NonProfit with clients who also have limited resources we were looking at ways of affording the extra fees for setting up SSL certificates.

Our Slack archive and meeting notes show me that we had got prototypes of the main site on Heroku in early February 2014.  It wasn't long before we'd realised that Heroku SSL suport would lead to a recurring monthly fee, and so the site was only very briefly on Heroku (3 months) before we'd switched to NineFold. 

We started investigating [NineFold](http://ninefold.com/) as an alternative to Heroku in April 2014, when a member of our community working for NineFold indicated we might be able to get a grant from NineFold to cover the hosting costs.  We went live on NineFold in May 2014, but it was less than a year before we were forced to switch back to Heroku due to NineFold sunsetting their Rails hosting solution in March 2015.  We'd gotten almost a year's free SSL support from NineFold, although that did inadvertently charge my credit card a couple of times.  Talking it through with them I got a refund.

I have emails from April 2015 about moving our data back from NineFold to Heroku, and getting SSL certificates set up.  At that point Heroku gave us a $100 grant towards the costs of the SSL certificates.  Importing the data back was problematic, and ultimately fixed by using a binary dump with schema AND data and resetting the db before doing the restore operation.

NineFold's command line interface had been slightly different to Heroku's and had taken a little getting used to.  The deployment was not too difficult, and we had a NineFold person giving us technical support directly in our Slack instance. There had been a fair amount of fiddling around to create valid SSL certificates through a third party provider. Overall it was an interesting experience to have migrated a site from one provider to another, which mainly involved being familiar with PostGres import and export tools.

Moving back to Heroku, the free SSL certificate provider I'd used for NineFold was no longer operating, and the client was now willing to take on some hosting costs.  At the time I was also just contributing to AgileVentures part time and so the Heroku Expedited SSL solution was very attractive, so we went for that and have been billing the client $35 a month since then.  Ironically SSL is now free on custom domains on Heroku, but to get off the limited hours free tier we need to pay $7 a month, and we need to keep the staging and develop instances running so we could switch over to the new dynos and take the costs down to $21 a month.

We're also looking seriously at moving to drie, who have a low cost hosting service called "drie push".  One of the advantages of drie is automatic availability of hosted versions of all the branches in one's git repo.  Early on we linked things up for HarrowCN like so:

* master branch --> https://www.harrowcn.org.uk
* staging branch --> http://staging.harrowcn.org.uk
* develop branch --> http://develop.harrowcn.org.uk

Having that kind of mapping automatically available from the start of the project is very appealing, but after the experience with NineFold we are cautious about moving to a new service provider.  However drie also has a tighter GitHub integration than Heroku allowing us to add drie as a GitHub integration and deploy directly by pushing to any repo branch.  Heroku can be set up to do this, but their build on PRs functionality creates more instances that we have to pay for.  drie has a different model that sleeps apps more aggressively and so their free tier goes further than that of Heroku.  We're also excited about being able to use GitHub's team interface system rather than Heroku's, since Heroku is also charging extra for the use of the team interface.

###Related Videos:

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=NiXjhjaTTCk)
* [LocalSupport Client Meeting](https://www.youtube.com/watch?v=zrz6ct9wIUs)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=ToV8wt9sr8w)
* [Saturday "Bob Martin" Scrum](https://www.youtube.com/watch?v=VOnol6QAaeg)
* [Sunday "Bob Martin" Scrum](https://www.youtube.com/watch?v=P66oJafqqng)


p.s. Full disclosure, AgileVentures has received financial grants from Heroku and NineFold in the past, and drie is an ongoing sponsor of the AgileVentures charity. 
