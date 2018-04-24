Exhausted.  Fun weekend in the lake district, but a fair amount of drinking meeting up with old friends.  Slow start yesterday, but got up earlier today and did my usual routine of jog, exercises etc. Computer locked up when I got to it.  Wasn't planning to blog as it feels like there's no time, but feeling seriously lethargic. Blog seemed least challenging of all of the many tasks waiting to be performed.  The particular challenge at the moment is the closed source nature of the private node project I'm working on at the moment.  It's reasonably interesting in as much as it is node and electron and involves Japanese translation, but since it's closed source I can't really blog about it, can't really get much synergy with everything else I'm doing for AgileVentures.  Am I happiest just tinkering away with the AV ecosystem?  I must complete at least this next chunk of the private project as I was paid up front and used the money to pay for this summer's trip to Japan.  Now there are bills for kids activities and instrument lessons coming up, as well as the need for a bit of spending money during the trip.  There's an offer of more work on the private project, but is it going to continue to be a battle each week between this closed source coding and what I really want to be doing?

Amazingly Nico has got us a completely new content model for the static pages on WebSiteOne (the agileventures.org site), which now allows us to submit pull requests and edit code at https://github.com/AgileVentures/AgileVentures/ and have all the markdown files imported into the AV site, stored as HTML and then displayed as necessary.  That allows me to get at least a couple of outstanding tasks done, specifically to update the [weekly contribution awards page](https://github.com/AgileVentures/AgileVentures/edit/master/WEEKLY_CONTRIBUTION_AWARDS.md) with the more recent two recipients:

```
- 22nd January 2018 --- [Nicolas Vidal](https://www.agileventures.org/users/nicolas-vidal) 
    - for contributions to [LocalSupport](https://www.agileventures.org/projects/localsupport/) and [WebSiteOne](https://www.agileventures.org/projects/websiteone/)
- 4th February 2018 --- [Ben Blanchard](https://www.agileventures.org/users/ben-blanchard) 
    - for contributions to [WikiEduDashboard](https://www.agileventures.org/projects/wiki-ed-dashboard) and [WebSiteOne](https://www.agileventures.org/projects/websiteone/)
- 22nd March 2018 --- [Brent Vardy](https://www.agileventures.org/users/nicolas-vidal) 
    - for contributions to [LocalSupport](https://www.agileventures.org/projects/localsupport/) and [AsyncVoter](https://www.agileventures.org/projects/asyncvoter/)
- 23rd April 2018 --- [Nick Schimek](https://www.agileventures.org/users/cfme00) 
    - for contributions to [LocalSupport](https://www.agileventures.org/projects/localsupport/) and [WebSiteOne](https://www.agileventures.org/projects/websiteone/)
```

I get distracted looking for Nick's user link.  I want to turn on Slack to look for the date of Brent's award.  I avoid turning it on so that I don't get too distracted.  Try email instead to look for Nick's email, but get distracted by that.  Eventually I use the Premium API and heroku Rails console to work out Nick's email and then his account page, and ensure he is added into the Premium members list.  Not much on his account page.  So much to improve and fix in the site.  One step at a time.  

As usual 20 minutes blogging has taken the edge off my lethargy, and I've even managed to get a couple of things done.  I guess I should run the rake task to check the changes on agileventures.org, which we run like this:

```
$ heroku run rake fetch_github:content_for_static_pages -r production
```

Funny, the difference between what I had to do before which was go to the heroku GUI and change the EDIT_PAGES variable to true and then edit with the Mercury editor.  Felt such a pain, and I didn't like working in Mercury.  Now I get to work in markdown, get previews in GitHub and run a command like the above.  Nico has some ideas about automatic triggers, which could be cool.  He's also got a pull request in to have all project documentation grabbed from GitHub as well.  Would be great to get that working - will need to talk to all the project managers about that I think - make sure no one loses important stuff in the main site that they won't be able to edit after we remove Mercury.

So I'm in Slack, getting distracted, but the page is all updated and I'm able to highlight Nick's award in the #av_community_channel, and I guess I go and get some coffee.  I'll drink that, try to work through my email, slack habit and then hopefully get a good solid hour of coding on the closed source project ...
